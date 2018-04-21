### socket.io 

因为平时一直在公司宿舍住着，前几天的时候晚上11点，公司技术老总突然到来，
说XXX交给你个任务，配合X经理基于node搭建一个socket服务。今晚就要弄好明天早上就要用。   

而我则一脸懵逼，啥? node? 我不是PHP么? 让我swoole搞个还差不多，go也凑合。
再不济Java百度百度应该也能写出来。心中思绪万千，在思考啥时候暴露了我还会 node 

X经理则过来和我说一下业务逻辑，以及要搭建什么样的服务。

socket.io 当时我第一次听说，就按照官网demo做了个例子（此处应有捂脸表情，没想到直接拿demo就用在项目上了）

demo 如下

- Server (app.js)
````javascript
var http = require('http'), 
  app = http.createServer(handler),    
  io = require('socket.io').listen(app),  
  connArr = {}
 
app.listen(8081);  
  
function handler (req, res) {   
  console.log(req.url);
  if(req.url.indexOf('/java') > -1) {

    let  xjh = getPar(req.url, 'xjh')
    if(connArr[xjh])  {
      connArr[xjh].val.emit('message', getPar(req.url, 'status'));
      connArr[xjh].val.disconnect(true)
      delete connArr[xjh]
    } 
    res.writeHead(200, {'Content-Type': 'text/html'});      
    res.end('');
  } 
}  
  
io.sockets.on('connection', function (socket) {  
    let xjh ;
    socket.on('xjh', function (data) {  
      xjh = data.xjh
        if(!connArr[xjh])  {
          connArr[xjh] =  {id: xjh ,val:socket, status :0}
        }
  }); 
  socket.on('disconnect', function (socket) {
     delete connArr[xjh]
     console.log('disconnect');
  });
});  

function getPar(url,par){
    //获取当前URL
    var local_url = url; 
    //获取要取得的get参数位置
    var get = local_url.indexOf(par +"=");
    if(get == -1){
        return false;   
    }   
    //截取字符串
    var get_par = local_url.slice(par.length + get + 1);    
    //判断截取后的字符串是否还有其他get参数
    var nextPar = get_par.indexOf("&");
    if(nextPar != -1){
        get_par = get_par.slice(0, nextPar);
    }
    return get_par;
}
````

很顺利的就搭建好了 socket的服务端。然后就想着利用swoole 也来写下这个逻辑。
没想到自己服务器上用了nginx转发socket 遇到了问题。最后怎么解决的 下次再说
