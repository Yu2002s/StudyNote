## HTML笔记

定义盒子大小的算法：

```css
boxsizing:content-box
boxsizing:border-box
```

盒子占据了多大的范围:

1. **width**
2. **height**
3. **margin**
4. **padding**

##### Margin-Padding 边距设置

上 右 下 左

```css
margin: 0 0 0 0;
/*分别代表 上 右 下 左 */
```

##### 不同类型盒子width属性的默认值

```html
<ul>
    <dt>inline</dt>
    <dd>width属性的默认值</dd>
    <dt>block</dt>
    <dd>width属性的默认值</dd>
    <dt>inline-block</dt>
    <dd>width属性的默认值</dd>
</ul>
<a href="" style="border:solid;">inline</a>
<div>
    block
</div>
<input type="text">
```



选择器、属性和属性值都和浏览器及浏览器的版本有关，兼容性各不同

### 选择器权重

| 选择器      | 权重 |
| ----------- | :--: |
| 通配符（*） |  0   |
| 伪元素      |  1   |
| 元素选择器  |  1   |
| class选择器 |  10  |
| 伪类        |  10  |
| 属性选择器  |  10  |
| id选择器    | 100  |
| 行内选择器  | 1000 |

常见的伪元素只有四个：::befer、::after、::first-letter和::first-line

伪类：hover、:first-child

优先级：行内选择器>id选择器>class选择器>元素选择器

###### 书写顺序

LVFHA



**1.什么是CSS？**

层叠样式表

Cascading style sheet

**2.将CSS文件链接给html文件使用的完整标签**

```css
<link rel="stylesheet" href="xxx.css">
```

**3.CSS的语法**

选择器 {

​	属性: 属性值

}

**4.四个基本选择器是?**

id选择器

类型选择器

类选择器

通用选择器

5.伪类选择器（:link)是寻找（超链接a）盒子（未访问、未点击）状态

6.伪类选择器（:visited)是寻找（超链接a）盒子（已访问、已点击）状态

7.伪类选择器（::after）是在（真）元素的 ( 内部)后面，插入一个假盒子

8.属性选择器（[attr|=val）是寻找 （寻找具有attr属性且属性值是以val开头含有连字符的）盒子

9.属性选择器 ([attr^=val]) 是寻找 （寻找具有属性attr且属性值是以val开头的）盒子

10.选择器属性和属性值都和（浏览器）和（浏览器版本）有关

**11.请问什么情况下CSS样式会发生层叠？**

当一个标签被相同的选择器选择到时，相同的样式会发生层叠，遵循就近原则

**12.请回答在什么情况下会发生css中的样式冲突？**

多个css样式应用到同一个元素时，这些样式之间可能存在对同一个属性的不同格式设置

**13.请问如何解决CSS中的样式冲突**

改变两个样式的加载顺序，提升样式的优先级，细化选择符，添加!important标记

**14.CSS中样式继承情况有哪两种？继承后样式冲突又是怎么解决的？**

自动继承（后代元素继承祖先元素），强制继承（使用属性inherit值）。添加!important标记解决样式冲突
