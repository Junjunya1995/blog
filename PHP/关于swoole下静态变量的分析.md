# 关于swoole下静态变量的分析 

转自  [关于swoole 下静态变量的分析 · Issue #41 · top-think/think-swoole](https://github.com/top-think/think-swoole/issues/41)


静态变量在传统php开发中很好用，但是在swoole等常驻内存模式下很容易造成bug  
下面是一些thinkphp中比较重要的静态变量，大部分都有可能在swoole模式下造成bug

\think\Model::$initialized;  
//原本作用：确保一个模型类中的init方法在一次请求中只被执行一次  
//常驻内存下：一个模型只在第一次请求时执行该方法，后续请求不再执行，极有可能会造成bug

\think\Model::$readMaster;  
//原本作用：设置某一个或者全部模型是否从主库读取数据  
//常驻内存下：如果在一个请求执行了Model::readMaster()方法，\think\Model::$readMaster不会释放，将会影响到其他请求


think\model\concern\ModelEvent::$event;  
//原本作用：给模型设置的事件回调  
//常驻内存下：随着运行时间不断增加将不断增加运行内存，一次请求增加的事件将影响到另外一次请求


think\model\concern\ModelEvent::$observe;  
//没有影响

think\db\Query::$readMaster;  
//原本作用：设置某一个或者全部模型是否从主库读取数据  
//常驻内存下：如果在一个请求执行了Query::readMaster()方法，Query::$readMaster不会释放，将会影响到其他请求

think\db\Query::$event;  
//常驻内存下：一次请求增加的事件将影响到另外一次请求

think\db\Query::$extend;  
//扩展查询方法  
//几乎没有影响 

think\Request::$ip;  
//协程下会用bug 常驻内存非协程下没有影响  

think\Template::$_varFunctionList;  
//常驻内存模式下增加的变量无法释放，造成内存不断增加，模板变量冲突 可能造成bug

think\cache\Driver::$serialize;  
//常住内存模式下 一次请求调用registerSerialize()方法后，会对下一次请求有影响

think\Validate::$typeMsg;  
//应该基本没有啥影响

think\db\Connection::$instance;  
//建立的连接管理实例
//协程模式，高并发下可能会导致数据库操作bug

think\db\Connection::$event;  
//连接事件
//随着运行时间不断增加将不断增加运行内存，事件重复执行，一次请求增加的事件将影响到另外一次请求

think\db\Connection::$info;  
//数据表信息  
//应该没事影响   

think\Db::$executeTimes;  
//常驻内存下实际是全局执行时间

think\Db::$queryTimes;  
//常驻内存下实际是全局执行次数

think\Db::$connection;  
//协程模式，高并发下可能会导致数据库操作bug  

