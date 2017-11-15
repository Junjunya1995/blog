## 下载Xdebug扩展  
* 下载地址: [Xdebug](https://xdebug.org/download.php)

选择好对应版本的Xdebug扩展 进行下载,因为是国外网站可能访问会有点慢。

## 安装扩展
放在对应PHP目录下的ext文件夹中，然后修改php.ini文件
增加下面一段话

`````
[Xdebug]
#必须以zend_extension 开头  经测试php7可以 以extension  形式引入扩展  
zend_extension="c:/php/ext/php_xdebug-2.1.0-5.3-vc6.dll"  
xdebug.remote_enable = On
xdebug.remote_handler = dbgp
xdebug.remote_host= localhost
xdebug.remote_port = 9000
xdebug.idekey = PHPSTORM     #用phpstorm调试时设置

`````

然后重启  apache 查看phpinfo();  若有xdebug 说明安装成功
