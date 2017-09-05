## 第一步  找到源码包

以编译fileinfo扩展为例（注意这是php内置的扩展，如果你编译的是第三方扩展请自行到官网下载源码） 

假设源码包位置为 /home/php  
```
cd  /home/php/ext/fileinfo
```



## 第二步  编译

假设已经安装的PHP 目录为  /usr/local/php

```
/usr/local/php/bin/phpize

./configure --with-php-config=/usr/local/php/bin/php-config

make && make install
```

首先是执行phpize，它是用来安装php扩展的工具，如果你的系统无法识别，请使用完整路径。比如/usr/local/php/bin/phpize 。这个工具会在当前目录下生成configure文件。
 

接着就是编译的三板斧啦~其中with-php-config参数请按照你实际情况填写

没有意外的话，经过上面的编译，你会在php/lib/php/extensions/(在php.ini的extension_dir可找到)目录下找到生成的fileinfo.so文件



## 第三步 添加配置

最后只需要在php.ini里把扩展添加进去就好了
```
extension = "fileinfo.so"
```

重启服务器
 
