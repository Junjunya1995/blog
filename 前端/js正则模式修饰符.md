## js正则表达式修饰符

 
 
 参数|含义|结果
 :------:|:-----:| :----: 
 i|忽略大小写	|如果不写，默认的是区分大小写的
 g|	全局匹配	|如果不写，结果是只要匹配到一个就返回
 m|	多行匹配	|如果不写，则只是匹配一行
 
 `i` `g` 比较好理解就不详细说明了，重点说下`m`
 
 `m` 表示多行匹配。如果字符串只有1行，不含有换行符`\n` ， `/m`是没有意义的。  
 并且 正则表达式中含有`^`或`$`的时候，`/m`修饰符才有作用 
  
 例如 
 ````javascript
var mutiline = /^abc/m;
var singleline = /^abc/;
var target = "ef\r\nabcd";
alert(mutiline.test(target));//true
alert(singleline.test(target));//false
````
如上可以看到 `singleline`的正则匹配的是 以`abc`为开头的字符，没有`/m`的情况下  
无法匹配到第二行(`\n`表示换行，所以是第二行)的`abc`。  但是加了`/m`的情况下就可以匹配到了
 