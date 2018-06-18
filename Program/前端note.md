

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
```

### return false 与 阻止冒泡、阻止默认行为
* 在事件函数中，使用`event.stopPropagation()`可以阻止事件冒泡，使用`event.preventDefault()`可以阻止浏览器默认行为。
* 使用`return false`则可以同时达到两者的效果
