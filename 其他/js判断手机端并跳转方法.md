# js判断手机端并跳转方法

 js判断手机端并跳转，这段代码最好放到head里面，这样加在是能够及时的跳转 。

```js
<script>    
function is_mobile() {
var regex_match = /(nokia|iphone|android|motorola|^mot-|softbank|foma|docomo|kddi|up.browser|up.link|htc|dopod|blazer|netfront|helio|hosin|huawei|novarra|CoolPad|webos|techfaith|palmsource|blackberry|alcatel|amoi|ktouch|nexian|samsung|^sam-|s[cg]h|^lge|ericsson|philips|sagem|wellcom|bunjalloo|maui|symbian|smartphone|midp|wap|phone|windows ce|iemobile|^spice|^bird|^zte-|longcos|pantech|gionee|^sie-|portalmmm|jigs browser|hiptop|^benq|haier|^lct|operas*mobi|opera*mini|320x320|240x320|176x220)/i;    
var u = navigator.userAgent;    
if (null == u) {    
return true;    
}    
var result = regex_match.exec(u);    
if (null == result) {    
return false    
} else {    
return true    
}    
}    
if (is_mobile()) { //  跳转到手机端网站  
document.location.href= 'http://m.baidu.com';    
}    
</script>
```

 如果需要从手机端跳转到PC端的话可采用 

```js
< script >
function is_mobile() {
    var regex_match = /(nokia|iphone|android|motorola|^mot-|softbank|foma|docomo|kddi|up.browser|up.link|htc|dopod|blazer|netfront|helio|hosin|huawei|novarra|CoolPad|webos|techfaith|palmsource|blackberry|alcatel|amoi|ktouch|nexian|samsung|^sam-|s[cg]h|^lge|ericsson|philips|sagem|wellcom|bunjalloo|maui|symbian|smartphone|midp|wap|phone|windows ce|iemobile|^spice|^bird|^zte-|longcos|pantech|gionee|^sie-|portalmmm|jigs browser|hiptop|^benq|haier|^lct|operas*mobi|opera*mini|320x320|240x320|176x220)/i;
    var u = navigator.userAgent;
    if (null == u) {
        return true;
    }
    var result = regex_match.exec(u);
    if (null == result) {
        return false
    } else {
        return true
    }
}
if (!is_mobile()) { //  跳转到PC端 
    document.location.href = 'http://www.baidu.com';
} < /script>
```

