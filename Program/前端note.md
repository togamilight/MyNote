

# Tip

### label与radio(checkbox)
* html中，单选框和其文本最好用label标签包起来，这样点击文本可以选中该项(多选框也一样)
```html
<label><input type="radio" name="name"> text</label>
<!--或者这样-->
<input type="radio" id="myRadio" name="name"> <label for="myRadio">text</label>
```

### a标签的锚点
* a标签的href的`#id`形式是表示跳转到页面中的锚点，即跳转到该id的元素处，如果该元素是a标签，可以是name属性(在H5中已废弃)
```html
<a href="#target">跳转到target</a>
<!--这样-->
<div id="target">target1</div>
<!--或者-->
<a name="target">target2</a>
```
* `#`表示跳转到页面顶部，等同于`#top`
* 如果想让a标签不跳转，可以用`href="#任何不存在的id"`，或者`href="javascript:void(0);"`，或者`href="javascript:;"`， 或者在点击事件中阻止浏览器默认行为：`return false;`或者`event.preventDefault();`

### onclick属性阻止冒泡
* 在使用普通的事件绑定时，可以用`return false;`或`event.stopPropagation();`来阻止冒泡
* 但是当使用标签的`onclick`属性绑定事件时，无法使用上述方式，要改为如下方法：
```js
<button onclick="doClick();">点击</button>

function doClick(){
  ...
  cancelBubble();
}
//阻止冒泡
function cancelBubble(e) {
  var evt = e || window.event;
  if (evt.stopPropagation) {          //W3C
    evt.stopPropagation();
  }else {                             //IE
    evt.cancelBubble = true;
  }
}
//类似的，阻止浏览器默认行为的代码
function stopDefault(e) {
  var evt = e || window.event;
  if (evt.preventDefault) {          //W3C
    evt.preventDefault();
  }else {                             //IE
    evt.returnValue  = false;
  }
}
```

### return false 与 阻止冒泡、阻止默认行为
* 在事件函数中，使用`event.stopPropagation()`可以阻止事件冒泡，使用`event.preventDefault()`可以阻止浏览器默认行为。
* 使用`return false`则可以同时达到两者的效果（**在IE中同样可用！**）

### jQuery的奇偶选择器
jQuery可以直接使用 `:odd/:even` 的形式来选择奇偶元素（CSS中为 `:nth-child(odd/even)`），不过jQuery的索引是从0开始的，所以奇偶和CSS的相反

### select标签
`select` 标签有 `selectedIndex` 属性，默认为0，即选中第一项，通过 `value` 属性给 `select` 赋值会更新 `selectedIndex`，如果是赋不存在于 `option` 列表的值，则 `value` 会变成 `null`，`selectedIndex` 则是-1。在IE10以下，`select` 没有默认值，除非在 `option` 添加属性 `selected="selected"`，或者将 `select`的`selectedIndex` 设为0。

### CSS控制换行

#### white-space

`white-space` 设置如何处理元素中的空白：
* `normal`: 合并连续的空白符（包括换行符），自动换行
* `nowrap`: 合并连续的空白符（包括换行符），不换行
* `pre`: 保留连续空白符，换行符换行，不自动换行
* `pre-wrap`: 保留连续空白符，换行符换行，自动换行
* `pre-line`: 合并连续空白符，换行符换行，自动换行

#### overflow-wrap(word-wrap)

两种等效，设置能否分割单词
* `normal`: 只在单词结束时换行，若下个单词会溢出，则将其移到下一行；若一个单词大于一行，让其溢出
* `break-word`: 若下个单词会溢出，则将其移到下一行；若一个单词大于一行，强制换行，分割单词

#### word-break

设置如何分割单词，应该是上面的加强版？
* `normal`: 只在单词结束时换行，若下个单词会溢出，则将其移到下一行；若一个单词大于一行,让其溢出。对于CJK文本，可在任意字符间断行。
* `break-all`: 强制换行，可在任意位置断开单词
* `keep-all`: CJK 文本不断行。 Non-CJK 文本表现同`normal`。
* `break-word`: 若下个单词会溢出，则将其移到下一行；若一个单词大于一行，强制换行，分割单词
CJK 指中文/日文/韩文

#### word-spacing

设置空格的长度: normal或长度

#### text-overflow

设置文本溢出时如何处理，一般和 `white-space: nowrap`搭配使用。应该和 `overflow: hidden` 一起使用
* `clip`: 默认，在容器边界处截断，可能截断字符的某个部分
* `ellipsis`: 显示省略号
* `clip`: 显示点 .
* `"XXX"`: 显示自定义字符串
* `fade`: 淡出效果，边缘完全透明

### IE中的事件

在IE8及以下，对于事件`event`参数的处理与众不同，如下：
```javascript
element.onclick = function(event){
  var eve = event || window.event; //获取事件对象
  var objEle = eve.target || eve.srcElement; //获取被点击元素的引用
}
```

### IE浏览器实现遮罩

IE8不支持`fixed`,这里使用`absolute`,IE8不支持`rgba`，要使用IE特有的滤镜实现透明背景
```CSS
.mask {
  position: fixed;
  position: absolute\9;   /*兼容IE8*/
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  width: 100%\9;    /*兼容IE8，IE8下设置四个方向的位置好像不能是其铺满容器，要设置宽高才行*/
  height: 100%\9;
  /*background: rgb(144, 144, 144);*/ /*兼容不支持rgba的浏览器，但是会覆盖滤镜*/
  background-color: rgba(0,0,0,0.3);
  filter: progid:DXImageTransform.Microsoft.gradient(startColorstr=#4c000000,endColorstr=#4c000000)\9; /*兼容IE8*/
  z-index: 999;
}
```
IE8设置了遮罩层之后，如果没有背景或背景透明（包括用滤镜），点击不会触发，而是直接穿过遮罩层，点击到其下的元素，把遮罩元素换为`iframe`可解决。
滤镜颜色参数的前两位是透明度，与`rgba`中的透明度的值对应如下：

|rgba   | 0.1 | 0.2 | 0.3 | 0.4 | 0.5 | 0.6 | 0.7 | 0.8 | 0.9 | 
|-------|-----|-----|-----|-----|-----|-----|-----|-----|-----| 
|filter | 19  | 33  | 4C  | 66  | 7F  | 99  | B2  | C8  | E5  |


### CSS hack
...

### js刷新页面

* `window.location.reload()`: 相当于点击浏览器的刷新按钮；
* `window.location = window.location`: 相当于在地址栏重新输入网址

### display: inline-block 产生间隙

当我们想要将多个块置于一行又不想用浮动时，可以使用 `display: inline-block`（其实也可以用弹性盒子 `display: flex`），如：
```html
<style>
  ul>li{ display: inline-block; }
</style>
<ul>
  <li>a</li>
  <li>a</li>
</ul>
```
但是这样每个块之间会留下一个空格的间隙，这是因为 HTML 代码中的换行符导致的，解决方法如下：  
1. 改变 HTML 代码的书写方式，比较僵硬，不推荐
   ```HTML
   <ul>
    <!--不换行-->
    <li>a</li><li>a</li>...
    <!--闭合标签写到下一行-->
    <li>a
    </li><li>a
    </li>...
    <!--不闭合标签-->
    <li>a
    <li>a
   </ul>
   ```
2. 给父元素设置 `font-size: 0;`，但是必须给子元素再设置字体大小，~~比较蛋疼，不推荐~~，比其它两种稳定，最终选择了这种
3. 设置负外边距，因为空格占半个字体大小，只要给子元素设置四分之一大小的负外边距就可以了：`margin: 0 -0.25em;`，~~推荐~~，不同字体下空格的大小不一样，比如宋体是二分之一，微软雅黑好像是三分之一，也挺蛋疼

### 自定义上传文件控件样式

思路是将上传文件 input 设为绝对定位，并设为透明，铺满父元素，溢出部分隐藏，然后在父元素上写样式：
```HTML
<span class="my-file-select">选择附件<input type="file"/></span>
```
```CSS
        .my-file-select{
            position: relative;    
            display: inline-block;
            overflow: hidden;
            color:#2B6997;
            text-decoration:underline;
            height: 35px;
        }
        .my-file-select>input{
            height: 100%;
            position:absolute;   
            right: 0;
            top: 0;
            outline: none;
            z-index: 10;
            opacity: 0;
            filter: alpha(opacity=0); /*IE版的透明*/
            cursor: pointer;
        }
```

### 在 IE8 下上传文件的傻逼之处

在 IE8 下异步上传文件，由于用不了 FormData()，使用了 jQuery.form.js 插件，有一万个坑。
1. 必须添加的 meta 标签：
  ```HTML
  <!--使文件名能支持中文，基本必有的（在IE8下好像不能使文件名支持中文，日）-->
  <meta http-equiv="Content-Type" content="text/html;charset=utf-8" />
  <!--没有这个，IE8 下文件不会发送。。。-->
  <meta content="always" name="referrer" />
  <!--可选，让你在各种国产浏览器的兼容模式中自动采用最新版本的 IE，远离傻逼 IE8-->
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  ``` 
2. 必须将文件 input 嵌套在 form 中，而不能这样：
  ```JavaScript
  //jQuery 的 clone() 在 IE8 下不会同时复制选中的文件
  $form.append($file.clone());
  ```
3. form 必须有 `method='post'`，input 必须有 name 和 value（value 可以是空字符串），莫名其妙
4. 必须设置 `dataType: "text"`，后端接口返回 `content-type: text/html`（.NET MVC 中返回 Content(value)），前端接收到的是文本，自行转换成 json
5. 上传完后清空 input，无法用普通的 `$file.val("")`，可以用简单的 `$form[0].reset()`，或者不嫌麻烦也可以将原来的 input 删掉加一个一样的（clone() 一下之类的）
6. IE8 下中文文件名会乱码，应该讲文件进行 URL 编码，再作为额外的参数传过去
例子：
```HTML
<form method="post">
  <input name="uploadFile" type="file" value=""/>
</form>
```
```JavaScript
//上传附件
$filePanel.on("change", "input[type='file']", function() {
    var $file = $(this);
    var $form = $file.parent();
    var fileName = $file.val();
    if (!fileName) { return; }

    $file.prop("readonly", true);
    $form.ajaxSubmit({
        data: { 
          fileName: encodeURIComponent(fileName), ... /*额外的数据*/
        },
        success: function(res) {
            var data = jQuery.parseJSON(res);
            ...
            $file.prop("readonly", false);
            $form[0].reset();
        },
        url: URL,
        type: "post",
        dataType: "text", /*设置返回值类型为文本*/
        contentType: "multipart/form-data;charset=utf-8" /*设置字符集为 utf-8 才可以支持中文文件名*/
    });
});
```

### 跨域 Ajax 支持 Session

Session 基于 Cookie 运作，而 Ajax 默认是不传 Cookie 的，所以要进行设置
1. 设置 Ajax 支持 Cookie：`xhrFields: {withCredentials: true}`，也可以进行全局设置：
    ```JavaScript
    $.ajaxPrefilter(function( options, originalOptions, jqXHR ) {
        options.xhrFields = { withCredentials: true }
    });
    //或  
    $.ajaxSetup({crossDomain: true, xhrFields: {withCredentials: true}});
    ```
2. 在服务器设置返回头
    ```CSharp
            //设置ajax可以携带、设置cookie
            filterContext.HttpContext.Response.AddHeader("Access-Control-Allow-Credentials", "true");
            //这里不能设置 "*"，要写明发起 Ajax 请求的网站的域名，可从请求头的 Origin 属性获得，但 IE8 发出的请求没有这个属性，需要自己写明
            filterContext.HttpContext.Response.AddHeader("Access-Control-Allow-Origin", "origin");    
    ```

### escape 和 encodeURI

escape 对 0-255 以外的 unicode 值（比如中文）进行编码时输出 %u**** 格式，其余与 encodeURI 基本一致；  
encodeURI 将所有特殊字符都转换成 utf-8 格式的 url 编码；  
ASP.NET MVC 在接收参数时，会自动对参数进行一次相当于 js 的 decodeURI 的操作，而调用 Server.UrlDecode(string) 或 HttpUtility.UrlDecode(string) 方法，则相当于先进行 js 的 decodeURI 操作，再对 %u**** 格式字符进行 js 的 unescape 操作

### jQuery Deferred

#### then

$.Deferred().then() 相当于 done()、 fail()、 progress() 的合体，可绑定这三种情况的回调函数（也可只绑定一两种），但其最大的作用是，若回调函数返回 Deferred 对象，则 then() 返回的也是 Deferred 对象，即可继续进行延迟操作
```js
//loadSelects 和 loadPageData 返回 ajax 对象
loadSelects().then(loadPageData).then(function(){...});
```

### IOS H5问题

#### 软键盘导致留白

IOS 的软键盘在弹出后会将页面顶上去，收起时页面又不会弹回来，导致页面错误，下方出现一块空白，解决方法是在输入框失去焦点时（即软键盘收起），将页面滚动回去
```JavaScript
$(document).on("blur", "input, select, textarea", function(){
  window.scrollTo(0, 0);
});
```

#### 聚焦输入框页面放大

输入框会有这种情况，好像是因为字体小于 16 px？总之直接禁用页面缩放
```HTML
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

#### 页面返回时不刷新

ios 在网页返回时并不会重新用 ajax 获取数据，需要用 js 刷新页面；    
应该在 `onpageshow` 事件而不是 `onload` 事件中执行刷新的代码，因为前者在每次加载页面时都会触发，而后者则只在第一次加载页面时触发，如果页面是从浏览器缓存中读取的则不会触发；  
`PageTransitionEvent` 对象的 `persisted` 属性可以判断页面是否从浏览器缓存中读取，是则为 true，不过使用 `history.back()` 返回的页面还是为 false；  
进一步的，可以用 `window.performance.navigation.type` 来判断： 
* **0**：页面是通过点击链接，书签，表单提交，脚本操作或直接浏览器输入地址访问的
* **1**：页面是通过点击刷新按钮，或 `location.reload()` 方法刷新访问的
* **2**：页面是通过历史记录或前进后退访问的
* **255**：其它

```JavaScript
window.addEventListener('pageshow', function(){
  if(e.persisted || (window.performance && window.performance.navigation.type == 2)){
    location.reload();
  }
}, false);
```