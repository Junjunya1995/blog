## php_network_getaddresses

转载

作者：痞子达
链接：https://www.jianshu.com/p/60307f133cb3

### 背景
最近遇到一个问题，很烦，真的很烦。无缘无故在你吃麻辣烫的时候有台机器就告警，突然它用不了。当然，幸好我们提供的服务集群，一个干不了活，可以用其他来干。但即使这样，我还是很烦告警。

收到的告警是：Unknown: php_network_getaddresses: getaddrinfo failed: Name or service not known

这个提示很熟悉，显然是DNS解析出现问题了。

### 检查
业务中很多地方用到域名访问API，尝试取一个redis域名测试，用www.test.com代替实际域名。

dig排查下问题：

    dig www.test.com
    dig: isc_socket_bind: address in use
域名解析www.test.com出现问题

直接dig一下：

    dig
    dig: isc_socket_bind: address in use
域名解析有问题，但是该dns服务在其他机器上没有问题。

使用tcp协议查询(dig默认是使用udp)：

    dig www.test.com +tcp
    ; <<>> DiG 9.9.4-RedHat-9.9.4-29.el7_2.2 <<>> www.test.com +tcp
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1269
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;www.test.com.      IN      A

    ;; ANSWER SECTION:
    www.test.com. 6     IN      A       client_ip

    ;; AUTHORITY SECTION:
    www.test.com. 6     IN      NS      www.test.com.

    ;; Query time: 2 msec
    ;; SERVER: ip#port(ip)
    ;; WHEN: Wed Aug 30 20:47:28 CST 2017
    ;; MSG SIZE  rcvd: 83
tcp出去是ok的。说明udp有点小问题

telnet测试redis服务：

    Trying ip...
    Connected to ip.
    Escape character is '^]'.
说明redis服务没问题。

ping测试，提示：

    connect: Resource temporarily unavailable
我ping了在其他机器上可以ping通的域名，但这台机器出现该提示，我找了资料，Linux文档提示服务器监听队列已满后，客户端会报连接拒绝错，就可能出现该错误。所以dns解析过程会不会也是因为这个问题引起的呢？

上面一系列检查，得出假设：很有可能是udp连接出现问题，可能是服务器端口占用过多，导致域名解析失败，因为域名走udp协议。

进一步验证
    ss -anul|wc -l
    28237
这个参数确实有点多。

再看下udp端口范围：

    cat /proc/sys/net/ipv4/ip_local_port_range
    32768   61000
61000 - 32768 = 28232 < 28237
果然端口号不够用，所以造成之前的问题。

看看是什么东西占用大量的UDP端口号：

    ss -anulp|head -n50
    UNCONN     0      0            *:36756       *:*   users:(("php-fpm",pid=16546,fd=72))
    UNCONN     0      0            *:36757       *:*   users:(("php-fpm",pid=16173,fd=54))
    UNCONN     0      0            *:36758       *:*   users:(("php-fpm",pid=16307,fd=60))
    UNCONN     0      0            *:36759       *:*   users:(("php-fpm",pid=16414,fd=65))
    UNCONN     0      0            *:36760       *:*  users:(("php-fpm",pid=16093,fd=69))
原来是php-fpm！！作为世界上最好的语言怎么能出现这种问题呢！！好气啊！

### 追查问题根源：
是不是php-fpm配置问题
那么问题来了？为何php-fpm会拉起这么多的udp连接，我很是不解。脚本程序中没有使用udp的地方呀？一开始我以为是php-fpm配置有问题，害我把php-fpm的对子进程的管理方式以及相关配置改了个遍，验证一段时间后还是出现这个问题。

### 是不是nginx配置问题
所以不是php-fpm配置的问题，那会不会nginx配置问题，但理论上nginx只做一个转发，不会造成这种现象。对nginx的参数进行调优测试，也是用默认的nginx配置测试，在一段时间后，服务器还是会报错。

### 是不是程序的问题
既然niginx和php-fpm都没有问题，那么会不会程序出问题了。
我使用了的业务接口 ，假设www.a.com/api ，并且创建了一个纯净的www.a.com/test.php. (脚本里面就一个echo)

访问www.a.com/test.php，查看php-fpm拉起的UDP端口数：

    ss -anup|grep php-fpm|wc -l
    0
是0啊，好像没有问题。

访问www.a.com/api:

    ss -anup|grep php-fpm|wc -l
    1
再访问一次www.a.com/api:

    ss -anup|grep php-fpm|wc -l
    2
再访问一次！

    ss -anup|grep php-fpm|wc -l
    3
我擦，问题就在这里！！
于是迅速定位了这个api，查到了某个可疑的扩展，是一个第三方扩展，将其屏蔽，再次访问，没有占用任何udp端口。

我重新改写和编译了该扩展，这个问题就得到解决，一个php-fpm子进程最多占用一个udp端口（之前的一个php-fpm会占用几十乃至更多的端口，造成端口不够用）。

总结
表象：PHP-FPM拉起过多的UDP端口，一直占用，导致dns无法解析，而业务中需要访问域名，所以出现了问题。

根源：第三方扩展使用了UDP，会不停地创建线程，api是不会回收相关线程的连接句柄。导致UDP端口过多，导致UDP在机器上的其他服务不能用。

解决方法：第三方扩展的不停创建线程和不释放句柄修改掉。不放心的同学可以写一个crontab，查询UDP session，如果超多一定数量（用ss，会币netstat快一些），那就平滑重启php-fpm，隔一段时间执行。

tips：如果访问量不是很大，php-fpm.conf配置中可以使用pm=ondemand模式拉起子进程，其中的pm.process_idle_timeout = 10s；设置会在空闲的时候，会平滑关闭worker，这样你的UDP session也会因此减少，这当然只是个辅助方案。


