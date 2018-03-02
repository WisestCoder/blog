### 两个节点之间可能存在哪些关系？以及如何在节点之间任意移动？

1.DOM中两个节点存在的关系无非3种：
  - 包含与被包含,IE率先引入的contains()方法可检测，例 A.contains(B)，
    即检查节点B是否是节点A的子节点，返回布尔值，现大多数浏览器都支持；
    DOM level 3引入的compareDocumentPosition()，确定节点之间的关系，
    返回值为一个表示关系的位掩码（见图-1）的合(或者是按位或的值，并不知道具体实现)，
    通过按位与操作符“&”可确定关系。例:节点A(例：<html>)在节点B(例：<body>)前--位掩码为4，
    且节点A包含节点B--位掩码为16,则返回值为20，通过"!!(20&16)"这种方式即可返回一个布尔值，
    解析：“20&16”返回16证明节点A包含节点B（即：16），通过!!取得16的布尔值true。
  - 父与子
    获取父节点：node.parentNode, node.parentElement,两者的区别在于后者只能获取元素，例如：图-2；
    获取子节点：childNodes(以NodeList对象存在的子节点集合),firstChild,lastChild
  - 同辈（兄弟节点）
    nextSibling，previousSibling  
  - Element Travel API给DOM添加的属性
    childElementCount,firstElementChild,lastElementChild,nextElementSibling，
    previousElementSibling  ,他们与之前的方法之间的区别是多了Element，保证只返回元素节点，而之前的方法普通的文本节点及注释节点也会返回,之前的方法在非IE浏览器中还会把元素间的空白符当文本节点返回。
  - children属性（IE9以后）
    与childNodes不同的地方在于：children只包含元素子节点(IE8及之前的版本可能会包含注释节点)。
2.如何在节点之间任意移动？
  根据上面提到的属性值移动就好了（感觉这句话水水的
