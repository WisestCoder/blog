### 元素垂直居中
```css
确定容器的宽高 宽500 高 300 的层
设置层的外边距

div {
 	position: relative;		/* 相对定位或绝对定位均可 */
 	width:500px;
 	height:300px;
 	top: 50%;
 	left: 50%;
 	margin: -150px 0 0 -250px;     	/* 外边距为自身宽高的一半 */
 	background-color: pink;	 	/* 方便看效果 */
}
```

```css
未知容器的宽高，利用`transform`属性

div {
  position: absolute;		/* 相对定位或绝对定位均可 */
  width:500px;
  height:300px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: pink;	 	/* 方便看效果 */
}
```

```css
利用`flex`布局
实际使用时应考虑兼容性

.container {
  display: flex;
  align-items: center; 		/* 垂直居中 */
  justify-content: center;	/* 水平居中 */
}
.container div {
  width: 100px;
  height: 100px;
  background-color: pink;		/* 方便看效果 */
} 
```

```css
利用`vertical-align`

div {
  display: table-cell;
  margin: auto;
  vertical-align;
}
```