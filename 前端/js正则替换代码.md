#  正则替换

### 前情介绍

最近一直做前端的项目， 由于公司还没有使用vue 等技术进行开发  
所以，调取接口数据，然后渲染页面是个很头疼的工作。  

所以自己研究了下，配合 template 实现了简易的前端模板引擎

代码只有一句


```html
<div id = "content"></div> 
<script type="text/template" id="tpl">
    <div>[name]</div> 
</script>
```

```javascript
<script>
var tpl  = document.getElementById('tpl').innerHTML
var html = tpl.replace(/\[(\w*)\]/gm, function(node, key) { 
    return { 
        name : 'OK啦'
    }[key]
}) 
document.getElementById('content').innerHTML = html
</script>
```

[js正则模式修饰符详解](js正则模式修饰符.md)
