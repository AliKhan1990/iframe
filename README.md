# iframe
@Snandy
### 完全跨域的iframe高度自适应

分别有以下资源

    页面 A：http://snandy.github.io/lib/iframe/A.html
    页面 B：http://snandy.github.io/lib/iframe/B.html
    页面 C：http://snandy.jd-app.com
    D.js：http://snandy.github.io/lib/iframe/D.js

这四个资源有如下关系

1. A里嵌入C，A和C是不同域的，即跨域iframe

2. C里嵌入B，C和B是不同域的，但A和B是同域的

3. C里嵌入D.js，D.js放在和A同域的项目里

 

通过一个间接方式实现，即通过一个隐藏的B.html来实现高度自适应。

A.html

嵌入页面C: http://snandy.jd-app.com 

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8' />
    <title>A.html</title>
  </head>
  <body>
    <iframe id="ifr" src="http://snandy.jd-app.com" frameborder="0" width="100%"></iframe>
  </body>
</html>


```


 

B.html

嵌入在C页面中，它是隐藏的，通过parent.parent访问到A，再改变A的iframe(C.html)高度，这是最关键的，因为A，B是同域的所以可以访问A的文档对象等。
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8' />
    <title>B.html</title>
  </head>
  <body>
    <script type="text/javascript">
        window.onload = function() {
            var isSet = false
            var inteval = setInterval(function() {
                var search = location.search.replace('?', '')
                if (isSet) {
                    clearInterval(inteval)
                    return  
                }
                if (search) {
                    var height = search.split('=')[1]
                    var doc = parent.parent.document
                    var ifr = doc.getElementById('ifr')
                    ifr.style.height = height + 'px'
                    isSet = true
                }
            }, 500)
        }
    </script>
  </body>
</html>
```
>search() 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。

>split() 方法用于把一个字符串分割成字符串数组。
 

C.html

嵌入在A中，和A不同域，要实现C的自适应，C多高则A里的iframe就设为多高。C里嵌入B.html 和 D.js
```
	
<!doctype html>
<html>
<head>
    <title>C.html</title>
    <meta charset="utf-8">
</head>
<body>
    <h3>这是一个很长的页面，我要做跨域iframe的高度自适应</h3>
    <ul>
        <li>页面 A：http://snandy.github.io/lib/iframe/A.html</li>
        <li>页面 B：http://snandy.github.io/lib/iframe/B.html</li>
        <li>页面 C：http://snandy.jd-app.com</li>
        <li>D.js：http://snandy.github.io/lib/iframe/D.js</li>
    </ul>
    <p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p><p>A</p>
    <iframe id="myifr" style="display:none" src="http://snandy.github.io/lib/iframe/B.html"></iframe>
    <script type="text/javascript" src="http://snandy.github.io/lib/iframe/D.js"></script>
</body>
</html>

```

D.js

在页面C载入后计算其高度，然后将计算出的height赋值给C里引入的iframe（B.html）的src
```
	
// 计算页面的实际高度，iframe自适应会用到
function calcPageHeight(doc) {
    var cHeight = Math.max(doc.body.clientHeight, doc.documentElement.clientHeight)
    var sHeight = Math.max(doc.body.scrollHeight, doc.documentElement.scrollHeight)
    var height  = Math.max(cHeight, sHeight)
    return height
}
window.onload = function() {
    var doc = document
    var height = calcPageHeight(doc)
    var myifr = doc.getElementById('myifr')
    if (myifr) {
        myifr.src = 'http://snandy.github.io/lib/iframe/B.html?height=' + height
        // console.log(doc.documentElement.scrollHeight)     
    }
};
```
