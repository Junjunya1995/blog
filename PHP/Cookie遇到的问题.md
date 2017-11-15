记录一下 遇到个某个Cookie问题吧  平时一直用session 有点忽略cookie了

前几天公司X部门经理 发过一套源码来 说给部署上，研究一下

部署完成后 却发现登入不上。 

初步最原始的 ``var_dump()``+``exit`` 调试下 发现是cookie 存不上

感觉很奇怪 因为其他项目是没有问题的，并且session 也是基于cookie实现的 

如果cookie 有问题 session 应该也有问题。

然后就用 PHPstorm 开启debug 模式调试了 

发现原来是 系统某处配置 cookie 的域名 导致我在本地环境下没法存储cookie 

没错 就是domain这个参数


#### 插播一下 setcookie的用法

```php
setcookie(name,value,expire,path,domain,secure)
```

参数|描述
-|-
name|必需。规定 cookie 的名称。
value|必需。规定 cookie 的值。
expire|可选。规定 cookie 的有效期。
path|可选。规定 cookie 的服务器路径。
domain|可选。规定 cookie 的域名。
secure|可选。规定是否通过安全的 HTTPS 连接来传输 cookie。


