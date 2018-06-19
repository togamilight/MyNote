# 第二章 为样式找到应用目标

## 2.1 常用的选择器

`tagName` 标签选择器， `.class` 类选择器，`#id` id选择器， `parent child` 后代选择器

### 伪类

* `:link :visited` 称为**链接伪类**，只能用于锚元素。
* `:hover :active :focus` 称为**动态伪类**，理论上可用于任何元素。

## 2.3 高级选择器

`parent>child` 子选择器，`h2+p`其后相邻同胞选择器（IE7以上都支持，但在IE7中，父子元素之间有HTML注释会出问题）
* 在不兼容的浏览器中，可以使用以下方法实现相同效果
    ```CSS
    /*先将样式应用到所有后代元素，再将非直接子元素的样式还原*/
    #nav li { /*想实现在子元素上的效果*/ }
    #nav li * { /*还原非直接子元素的样式*/ }
    ```

`[attr="value"]`属性选择器
* 属性选择器的几种形态：
    * `[attribute]`：根据属性名选择
    * `[attribute='value']`：根据属性值选择
    * `[attribute~='value']`：根据属性值之一选择
    * `[attribute|='value']`：选择属性值以value开头的属性的元素（value或value-*）