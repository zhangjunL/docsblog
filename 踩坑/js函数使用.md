# js函数使用
## setInterval
```js
function show(){ 
trace("每隔一秒我就会显示一次"); 
} 
var sh; 
sh=setInterval(show,1000); 
clearInterval(sh); 
```