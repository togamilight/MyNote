

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
* 如果想让a标签不跳转，可以用`href="#任何不存在的id"`,或者`href="javascript:void(0);"`，或者在点击事件中阻止浏览器默认行为：`return false;`或者`event.preventDefault();`

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


## CSS hack

