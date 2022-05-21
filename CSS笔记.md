# CSS入门学习

> 前端三剑客

> - html：html为前端页面的主体。可以理解为一间什么东西都没有的房间
>- css：css为前端页面的样式。可以理解成房间里的地砖，灯，床
> - javascript：js为前端页面的脚本。可以理解成房间开灯，关门。

## 学习内容

1. CSS是什么
2. CSS怎么用（快速入门）
3. CSS选择器（重点+难点）
4. 美化页面（文字，阴影，超链接，列表，渐变….)
5. 盒子模型
6. 浮动
7. 定位
8. 网页动画（特效效果）



# 1、CSS

# 1.1、什么是CSS

- CSS：层叠样式表（英文全称：Cascading Style Sheets），用来修饰网页样式。
- 作用：美化网页，装饰网页样式。

## 1.2、 发展史

- CSS 1.0
- CSS 2.0 DIV(块)+CSS，HTML与CSS结构分离的思想，网页简单，SEO
- CSS 2.1 浮动，定位
- CSS 3.0 圆角，阴影，动画…浏览器兼容性~

## 1.3、CSS的几种导入方式

> 内部样式(推荐)

```html
<!--内部样式-->
<style>
    h1{
        color: yellow;
    }
</style>
```

> 外部样式

```html
<!--外部样式-->
<link rel="stylesheet" href="css/style.css">
```

> 内行样式

```html
<!--内行样式：在标签元素中编写一个style属性，编写样式即可-->
<h1 style="color: #c134b5" class="first" id="test">第一个标题</h1>
</body>
</html>
```

> 优先级遵循就近原则，离代码越近，优先级越高

# 2、 选择器

> 作用： 选择页面上的某一个或则某一类元素

## 2.1、 基本选择器

1.标签选择器： 选择一类标签。例如：h1{}

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*标签选择器会选择页面上所有的对应标签*/
        h1{
            color: lightblue;
        }
        p{
            color: lightcoral;
        }
    </style>
</head>
<body>
<h1>2.标签选择器</h1>
<p>这是一段文字</p>
</body>
</html>
```

2.类选择器 class： 选择所有class属性一致的标签。.类名{}

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*类选择器格式：.class的名称{}
        好处：不同的标签，可以用一个类。*/
        .text{
            color: lightcoral;
        }
    </style>
</head>
<body>
<h1>3.类选择器</h1>
<p class="text">这是一段文字</p>
<span class="text">这是一段文字</span>
</body>
</html>
```

3.ID选择器： 全局唯一！ #id名{}

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*id选择器 : id必须保证全局唯一
        优先级： id选择器 > class选择器 > 标签选择器*/
        #title{
            background: lightcoral;
        }
        .text{
            color: lightcoral;
        }
    </style>
</head>
<body>
<h1>4.id选择器</h1>
<p class="text">这是一段文字</p>
<span class="text">这是一段文字</span>
</body>
</html>
```

## 2.2、 层次选择器

1. 后代选择器： 在某个元素后面的所有元素 包括下代 下下代 下..下代

   ```css
   /*后代选择器*/
   body p{
       background: lightcoral;
   }
   ```

2. 子选择器 只包含下一代

   ```css
   /*子选择器*/
   body > p{
       background: lightcoral;
   }
   ```

3. 相邻，兄弟选择器（对下不对上）

   ```css
   /*兄弟选择器： 只有一个，向下*/
   .active + p{
   	background: lightcoral;
   }
   ```

4. 通用选择器

   ```css
   /*通用兄弟选择器， 当前选中元素的向下所有的兄弟元素*/
   .active~p{
   	background: lightcoral;
   }
   ```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*后代选择器*/
        body p{
            background: lightcoral;
        }
        /*子选择器*/
        p > span{
            background: lightblue;
        }
        /*兄弟选择器： 只有一个，向下*/
        .active + p{
            background: lightgreen;
        }
        /*通用兄弟选择器， 当前选中元素的向下所有的兄弟元素*/
        .active~li{
            background: lightcoral;
        }
    </style>
</head>
<body>
<h1 >2.标签选择器</h1>
<p class="active">这是一段文字
    <span>这是一段描述</span>
</p>
<p>这是段落2</p>
<ul>
    <li>1-1</li>
    <li class="active">1-2</li>
    <li>1-3</li>
</ul>
</body>
</html>
```

## 2.3、 结构伪类选择器

标签：条件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*ul的第一个子元素*/
        ul li:first-child{
            background: lightgreen;
        }
        /*ul的最后一个子元素*/
        ul li:last-child{
            background: lightcoral;
        }
        /*选中p1: 定位到父元素，选择当前的第一个元素
          选择当前p元素的父级元素，选中父级元素的第一个
          并且是当前元素才生效！
          */
        p:nth-child(2){
            background: lightblue;
        }
        /*第二个p标签*/
        p:nth-of-type(2){
            background: lightgoldenrodyellow;
        }
        /*鼠标在上面的样式*/
        a:hover{
            color: orange;
        }
    </style>
</head>
<body>
<h1>6.伪类选择器</h1>
<p>pp1</p>
<p>pp2</p>
<p>pp3</p>
<ul>
    <li>1-1</li>
    <li>1-2</li>
    <li>1-3</li>
</ul>
<a href="#">超链接。。。</a>
</body>
</html>
```

## 2.4、属性选择器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .demo a{
            display: inline-block;
            height: 50px;
            width: 50px;
            border-radius: 10px;
            background: #67e4ff;
            text-align: center;
            color: #ffffff;
            text-decoration: none; /*去下划线*/
            margin-right: 5px;
            font: bold 20px/50px Arial;
        }
        /* 属性名， 属性名 = 属性值（正则）
        = 绝对等于
        *= 包含这个元素
        ^= 以这个开头
        $ 以这个结尾
        */
        /*存在id属性的元素    a[]{}*/
        a[id]{
            background: lightgoldenrodyellow;
        }
        a[id=first]{
            background: lightblue;
        }
        /*class 中有links的元素*/
        a[class*="links"]{
            background: lightcoral;
        }
        /*选中href中以http开头的元素*/
        a[href^=http]{
            background: lightcyan;
        }
        /*a标签中href里面的值以jpg结尾*/
        a[href$=jpg]{
            background: lightseagreen;
        }
    </style>
</head>
<body>
<p class="demo">
    <a href="http://www.baidu.com" class="links item first" id="first">1</a>
    <a href="http://www.baidu.com" class="links item active" target="_blank" title="test">2</a>
    <a href="images/123.html" class="links item">3</a>
    <a href="images/123.png" class="links item">4</a>
    <a href="images/123.jpg" class="links item">5</a>
    <a href="abc" class="links item">6</a>
    <a href="/a.pdf" class="links item">7</a>
    <a href="/abc.pdf" class="links item">8</a>
    <a href="abc.doc" class="links item">9</a>
    <a href="abcd.doc" class="links item last">10</a>
</p>
</body>
</html>
```

# 3、美化网页元素

## 3.1、为什么要美化网页

1. 有效的传递页面信息
2. 美化网页，页面漂亮，才能吸引用户
3. 凸显页面的主题
4. 提高用户的体验

> 美化网页元素重点：为了好看，为了好看，为了好看。

## 3.2、字体样式

```css
font-family: 字体
font-size: 字体大小
font-weight: 字体粗细
color: 字体颜色
```

## 3.3、文本样式

```css
颜色 color rgb rgba
文本对齐的方式 text-align = center
首行缩进 text-indent
行高 line-height
装饰 text-decoration
文本图片水平对齐： vertical-align
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        h1{
            color: rgba(250, 75, 165, 0.5);
            text-align: center;
        }
        .p1{
            text-indent: 2em;
        }
        /*上划线*/
        .l1{
            text-decoration: underline;
        }
        /*中划线*/
        .l2{
            text-decoration: line-through;
        }
        /*下划线*/
        .l3{
            text-decoration: overline;
        }
        /*超链接去下划线*/
        a{
            text-decoration: none;
        }
    </style>
</head>
<body>
<h1>学习CSS</h1>
<p class="p1">穿过挪威的森林，让我走进你的梦里，夕阳落在我的铠甲，王子不一定骑着白马，黑马王子四海为家，现在是晚上18:00，我不是马思唯，也没有王子陪。</p>
<p class="l1">1231231</p>
<p class="l2">1231231</p>
<p class="l3">1231231</p>
</body>
</html>
```

## 3.4、列表前缀样式

```css
/*ul li*/
/*circle 空心圆
  decimal 数字
  square 正方形*/
/*ul li*/
ul li{
    list-style: none; /*去掉圆点*/
}
```

## 3.5、背景样式

- 背景图可以填充，拉伸，居中…等效果

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            width: 1000px;
            height: 700px;
            border: 1px solid red;
            background-image: url("images/t.jpg");
            /*默认是全部平铺的*/
        }
        .div1{
            background-repeat: repeat-x;
        }
        .div2{
            background-repeat: repeat-y;
        }
        .div3{
            background-repeat: no-repeat;
        }
    </style>
</head>
<body>
<div class="div1"></div>
<div class="div2"></div>
<div class="div3"></div>
</body>
</html>
```

## 3.6、渐变

一个渐变的网站：https://www.grabient.com/

```css
body{
    background: linear-gradient(43deg, #4158D0 0%, #C850C0 46%, #FFCC70 100%);
}
```

# 4、盒子模型

## 4.1、什么是盒子模型

![image-20220516115256554](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220516115256554.png)

margin：外边距

padding：内边距

border：边框

## 4.2、边框

1. 边框的粗细
2. 边框的样式
3. 边框的颜色

```css
/* 边框大小 边框样式 边框颜色 */
border: 1px solid #000000;
```

## 4.3、内外边距

margin：外边距

padding：内边距

```css
/*
    当margin/padding
    有一个参数，上下左右，都有边距
    有二个参数，上下，左右，表示
    四个参数时，上，右，下，左，表示
    margin：0 1px 2px 3px;
*/
```

参数是上右下左

盒子的计算方式：元素到底多大？

margin+border+padding+内容宽度

## 4.4、圆角边框

4个角

```css
div{
    width: 100px;
    height: 50px;
    margin: 30px;
    border: 2px solid red;
    border-radius: 50px 50px 0px 0px; /* 和边距的参数一样，左上，右上，右下，左下 */
}
```

## 4.5、阴影

```css
/*
h-shadow    必需。水平阴影的位置。允许负值。
v-shadow    必需。垂直阴影的位置。允许负值。
blur    可选。模糊的距离。
color    可选。阴影的颜色。
text-shadow: h-shadow v-shadow blur color;
*/
```

## 4.6、综合案例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*
            当margin/padding
            有一个参数，上下左右，都有边距
            有二个参数，上下，左右，表示
            四个参数时，上，右，下，左，表示
            margin：0 1px 2px 3px;
        */
        #box{
            width: 300px;
            margin: 0 auto; /*有宽度时，可以居中*/
            padding: 10px; /* 内边距 */
            border: 1px solid #67e4ff;
            background: linear-gradient(43deg, #4158D0 0%, #C850C0 46%, #FFCC70 100%);
            border-radius: 20px;/*圆角*/
            box-shadow: 10px 10px 10px #000000;/*阴影*/
        }
        h2{
            color: #ffffff;
            text-align: center;
        }
        div{
            padding: 10px;
        }
        input[type=submit]{
            width: 100%;
        }
    </style>
</head>
<body>
<div id="box">
    <h2>会员登录</h2>
    <form action="#">
        <div>
            <span>用户名：</span>
            <input type="text">
        </div>
        <div>
            <span>密码：</span>
            <input type="text">
        </div>
        <div>
            <input type="submit">
        </div>
    </form>
</div>
</body>
</html>
```

# 5、浮动

## 5.1、 标准文档流

块级元素：独占一行

```html
<!-- h1-h6 p div ... -->
```

行内元素：不独占一行

```html
<!-- span a img strong ... -->
```

块级元素可以存在行内元素。

## 5.2、display

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- block，块元素
         inline 行内元素
         inline-block  是块元素，但是可以内联，在一行！
         none： 消失-->
    <style>
        div{
            width: 100px;
            height:100px;
            border: 1px solid red;
            display: inline;
        }
        span{
            width: 100px;
            height:100px;
            border: 1px solid red;
            display: block;
        }
    </style>
</head>
<body>
<div>div块元素</div>
<span>span行内元素</span>
</body>
</html>
```

## 5.3、float

1、左右浮动

```css
div{
    float: right;/*向右浮动*/
}
```

浮动的元素会脱离标准流。

## 5.4、父级边框塌陷问题

```css
/*
clear: right; 右侧不允许有浮动元素
clear: left;  左侧不允许有浮动元素
clear: both;  两侧不允许有浮动元素
clear: none;  不允许有浮动元素
*/
.text{
    clear:both;
}
```

解决父级边框塌陷问题：

1、增加父级元素高度

```css
#body{
    border: 1px #000 solid;
    height: 800px;
}
```

2、增加一个空的div标签，清除浮动

```css
<div class="clear"></div>
.clear{
    clear: both;
    margin: 0;
    padding: 0;
}
```

3、父类添加一个伪类：after

```css
#body:after{
    content: '';
    display: block;
    clear: both;
}
```

小结：

1、浮动元素后面增加div

简单，代码中尽量避免空div

2、设置父元素的高度

简单，元素假设有了固定的高度，就会被限制

3、父类添加伪类:after(推荐使用)

原理跟第一个方法一样。这里是通过伪类添加元素。

# 6、定位

## 6.1、相对定位

```css
div{
    position: relative/*相对定位*/
    top: -20px;/*相对当前的位置 向上移动，负值相反方向*/
    left: 20px;/*相对当前的位置 向右移动*/
    bottom: -20px;/*相对当前的位置 向下移动*/
    right: 20px;/*相对当前的位置 向左移动*/
}
```

相对于原来的位置，进行指定的偏移，相对定位的话，它仍然在标准文档流中，原来的位置会被保留

## 6.2、绝对定位

```css
div{
    position: absolute;/*绝对定位*/
    top: -20px;/*相对当前的位置 向上移动，负值相反方向*/
    left: 20px;/*相对当前的位置 向右移动*/
}
```

绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于浏览器。

用绝对定位的元素会脱离文档流。

## 6.3、固定定位

```css
div{
    position: fixed;/*固定定位*/
    right: 0;
    bottom: 0;
}
```

固定定位使元素的位置相对于浏览器窗口来定位。

即使窗口是滚动的它也不会移动。

### 6.4、z-index

```css
div{
    z-index:2;
}
```

图层，用定位的时候会产生会和其他元素重叠。

相当于上一层盖住了下一层的内容。

# 7、动画

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
<style> 
div{
    width:100px;
    height:100px;
    background:red;
    animation:myfirst 5s; /* 动画执行时间 */
}
@keyframes myfirst{
    0%   {background: red;}
    25%  {background: yellow;}
    50%  {background: blue;}
    100% {background: green;}
}
</style>
</head>
<body>
    <div></div>
</body>
</html>
```

作业：

- 定位练习
  ![image-20220516115211301](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220516115211301.png)
- 模仿京东登录
  ![image-20220516115225544](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220516115225544.png)
- 模仿QQ会员导航顶部
  ![image-20220516115235262](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220516115235262.png)

