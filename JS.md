### 元素事件中的坐标和大小信息

```js
// e.pageX : 当前元素点击位置距离视口的x轴距离
// e.pageY : 当前元素点击位置距离视口的y轴距离
// box.offsetLeft : 当前元素距离父元素左边距离
// box.offsetTop : 当前元素距离父元素上边距离
// e.clientX : 当前元素点击位置距离视口的x轴距离
// e.offsetX : 当前元素点击位置距离元素x轴距离
// e.screenX : 当前元素点击位置距离屏幕x轴距离
// e.layerX : 往上找有定位属性的父元素的左上角（自身有定位属性的话就是相对于自身），
// 都没有的话，就是相对于body的左上角的坐标。
// box.clientWidth : 当前元素宽度 不包含边框
// box.offsetWidth: 当前元素宽度 包含边框
// box.scrollHeight: 当前元素高度，包含滚动的内容区域

// clientX和pageX的区别，clientX相当于浏览器可视范围的坐标，而pageX相当于整个网页的坐标。
```

