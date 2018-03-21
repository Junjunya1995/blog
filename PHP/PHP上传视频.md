##PHP上传视频 遇到的问题

###前请介绍 

最近维护老项目比较多。昨天让排查一个上传视频上传不上去的问题。

* 首先思路是服务器上  PHP上传文件大小给限制了。

查看后，发现果然是这样
 
找到 php.ini 文件 ， 修改 upload_max_filesize、post_max_size的值

```upload_max_filesize = 8m ;望文生意，即允许上传文件大小的最大值。默认为2M```

```post_max_size = 8m ;指通过表单POST给PHP的所能接收的最大值，包括表单里的所有值。默认为8M```

然后重启服务器  OK 问题解决 可以上传。

----------

*  但是事情还没有结束，上传的mp4 在html中<video>标签中无法播放。经搜索后， 发现原来和MP4格式有关系 



 MP4有4种编码，MPEG4(DivX)，MPEG4(Xvid)，AVC(H264)， HEVC(H265)，用工具转换成H264编码就可以网页正常播放了
 
 * 转码后 发现还不能播放 发现video标签中  type值是 ```application/octet-stream```
 
 为什么会这样呢。再次上传视频并抓包
 
  ![抓包图片](杂项/img/zbtp.png)
  
  发现了 Content-Type : application/octet-stream
  
  然后php 接收的时候 打印 ```print_r($_FILES)```
  发现文件type 也是application/octet-stream
  
  查阅资料后 了解到 上传的时候 application/octet-stream 表明是使用了二进制流的方式上传，可以上穿多种文件格式
  
  PHP需要开启 fileinfo扩展 就可以自己判断文件类型
  
  查看服务器 果然没有开启 添加好扩展后 再次实验。  成功 问题解决
 

   

    