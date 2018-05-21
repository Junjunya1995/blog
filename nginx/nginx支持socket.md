###  nginx支持socket
WebSocket是HTML5出的一个持久化的协议。  
首先Websocket是基于HTTP协议的，或者说借用了HTTP的协议来完成一部分握手。在握手阶段是一样的。  
使用 WebSocket协议，服务端就可以主动推送信息给客户端。

nginx1.3 版本才支持WebSocket   [官方文档](http://nginx.org/en/docs/http/websocket.html)

````
server
{
    listen 80;
    server_name  xx.com;
    index index.html index.htm index.php default.html default.htm default.php;
    root  _;
    
    location /
    {
        
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        
        proxy_pass http://127.0.0.1:9501;
        include /etc/nginx/proxy.conf;
    }
    
    access_log off;
}
````

  