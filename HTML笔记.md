# 1 HTML是什么

Hyper Text Markup Language 超文本标记语言

# 2 万维网联盟

W3C World Wide Web Consortium 万维网联盟

成立于1994年，Web技术领域最权威和最具影响力的国际中立性技术标准机构

http://www.w3.org/   org表示开源  国际

http://www.chinaw3c.org/   中国

W3C标准包括：

- 结构化标准语言（HTML、XML）
- 表现标准语言（CSS）
- 行为标准（DOM、ECMAScript）

# 3 网页基本结构

```html
<!--DOCTYPE 告诉浏览器  我们要使用什么规范-->
<!DOCTYPE html>
<html lang="en">
<!-- head标签代表网页头部-->
<head>
<!--meta描述性标签 它用来描述我们网站的一些信息-->
<!--meta一般用来做SEO-->
    <meta charset="UTF-8">
    <meta name="keywords" content="夏天Java">
    <meta name="description" content="来这里学java">
<!-- title网页标题 -->
    <title>1</title>
</head>
<!--body标签表示网页主体-->
<body>
    <table>夏天网欢迎你!</table>
</body>
</html>
```

# 4 网页基本标签

## 4.1 标题标签

```html
<h1></h1>
<h2></h2>
<h3></h3>
<h4></h4>
<h5></h5>
<h6></h6>
```

## 4.2 段落标签

```html
<p></p>
```

## 4.3 换行标签

```html
<br/>
```

## 4.4 水平线标签

```html
<hr/>
```

## 4.5 字体样式标签

```html
粗体:<strong></strong>
斜体:<em></em>
```

## 4.6 注释和特殊符号标签

`<!--注释内容-->`

|  名称  | 代码  |
| :----: | :---: |
|  空格  | &nbsp |
|  大于  |  &gt  |
|  小于  |  &lt  |
| 版权符 | &copy |

## 4.7 图像标签

```html
<!--
src:图片地址(必填)
    相对地址(推荐) 绝对路径
    ../  上一级目录
alt:图片名字  如果图片不显示了则会出现名字来代替
title:把鼠标放在图片上 就会显示出的文字
-->
<img src="../resources/imag/1.png" alt="截图" height="300px" width="300px" title="悬停文字">
```

## 4.8 超链接标签

```html
<!--
href:跳转到的地址
target:表示窗口在哪里打开 _blank重新开一个页面打开 _self在自己的页面打开 默认就是self
可以将图片作为一个超链接
-->
<a href="1.MyFirstHtml.html" target="_blank">dad</a>

<a href="https://taobao.com" target="_self">dad</a>

<a href="https://taobao.com" target="_self">
    <img src="../resources/imag/1.png" alt="asd">
</a>
<!--锚点s-->
<a name="s">等下你就会跳我这里来</a>
<!--跳转到锚点v-->
<a href="#v">再跳回去点我</a>
<!--
锚链接
1.需要一个锚标记
2.跳转到该锚标记的地方
-->
<!--跳转到锚点s-->
<a href="#s">s</a>
<!--锚点v-->
<a name="v">你看又跳回来了</a>

<!--
功能性链接
邮箱链接:mailto
-->
<a href="mailto:1205232048qq.com">点击请联系我</a>
```

# 5 块元素和行内元素

```html
<!--块元素     无论内容多少,只占一行   <p><p/> 段标签-->
<!--行内元素    内容撑开宽度,左右都是行内元素可以排在一行-->
```

# 6 列表标签

```html
<!--有序列表-->
<ol>
    <li>Java</li>
    <li>Python</li>
    <li>C</li>
    <li>C++</li>
    <li>C#</li>
</ol>

<!--无序列表-->
<ul>
    <li>Java</li>
    <li>Python</li>
    <li>C</li>
    <li>C++</li>
    <li>C#</li>
</ul>

<!--自定义列表
dl:标签
dt:列表名称
dd:列表内容
一般应用于网站底部
-->
<dl>
    <dt>编程语言</dt>
    <dd>C</dd>
    <dd>C++</dd>
    <dd>Python</dd>
    <dd>Java</dd>

    <dt>位置</dt>
    <dd>北京</dd>
    <dd>上海</dd>
    <dd>广州</dd>
    <dd>深圳</dd>
</dl>
```

# 7 媒体元素

```html
<!--
video视频  
audio音频  
        controls会出现控制栏 
        autoplay 自动播放（部分浏览器禁止自动播放，若要自动播放需要添加muted，但是仍然为静音）
推荐图片 视频 音频文件都存放在同一个文件夹中 resources
-->
<video src="../resources/video/1.mp4" controls autoplay>asda</video>

<audio src="../resources/audio/1.m4a" controls autoplay>adadada</audio>
```

# 8 页面结构分析

```html
<!--标题头部区域的内容(用于页面或页面中的一块区域)-->
<header>
    <h2>头部区域</h2>
</header>

<!--标记脚部区域的内容(用于整个页面或页面的一块区域)-->
<footer>
    <h2>脚部区域</h2>
</footer>

<!--Web页面中的一块独立区域-->
<section>
    <h2>独立区域</h2>
</section>

<!--独立的文章内容-->
<article>
    <h2>文章区域</h2>
</article>

<!--相关内容或应用(常用于侧边栏)-->
<aside>
    <h2>侧边栏</h2>
</aside>

<!--导航类辅助内容-->
<nav>
    <h2>导航</h2>
</nav>
```

# 9 iframe内联框架

```html
<a href="1.MyFirstHtml.html" target="hello">点</a>

<iframe src="https://www.taobao.com" name="hello" frameborder="0" width="300" height="300"></iframe>
```

# 10 表单

```html
<!--
表单
action:提交后跳转的地址 可以是网站也可以是一个请求处理地址
method:提交方法有post和get两种  get提交的信息可以在url直接看到,不安全高效   post比较安全,传输大文件
-->
<form action="1.MyFirstHtml.html" method="get">
    <!--隐藏hidden 只读readonly 禁用disabled 必填项required 正则pattern-->
    <!-- 文本框 一般用来输入账号-->
    <p> 账号:<input type="text" name="username" placeholder="请输入你的账号" required> </p>
    <!-- 密码框 一般用来输入密码-->
    <p> 密码:<input type="password" name="pwd"> </p>

    <p>
        <input type="image" src="../resources/imag/1.png" >
    </p>

    <p>
        <input type="text" value="默认值" maxlength="8" size="15">
    </p>
    
    <p>
        <input type="radio" value="男" name="sex">男
        <input type="radio" value="女" name="sex">女
    </p>

    <p>爱好:
        <input type="checkbox" value="sleep" name="hobby">睡觉
        <input type="checkbox" value="code" name="hobby">敲代码
        <input type="checkbox" value="game" name="hobby">游戏
        <input type="checkbox" value="chat" name="hobby">聊天
    </p>

    <p>
        <input type="button" value="别点我" name="bdw">
    </p>

    <p>
        <!--点击按钮标签有提交表单的作用-->
        <input type="image" src="../resources/imag/1.png" >
    </p>

    <!--select表示一组下拉框  option是下拉框的元素 selected是默认值-->
    <p>国家:
        <select name="china">
            <option value="china"selected>中国</option>
            <option value="els">俄罗斯</option>
            <option value="mg">美国</option>
            <option value="yd">印度</option>
            <option value="rd">瑞典</option>
        </select>
    </p>

    <!--文本域 不太好在网页中可以随意改变大小-->
    <p>
        <textarea cols="20" rows="10">默认值</textarea>
    </p>

    <!--文件域-->
    <p>
        <input type="file" name="files">
    </p>

    <p>邮箱:
        <input type="email" name="email">
    </p>

    <p>URL:
        <input type="url" name="url">
    </p>

    <p>数字:
        <input type="number" name="num" max="100" min="0" step="10">
    </p>

    <p>音量:
        <input type="range" value="voice" max="100" min="0" step="5">
    </p>

    <p>搜索框:
        <input type="search" name="ssss">
    </p>

    <p>
        <input type="submit">
        <input type="reset">
    </p>

    <p>
        <!--增强鼠标可用性-->
        <label for="dwss">点我试试:</label>
        <input type="text" id="dwss">
    </p>
</form>
```













