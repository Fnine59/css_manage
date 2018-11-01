## css_manage

   中台首页样式练习项目

## 项目心得与总结

在完成纯css实现中台首页样式这个练习的过程中，遇到了一系列问题，现总结如下。

### 外边距（margin）塌陷

#### 问题描述

在编写左侧导航栏的过程中，发现了这样的情况。

![问题描述](https://ws1.sinaimg.cn/large/006gU7ahly1fwri52j3yoj30nk08ewen.jpg)

#### 外边距塌陷是什么？

经过查询，发现上面这种情况被称为“外边距塌陷”或“外边距重叠”，它是指，*两个或多个盒子（可能相邻也可能嵌套）的相邻边界（其间没有任何非空内容、补白、边框）重合在一起而形成一个单一边界*。

通过图示可以直观的理解外边距塌陷的几种情况。

1. 当一个元素出现在另一个元素上面时，第一个元素的底边界与第二个元素的顶边界发生合并：

![图示1.1](https://upload-images.jianshu.io/upload_images/5122957-d05334d7408c00ba.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/504/format/webp)

2. 当一个元素包含在另一个元素中时（假设没有内边距或边框把外边距分隔开），它们的顶和底边界也发生合并：

![图示1.2](https://upload-images.jianshu.io/upload_images/5122957-3618d6539fccf3a5.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/504/format/webp)

3. 假设有一个空元素，它有边界，但是没有边框或填充。在这种情况下，顶边界与底边界就碰到了一起，它们会发生叠加：

![图示1.3](https://upload-images.jianshu.io/upload_images/5122957-4be1f7259d975334.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/481/format/webp)

#### 为什么存在外边距塌陷？

外边界塌陷乍看上去可能显得有些奇怪，但它是有实际意义的。以由几个段落组成的典型文本页面为例（见下图）。第一个段落上面的空间等于段落的顶边界。如果没有边界叠加，后续所有段落之间的边界将是相邻顶边界和底边界的和。这意味着段落之间的空间是页面顶部的两倍。如果发生边界叠加，段落之间的顶边界和底边界就叠加在一起，这样各处的距离就一致了。简单来说，当我们上下排列一系列规则的块级元素（如段落P）时，那么块元素之间因为外边距重叠的存在，就不会产生双倍的距离。

![图示1.4](https://upload-images.jianshu.io/upload_images/5122957-d50a10b102caa892.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/355/format/webp)

#### 不会出现外边距塌陷的情况

1. 水平边距永远不会重合。
2. 在规范文档中，2个或以上的块级盒模型相邻的垂直`margin`会重叠。最终的`margin`值计算方法如下：
    1. 全部都为正值，取最大者；
    2. 不全是正值，则都取绝对值，然后用正值减去最大值；
    3. 没有正值，则都取绝对值，然后用0减去最大值。
    
    注意：相邻的盒模型可能由DOM元素动态产生并没有相邻或继承关系。
3. 相邻的盒模型中，如果其中的一个是浮动的（`float`），垂直`margin`不会重叠，并且浮动的盒模型和它的子元素之间也是这样。
4. 设置了`overflow`属性的元素和它的子元素之间的`margin`不被重叠（`overflow`取值为`visible`除外）。
5. 设置了绝对定位（`position:absolute`）的盒模型，垂直`margin`不会被重叠，并且和他们的子元素之间也是一样。
6. 设置了`display:inline-block`的元素，垂直`margin`不会重叠，甚至和他们的子元素之间也是一样。
7. 如果一个盒模型的上下`margin`相邻，这时它的`margin`可能重叠覆盖（collapse through）它。在这种情况下，元素的位置（`position`）取决于它的相邻元素的`margin`是否重叠。
    1. 如果元素的`margin`和它的父元素的`margin-top`重叠在一起，盒模型`border-top`的边界定义和它的父元素相同。
    2. 另外，任意元素的父元素不参与`margin`的重叠，或者说只有父元素的`margin-bottom`是参与计算的。如果元素的`border-top`非零，那么元素的`border-top`边界位置和原来一样。
    3. 一个应用了清除操作的元素的`margin-top`绝不会和它的块级父元素的`margin-bottom`重叠。
    
    注意，那些已经被重叠覆盖的元素的位置对其他已经重叠的元素的位置没有任何影响；只有在对这些元素的子元素定位时，`border-top`边界位置才是必需的。
8. 根元素的垂直`margin`不会被重叠。

#### 外边距塌陷时的解决方法

虽然外边距的重叠有一定的意义，但有时候我们在设计上却不想让元素之间产生重叠，那么可以有如下几个建议可供参考：

- 外层元素`padding`代替
- 内层元素透明边框`border:1px solid transparent;`
- 内层元素绝对定位`postion:absolute;`
- 外层元素`overflow:hidden;`
- 内层元素加`float:left;`或`display:inline-block;`
- 内层元素`padding:1px;`
- 底部元素设置为浮动`float:left;`
- 底部元素的`position`的值为`absolute/fixed`
- 在设置`margin-top/bottom`值时统一设置上或下

#### 参考文章

[CSS 外边距(margin)重叠及防止方法 —— blog](http://www.hujuntao.com/web/css/css-margin-overlap.html)

[什么是外边距重叠，重叠的结果是什么？ —— 简书](https://www.jianshu.com/p/669c6aef425c)

[CSS中margin边界叠加问题及解决方案 —— 51CTO](http://developer.51cto.com/art/201008/221940_all.htm)

[CSS外边距(margin)重叠及防止方法 —— 掘金](https://juejin.im/entry/59c3be3e6fb9a00a571d39e4)

[如何解决外边距叠加的问题？ —— 知乎](https://www.zhihu.com/question/19823139)

### 子元素的margin-top影响父元素

#### 问题描述

因为当时的代码没有保留，我们写一个最简单的例子来重现这种情况。

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <title>test</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style type="text/css">
    *{
      margin: 0px;
      padding: 0px; 
    }
    html, body {
      background-color: #cccccc;
    }
    .container{
      width: 200px;
      height: 200px;
      background-color: #999999;
    }
    .container h2{
      margin-top: 50px;
      border: 10px solid red;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>sub content</h2>
  </div>
</body>
</html>
```

上面这段代码运行后，展现的效果如下图。

![图示2.1](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjja9tjmj309109v3ye.jpg)

只有一张图也许不够清晰，我们添加一些标注：

![图示2.2](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjlh09hlj30kr0990sv.jpg)

我们为h2添加一个border，可以更加直观地看到问题。

```CSS
    *{
      margin: 0px;
      padding: 0px; 
    }
    html, body {
      background-color: #cccccc;
    }
    .container{
      width: 200px;
      height: 200px;
      background-color: #999999;
    }
    .container h2{
      margin-top: 50px;
      border: 10px solid red;
    }
```

![图示2.3](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjn7ckf3j309k08qq2t.jpg)

很明显，我们并没有为深灰色的父级元素添加`margin-top`属性，明明是将`margin-top`属性添加到了红色边框的部分，但展现出来的效果却像是为父元素添加了`margin-top`属性。

上面的代码，我们预期得到的效果如下图。

![图示2.4](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjq06gcvj30er074q2w.jpg)

我们通过chrome开发者工具来查看父元素和子元素。

父元素：

![图示2.5](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjshvna5j311y0epdh5.jpg)

子元素：

![图示2.6](https://ws1.sinaimg.cn/large/006gU7ahly1fwrjtu6a67j311y0gvdhk.jpg)

可以发现，这个50px的`margin-top`依旧属于子元素。

#### 问题分析

来看css2.1盒模型中规定的内容：

```
In this specification, the expression collapsing margins means that adjoining margins (no non-empty content, padding or border areas or clearance separate them) of two or more boxes (which may be next to one another or nested) combine to form a single margin.

所有毗邻的两个或更多盒元素的margin将会合并为一个margin共享之。毗邻的定义为：同级或者嵌套的盒元素，并且它们之间没有非空内容、Padding或Border分隔。
```

可以发现，这个问题属于第一个问题中的父子元素外边距塌陷问题。

#### 解决方案

1. 父级元素或子元素使用浮动或者绝对定位`absolute`（因为浮动或绝对定位不参与`margin`的折叠）

2. 父级元素`overflow:hidden;`

3. 父级元素设置`padding`（破坏父子元素的外边距之间没有非空内容的折叠条件）

4. 父级元素设置`border`（破坏父子元素的外边距之间没有非空内容的折叠条件）

我们采取上述任意一种解决方案，都可以发现显示效果恢复正常。

#### 参考文章

[子元素margin-top为何会影响父元素？ —— CSDN](https://blog.csdn.net/sinat_27088253/article/details/52954688)

[子元素应用margin-top为何会影响父元素【转】](http://www.cnblogs.com/hejia/archive/2013/05/26/3099697.html)

### 子元素浮动导致父元素高度塌陷

在使用`float`属性的时候，我们经常可以碰到如下图所示这种父元素高度塌陷的情况。

![图示3.1](http://www.w3school.com.cn/i/ct_css_positioning_floating_clear_div.gif)

想要理解这种情况，首先要理解浮动是什么。

#### 关于浮动

浮动是样式编写中经常使用到的一个属性，对于浮动的理解简单来说就是五个字：“**脱离文档流**”。来看一下官方对于浮动的解释：

    浮动的框可以向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。由于浮动框不在文档的普通流中，所以文档的普通流中的块框表现得就像浮动框不存在一样。

#### 子元素浮动导致的父元素高度塌陷

如果对浮动有了一个清晰的认识，也就不难理解父元素为什么会“塌陷”。子元素脱离了普通文档流而父元素没有，因此处于普通文档流中的父元素就会“*表现得就像浮动框不存在一样*”，对于父元素来说，它的子元素就像不存在一样，因此高度也就“塌陷”了。

一旦出现了这种父元素高度塌陷问题，应该如何解决？

**方法一：为某个子元素设置clear属性**

如下图右侧示例。

![图示3.2](http://www.w3school.com.cn/i/ct_css_positioning_floating_clear_div.gif)

这种方法能够实现我们希望的效果，但通常出现高度塌陷的父元素，其子元素基本均是浮动元素，如果找不到一个可以应用清理的元素，我们就只能添加一个空元素并且对它应用清理，这部分代码实际上是多余的。

**方法二：对容器 div 进行浮动**

这种方法是最容易理解的方法了。既然子元素浮动后脱离了普通文档流从而导致处于普通文档流中的父元素高度塌陷，那么只要让父元素也一起浮动，它就可以再次感知到子元素的存在了，高度自然也会被子元素撑开。但这种方法也有不尽人意的地方，那就是下一个处于普通文档流中的父元素也会受到这个浮动元素的影响。

要解决这个问题，可以对布局中的所有东西进行浮动，然后使用适当的有意义的元素（常常是站点的页脚）对这些浮动进行清理。这有助于减少或消除不必要的标记，并且能够实现我们想要的效果。

**方法三：为float元素的父元素添加overflow:hidden**

为浮动元素的父元素添加`overflow:hidden;`即可解决这种塌陷问题。

只要给外面大容器加上`overflow:hidden`的属性，可以解决IE7和火狐浏览器下的清除浮动问题，但是IE6下不生效，所以我们还需要使用`zoom`这个IE的私有属性来达到彻底清除浮动的兼容效果，代码如下。

```CSS
#container {
	border: 1px solid green;
	overflow: hidden;
	zoom: 1;
}
```

**方法四：使用after伪对象清除浮动**

代码如下。

```CSS
#container:after {
	content: ".";
	display: block;
	height: 0;
	clear: both;
	visibility: hidden;
}
```

#### 参考文章

[CSS 浮动 —— w3school](http://www.w3school.com.cn/css/css_positioning_floating.asp)

[CSS中子元素浮动导致父元素高度塌陷解决方案 —— CSDN](https://blog.csdn.net/liuliuliu_666/article/details/70847566)

[HTML的文档流和文本流分别是什么？ —— 知乎](https://www.zhihu.com/question/21911352)

[clear属性 —— w3school](http://www.w3school.com.cn/cssref/pr_class_clear.asp)


