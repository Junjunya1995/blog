# Java 随笔

## Tomcat 等web容器

http 服务器收到请求后,静态文件直接返回，其他请求转发给web容器。

Web容器负责管理和运行Servlet
容器对Servlet的支持包括
- 通信支持
- 生命周期管理
- 多线程支持
-  JSP支持
- 处理安全性


------------------待验证----------------

`至于Spring的运行原理，个人猜测为 所有请求都发给一个Servlet，然后由这个 Servlet 
与Spring 进行结合，由Spring去处理剩下的操作。`




