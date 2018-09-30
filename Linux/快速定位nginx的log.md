## Linxu下，快速定位nginx的log文件的位置

* 1  从当前全部进程中筛选出nginx进程 
```base
    ps aux | grep nginx     
```
* 2 得到nginx配置文件的位置。  
如果在进程信息中未显示配置文件位置，则说明在默认位置`/etc/nginx/nginx.conf` 
如果配置了`vhost` 可在`nginx.conf`文件确认`vhost文件夹`的位置，然后打开`vhost文件夹`

* 3 确定aa.com网址的vhost配置文件 
```base
    grep  -r  aa.com  ./     
```

* 4 确定日志文件位置

```base
    cat aa.com.conf  | grep log     
```
