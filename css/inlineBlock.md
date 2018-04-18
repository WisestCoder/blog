### inline-block的对齐问题

大家在写页面的时候，是否经常遇到这样的问题：当两个设置了display: inline-block; 属性的元素并列排放时，它们的位置能够互相影响。

于是我就测试了一把，结果如下图：

![](https://github.com/wisestcoder/blog/blob/master/css/images/inline-block.png)

结果竟然是如此的怪异，我们探究原因，发现官方有如此说法：
>inline-block元素可以将对象呈递为内联对象，但是对象的内容作为块对象呈递。

其实按照官方说法，inline-block的元素会有一个对齐基线
> 对于一个inline-block元素，如果内部没有inline内联元素，或者overflow不是visible，则该元素的基线就是它margin的底边缘，否则就是元素内部最后一行内联元素的基线。

简单地就以上这种情况来说，没有内容的div会基于它的margin底边缘也就是它的下边缘对齐，而包含内容的div会基于其内部内联元素的基线对齐，所以会造成这种一上一下的情况。

对此我们有多种解决办法：

1、暴力float，当然这是备选方案，毕竟脱离文档流后页面元素会不好控制

2、简单粗暴地给所有元素都加上内容，例如空格符

3、设置所有内联元素 vertical-align: top/middle/bottom; 属性，改变默认设置