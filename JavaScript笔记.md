# 1、 什么是JavaScript

## 1.1、 概述

> JavaScript是世界上最流行的脚本语言   10天开发出来的   用Java开头引流  

**一个合格的后端人员，必须要精通Javas**

## 1.2、 历史

> https://blog.csdn.net/kese7952/article/details/79357868
>
> **ECMAScirpt**他可以理解为是JavaScript的一个标准
>
> 最新版本已经到es6版本，但是大部分浏览器还只停留在支持es5代码上！
>
> 开发环境—线上环境，版本不一致

# 2、 快速入门

## 2.1、 引入JavaScript

1. 内部标签

   ```html
   <script>
       window.alert("你好，王小姐");
   </script>
   ```

2. 外部引入

   ```html
   <script src="js/fistjs.js"></script>
   ```

3. 测试代码

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <!--script标签内写JavaScript代码，script标签必须成对出现，不用显式定义type，默认就是JavaScript-->
       <script type="text/javascript"></script>
       <!--第一种方式，内部引用
   <script>
        window.alert("你好，王小姐");
    </script>-->
       <!--第二种方式，外部引用-->
       <script src="js/fistjs.js"></script>
   </head>
   <body>
   </body>
   </html>
   ```

## 2.2、 基本语法入门

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<script>
    var score = 71;
    if (score>=0&&score<=100){
        if (score>70&&score<80){
            alert("70-80");
        }else if (score<70) {
            alert("0-70");
        }else {
            alert("other");
        }
    }
</script>
</body>
</html>
```

## 2.3、 数据类型

数值，文本，图形，音频，视频….

**变量**

```javascript
var a = 1;
```

**number**

js不区分小数和整数，Number

```javascript
123//整数
123.1//浮点数
-99//负数
NaN//not a number
Infinity//表示无限大
```

**字符串**

‘abc’ “abc”

**布尔值**

true false

**逻辑运算符**

```javascript
&&  //两个都为真，结果为真
||    //一个为真，结果为真
!    //真即假，假即真
```

**比较运算符**

```javascript
=    //赋值预算法
==    //等于（类型不一致，值一样，也会判断为true  即判断1=='1'）
===    //    绝对等于（类型一样，值一样，结果为true）
```

这是一个JS的缺陷，坚持用===进行比较

须知：

- NaN，与所有的数值都不相等，包括自己
- 只能通过isNaN()来判断这个数是不是NaN

**浮点数问题：**

```javascript
console.log(1/3)===(1-2/3) //结果为false
```

尽量避免使用浮点数进行运算，存在精度问题

```javascript
Math.abs(1/3-(1-2/3))<0.00000001 //结果为true
```

**null和undifined**

- null 空
- undefined 未定义

**数组**

Java的数组必须是相同类型的对象。JS中不需要这样！

```javascript
//保证代码的可读性，尽量使用[]
var arr = [1,2,3,'hello',null,true];
//取数组下标：如果越界了，就会报undefined
```

**对象**

对象是大括号，数组是中括号

每一个属性之间使用逗号隔开，最后一个不需要加逗号

```javascript
var person = {
    name: "王小姐",
    age: 16,
    tags: ['可爱','樱桃小嘴', '柳叶眉', '大眼睛']
}
```

取对象的值

```javascript
person.name
"王小姐"
person.tags[0]
"樱桃小嘴"
```

## 2.4、 严格检查格式

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--
前提：Idea，设置支持ES6语法
'use strict';严格检查模式，预防JavaScript随意性导致产生的一些问题
必须写在script标签内的第一行！
局部变量建议都使用let去定义
-->
<script>
    'use strict';
    let i = 1;
</script>
</body>
</html>
```

# 3、 数据类型

## 3.1、 字符串

1. 正常字符串我们使用单引号或者双引号包裹

2. 注意转义字符 \

   ```javascript
   \'
   \n
   \t
   \u4e2d   \u#### Unicode 字符
   \x41       Ascll字符
   ```

   3.多行字符串编写

   ```javascript
   //tab 和ESC键中间的字符`` 包裹
   var msg = `
           hello
           world
           你好
           `
   ```

   4.模板字符串

   ```javascript
   let name = '王小姐';
   let msg = `你好呀，${name}`;
   ```

   5.字符串长度

   ```javascript
   str.length
   ```

   6.字符串不可变

   ```javascript
   let student = "student"; student[1] = 1;
   >1
   console.log(student)
   >student
   ```

   7.大小写转换

   ```javascript
   let student = "student";
   console.log(student.toUpperCase());
   console.log(student.toLowerCase());
   ```

   8.sutdent.indexOf(‘t’);

   9.substring

   ```javascript
   let student = "student";
   //左闭右开[0,3)，从第一个字符串取到第二个 stu;
   console.log(student.substring(0,3));
   ```

## 3.2、 数组

Array可以包含任意的数据类型

```javascript
var arr = [1,2,3,4,5,6]
//通过下标取值和赋值
arr[0]
arr[0] = 1;
```

1. 长度

   ```javascript
   arr.length
   ```

   注意：假如给arr.length赋值，数组的大小就会发生变化，如果赋值过小，元素就会丢失

2. indexOf，通过元素获得下标索引

   ```javascript
   arr.indexOf(2);
   ```

   字符串的”1”和数字的1是不同的

3. slice() 截取Array的一部分，返回一个新的数组，类似于String中的substring

4. push() ， pop() 尾部

   ```javascript
   //push(); 压入到尾部
   let arr = [1,2,3,4,5,"6","a"];
   arr.push("b");
   [1, 2, 3, 4, 5, "6", "a", "b"];
   //pop();删除尾部
   arr.pop();
   [1, 2, 3, 4, 5, "6", "a"];
   ```

5. unshift() , shift() 头部

   ```javascript
   //unshift() 头部增加
   let arr = [1, 2, 3, 4, 5];
   arr.unshift(0);
   [0, 1, 2, 3, 4, 5];
   //shift(),删除头部
   arr.shift();
   [1, 2, 3, 4, 5];
   ```

6. 排序 sort()

   ```javascript
   let arr = [6, 2, 8, 4, 5];
   arr.sort();
   [2, 4, 5, 6, 8];
   ```

7. 元素反转

   ```javascript
   let arr = [2, 4, 5, 6, 8];
   arr.reverse();
   [8, 6, 5, 4, 2];
   ```

8. concat()

   ```javascript
   let arr = [8, 6, 5, 4, 2];
   arr.concat(['a','b','c']);
   //返回一个新的数组 [8, 6, 5, 4, 2, "a", "b", "c"];
   //并未改变原来的数组arr
   [8, 6, 5, 4, 2];
   ```

9. 连接符 join

   打印拼接数组，使用特定的字符串连接

   ```javascript
   let arr = [8, 6, 5, 4, 2];
   arr.join("-");
   "8-6-5-4-2";
   ```

10. 多维数组

    ```javascript
    let arr = [[1,2], [3,4], ['a','b']];
    arr[1][1];
    4;
    ```

## 3.3、 对象

若干个键值对

```javascript
/*var 对象名 = {
    属性名: 属性值,
    属性名: 属性值,
    属性名: 属性值
}*/
var person = {
    name : "王小姐",
    age : 16,
    email : "summer@qq.com",
    score : 100
}
```

js中的对象， {…..}表示一个对象，键值对描述属性xx : xx，多个属性之间使用逗号隔开，最后一个属性不加逗号！

JavaScript 中的所有键都是字符串，值是任意对象！

1. 对象赋值

   ```javascript
   var person = {
       name : "",
       age : 16,
       email : "summer@qq.com",
       score : 100
   }
   person.name="王小姐";
   >"王小姐"
   person.name;
   >"王小姐"
   ```

2. 使用一个不存在的对象属性，不会报错！undefined

   ```javascript
   var person = {
       name : "",
       age : 16,
       email : "summer@qq.com",
       score : 100
   }
   person.dog;
   undefined
   ```

3. 动态的删减属性，通过delete删除对象的属性

   ```javascript
   var person = {
       name : "",
       age : 16,
       email : "summer@qq.com",
       score : 100
   }
   delete person.score;
   person
   >{name: "王小姐", age: 16, email: "summer@qq.com"}
   ```

4. 动态的添加，直接给新的属性添加值即可

   ```javascript
   var person = {
       name : "",
       age : 16,
       email : "summer@qq.com",
       score : 100
   }
   person.dog = "tom";
   person.tom;
   >tom
   ```

5. 判断属性值是否在这个对象中！ xx in xx

   ```javascript
   var person = {
       name : "王小姐",
       age : 16,
       email : "summer@qq.com",
       score : 100
   };
   "age" in person;
   >true;
   //继承
   'toString' in person
   >true;
   ```

6. 判断一个属性是否是这个对象自身拥有的 hasOwnProperty()

   ```javascript
   var person = {
       name : "王小姐",
       age : 16,
       email : "summer@qq.com",
       score : 100
   };
   person.hasOwnProperty("toString");
   >false
   person.hasOwnProperty("age");
   >true
   ```

## 3.4、 流程控制

> if判断

```javascript
var age = 3;
if(age > 3){
    alert("哈哈");
}else if(age < 5){
    alert("kuwa~");
}else{
    alert("heihei");
}
```

> while循环，避免程序死循环

```javascript
while(age<100){
    age = age+1;
    console.log(age);
}
do{
    age = age+1;
    console.log(age);
}while(age<100);
```

> for循环

```javascript
for(let i = 0; i < 100; i++){
    console.log(i);
}
```

> forEach循环
>
> ES5.1引入的

```javascript
var age = [12,3,4,22,23,55];
//传一个函数
age.forEach(function (value,index) {
    console.log(value);
    console.log(index);
})
```

> for…in

```javascript
//for(var index in object)
var age = [12,3,4,22,23,55];
for (var num in age){
    console.log(age[num]);
}
```

## 3.5、 Map 和 Set

> ES6新特性

> Map：

```javascript
var map = new Map([['tom',100],['jack',90],['tim',80]]);
var name = map.get('tom');//通过key获取value
map.delete('tom');//删除元素
map.set('kate',70);
```

> Set: 无序不重复的集合

```javascript
var set = new Set([3,2,1]);
set.add(5);//增加元素
set.delete(3);//删除元素
set.has(1);//判断是否包含元素
set.size;//长度
```

## 3.6 iterator

> ES6新特性 	

> 遍历数组

```javascript
//通过for of遍历值 / for in 遍历下标
var array = [1,2,3];
for (let x of array){
    console.log(x);
}
```

> 遍历map

```javascript
var map = new Map([['tom',100],['jack',90],['tim',80]]);
for (let x of map){
    console.log(x);
}
```

> 遍历set

```javascript
var set = new Set([3,2,1]);
for (var x of set){
    console.log(x);
}
```

# 4、 函数

## 4.1、 定义函数

> 定义方式一

绝对值函数

```javascript
function abs(x){
    if(x >= 0){
        return x;
    }else{
        return -x;
    }
}
```

一旦执行到return 代表函数结束，返回结果！

如果没有执行return ， 函数执行完也会返回结果，结果就是undefined

> 定义方式二

```javascript
var abs = function(x){
    if(x >= 0){
        return x;
    }else{
        return -x;
    }
}
```

function(x){….} 这是一个匿名函数，但是可以把结果赋值给abs，通过abs可以调用函数！

方式一和方式二等价！

> 调用函数

```javascript
abs(10)   //10
abs(-10)  //10
```

> 参数问题：JavaScript 可以传任意多个参数，也可以不传
>
> 自定义异常

```javascript
function abs (x){
    if(typeof x!== "number"){
        throw "Not A Number";
    }
    if(x >= 0){
        return x;
    }else{
        return -x;
    }
}
```

> 参数传多个问题  
>
> arguments  关键字 参数数组
>
> 代表 传递进来的所有参数，是一个数组！

```javascript
function abs(x){
    for(let i = 0;i < arguments.length;i++){
        console.log(arguments[i]);
    }
    if(x >= 0){
        return x;
    }else{
        return -x;
    }
}
```

> **arguments** 包含所有的参数，想使用多余的参数操作，需要排除已有参数
>
> ES6引入**rest** 获取除了已定义的参数之外所有的参数 …

```javascript
function x(x,y,...rest){
    console.log(x);
    console.log(y);
    console.log(rest);
}
x(1,2,3,4,5,6,7,8);
// 结果
// 1
// 2
// [3, 4, 5, 6, 7, 8]
```

## 4.2、 变量的作用域

> 在JavaScript中， var 定义的变量实际是有作用域的
>
> 假设在函数体中声明，则在函数体外不可以使用

```javascript
function abs(x){
    var a = 1;
}
a = a + 2;//Uncaught ReferenceError: a is not defined
```

> 如果两个函数使用了相同的变量名，只要在函数内部，就不冲突

```javascript
function abs(){
    var x = 1;
    x = x +1;
}
function ab(){
    var x ="a";
    x = x + 1;
}
```

> 内部函数可以访问外部函数的成员，反之则不行

```javascript
function abs(x) {
    var a = 1;
    function f(a) {
        var b =a+1;
    }
    var c = b+1;//Uncaught ReferenceError: b is not defined
}
```

> 假设，内部函数变量和外部函数的变量重名

```javascript
function abs(x){
    var a = 1;
    function ab(y){
        var a ="A";
        console.log("内部"+a);
    }
    console.log("外部"+a);//输出外部1
}
```

> 假设在JavaScript中函数查找变量从自身函数开始~，由"内"向"外"查找.假设外部存在这个同名的函数变量，则内部函数会屏蔽外部函数的变量。
>
> 替提升变量的作用域

```javascript
function yw(){
    var x = "x" + y;
    console.log(x);
    var y = 'y';
}
//结果 x is not defined
```

> 说明:js执行引擎  自动提升了y的声明 但是不会提升y的赋值

```javascript
function yw2(){
    var y;
    var x = "X" + y;
    consloe.log(x);
    y = 'y';
}
```

> 这是在JavaScript语言建立之初就存在的特性,养成规范,所有变量定义都放在头部,不要乱放便于代码的维护

```javascript
function y(){
    var x,y = 2,g,hs;
}
```

> 全局变量

```javascript
var a = 1；
function f(){
    console.log(a);
}
f();
console.log(a);
```

> 全局对象 window

> JavaScript实际上只有一个全局作用域，任何变量（函数也可以视为变量），假设没有函数作用范围内找到，就会向外查找，如果全集作用域都没有找到，就报错

```javascript
var x = "xxx";
alert(x);
alert(window.x);//默认所有的全局变量都会自动绑定在window对象上
```

> alter本身也是window下的

```javascript
var x = "xxx";
window.alert(x);
var older_alert = window.alert;
older_alert(x);
window.alert(x);
var i = alert;
i(x);
```

> 规范

> 由于所有的全局变量都会绑定到我们的window 上，如果不同的js文件，使用了相同的全局变量，冲突如何减少冲突

```javascript
//唯一全局变量
var summerApp={};
//定义全局变量
summerApp.name = "王小姐";
summerApp.age = function (a,b){
    return a + b;
}
```

> let关键字，解决局部作用域冲突问题

```javascript
function ab(){
    for(var i = 0;i<100;i++){
        console.log(i);
    }
    console.log(i+1);//问题 i出了作用域还可以使用  
}

function ab(){
    for(let i = 0;i<100;i++){
        console.log(i);
    }
    console.log(i+1);//报错 建议let 
}
```

> 怎么定义常量：只有用全部大写字母命名的变量就是常量，建议不要修改这样的值

```javascript
var PI = 3.14
console.log(PI);
PI = 123;//可以修改
console.log(PI);
```

> 在ES6引入了常量关键字 const

```javascript
const PI = 3.14; //只读变量
PI = 123;//报错 Attempt to assign to const or readonly variable
console.log(PI);
```

## 4.3 、方法

> 方法的定义

```javascript
var summer = {
    name: "yuwei",
    birth: 2000,
    age: function () {
        return new Date().getFullYear() - this.birth;
    }
}
console.log(summer.age());
```

> this代表什么?

```javascript
function getAge(){
    return new Date().getFullYear() - this.birth;
    //如果由summer去调用的话  this指向的是summer对象
    //如果直接调用getAge方法那么this指向的是window对象
}

var summer = {
    name: "yuwei",
    birth: 2000,
    age: getAge
};
console.log(summer.age());
```

> apply   在JavaScript中可以改变this的指向    
>
> 有两个参数 
>
> 第一个参数为指向那个对象  
>
> 第二个参数为一个数组
>
> 具体可以去:https://www.w3school.com.cn/js/js_function_apply.asp

```js
function getAge(){
    return new Date().getFullYear() - this.birth;
}
var summer = {
    name: "yuwei",
    birth: 2000,
    age: getAge
};
getAge().apply(summer,[]);
//狂神写的没报错
//我写的报错:getAge(...).apply is not a function
//代码肯定没问题  可能浏览器兼容性的问题
```

```js
var person = {
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
};
var person1 = {
    firstName: "Bill",
    lastName: "Gates",
};
person.fullName.apply(person1);  
//结果'Bill Gates'
```

# 5、 内部对象

## 5.1、 Date

> 标准对象

```javascript
data = new Date();
Tue Nov 09 2021 19:05:23 GMT+0800 (中国标准时间)
data.getDate()//日
9
data.getDay()//星期几
2
data.getFullYear()//年
2021
data.getHours()//时
19
data.getMinutes()//分
5
data.getMonth()//月
10//从00-11
data.getSeconds()//秒
23
time = data.getTime();//时间戳  1970:01:01 00:00:00 开始计算的毫秒数  世界统一
1636455923897
new Date(time)
Tue Nov 09 2021 19:05:23 GMT+0800 (中国标准时间)
```

转换

```javascript
data = new Date();
Tue Nov 09 2021 19:05:23 GMT+0800 (中国标准时间)
data.toLocaleString()
"2021/11/9 下午7:05:23"
```

## 5.2、 JSON

早期，所有的数据传输习惯都使用XML文件！

- JSON（JavaScript Object Notation，JS对象简谱）是一种轻量级的数据交换格式。
- 简洁和清晰的==层次结构==使得JSON成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地==提升网络传输效率==。

在JavaScript 中 一切皆为对象，任何js支持的类型都可以用JSON来表示

格式：

- 对象都用{}
- 数组都用[]
- 所有的键值对都用key:value

```js
var summer = {
    name: "于伟",
    age: 22,
    sex: "男"
};
console.log(summer);
//{name: '于伟', age: 22, sex: '男'}
//age: 22
//name: "于伟"
//sex: "男"
//[[Prototype]]: Object
console.log(JSON.stringify(summer));
//{"name":"于伟","age":22,"sex":"男"}
```

JSON字符串和JS对象的转换

```javascript
var user = {
    name: '王小姐',
    age: 16,
    sex: '女'
}
let jsonUser = JSON.stringify(user);
let json = '{"name":"王小姐","age":16,"sex":"女"}';
let parse = JSON.parse(json);
```

JSON和JS对象的区别

```javascript
var obj = {name:"王小姐",age:16};
var json = '{"name":"王小姐","age":"16"}';
```

## 5.3、 Ajax

- 原生的js写法 xhr：异步请求 
- jQuery 封装好的方法 $(“#name”).ajax(“”)
- axios 请求

# 6、 面向对象编程

## 6.1、 什么是面向对象

javascript、java、c#都有面向对象

javascript有区别！

- 类：模板
- 对象：具体的实例

在javascript这个需要大家换一下思维方式！

原型：

```javascript
var user = {
    name: "ding",
    age: "3",
    run: function () {
        console.log(this.name + "run.....")
    }
};
// 原型对象
var xiaoming = {
    name: "xiaoming"
};
var Bird = {
    fly: function () {
        console.log(this.name+ "fly....")
    }
}
// 小明的原型 是user
xiaoming.__proto__ = user;
```

```javascript
function Student(name) {
    this.name = name;
}
// 给student新增一个方法
Student.prototype.hello = function () {
        alert('Hello')
}
```

> class继承

class关键字，是在ES6引入的

```javascript
class Student{
    constructor(name){
        this.name = name;
    }
    hello(){
        alert('hello')
    }
}
```

```javascript
//ES6之后====
//定义一个学生的类
class Student{
    constructor(name){
        this.name = name;
    }
    hello(){
        alert('hello')
    }
}
class XiaoStudent extends Student{
    constructor(name,grade) {
        super(name);
        this.grade = grade;
    }
    mygrade() {
        alert('我是一名小学生')
    }
}
var xiaoming = new Student("xiaoming");
var xiaohong = new Student("xiaohong");
```

本质：查看对象原型

> 原型链

![image-20220519152023677](C:\Users\SummerDeBaby\AppData\Roaming\Typora\typora-user-images\image-20220519152023677.png)

# 7、 操作BOM对象（重点）

> 浏览器介绍

JavaScript和浏览器的关系

javascript诞生就是为了能够让他在浏览器中运行

B浏览器 O object M model

BOM：浏览器对象模型 

- IE 6~11
- Chrome   Windows
- Safari     Apple
- FireFox  Linux
- Opera

> **第三方**

- QQ游览器
- 360

> window

window 代表游览器窗口

```javascript
window.alert(1);
window.innerHeight;//内部高度
window.innerwidth;//内部宽度
window.outerHeight;//外部高度
window.outerWidth;//外部宽度
//大家可以调整游览器窗口
```

> Navigator 不建议使用

Navigator，封装了浏览器的信息

```javascript
navigator.appName;
//'Netscape'
navigator.appVersioin;
//undefined
navigator.userAgent;
//'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.67 Safari/537.36'
navigator.platform;
//'Win32'
```

大多数时候，我们不会使用`navigator`对象，因为会被人为修改~

不建议使用这些属性来判断和编写代码

> screen

```javascript
screen.width
screen.height
//代表屏幕尺寸
```

> location(重要)

location 代表当前页面的URL信息

```javascript
host: "www.baidu.com"
href: "https://www.baidu.com/"
protocol: "https"
reloadLf reload() //刷新网页
// 设置新的地址
location.assign('新的网站')
```

> document ()

document 当前的页面，HTML DOM文档树

```javascript
document.title
document.title='ding'
```

获取具体的文档树节点

```html
<body>
<dl id="app">
    <dt>java</dt>
    <dd>python</dd>
    <dd>ding</dd>
</dl>
</body>
<script>
    var dl = document.getElementById('app');
</script>
```

获取cookie

```javascript
document.cookie;
```

劫持cookie原理

```html
<script src="aa.js"></script>
<!--恶意人员：获取你的cookie上传到他的服务器 -->
```

服务器端可以设置cookie：httpOnly

> history  不建议使用

```javascript
history.back() //后退
history.forward() //前进
```

# 8、 操作DOM对象（重点）

> 核心

DOM文档对象模型  Doucment Object Model

浏览器网页就是一个DOM树形结构！

- 更新：更新Dom节点
- 遍历Dom节点：得到Dom节点
- 删除：删除一个Dom节点
- 添加：添加一个新的节点

要操作一个Dom节点，就必须先获得这个Dom节点

> 获得Dom节点

```javascript
//对应 css选择器
var h1 = doucment.getElementByTagName('h1');
var p1 = doucment.getElementById('p1');
var p2 = doucment.getElementByClassName('p2');
var father = doucment.getElementById('father');
//获取父节点下所有的子节点
var childrens = father.children;
//father.firstChild
//father.lastChild
```

这是原生代码，之后会使用jQuery();

> 更新节点

```html
<div id="id1"></div>
<script>let id1 = document.getElementById('id1');</script>
```

```js
//js操作文本
id1.innerText = "231";//给标签赋值
id1.innerHTML = "<strong>213</strong>";//可以识别标签
//js操作css
id1.style.color = 'yellow';//属性使用字符串包裹起来
id1.style.fontSize = '20px';//在js中使用驼峰命名法
id1.style.padding = '2em';
```

> 删除节点

删除节点的步骤： 先获取父节点， 再通过父节点删除自己

```html
<div id="father">
    <h1>标题1</h1>
    <p id="p1">p1</p>
    <p2 class="p2">p2</p2>
</div>
<script>
    var self = document.get ElementById('p1');
    var father = p1.parentElement;
    father.removeChild(self)
    //删除是一个动态的过程
    father.removeChild(father.children[0])
    father.removeChild(father.children[1])
    father.removeChild(father.children[2])
</script>
```

注意： 删除多个节点的时候，children时时刻刻是变化的，删除节点的时候一定要注意！利用下标的话需要注意

> 插入节点      ==appendChild==

我们获得了某个DOM节点，假设DOM节点是空的，我们通过innerHTML就可以增加一个元素了，但是这个DOM节点已经存在元素，我们就不能这样做了，因为会覆盖。

追加

```html
<p id="js">biancheng</p>
<div id="list">
    <p id="se">javascript</p>
	<p id="ee">java</p>
	<p id="py">python</p>
</div>
<script>
    var js = document.getElementById('js');
    var list = document.getElementById('list');
    list.appendChild(js); //追加到后面
</script>
```

> 创建一个新的标签，实现插入     ==createElement==

```javascript
var js = document.getElementById('js');   //已经存在的节点
var list = document.getElementById('list');
// 通过js 创建一个新的节点
var newp = document.createElement('p'); //创建一个P标签
newp.id = 'newp';
newp.innerText = 'hello,ding';
list.appendChild(newp)

// 可以创建一个Style标签
var mystyle= document.createElement('style');//创建一个标签
mystyle.setAttribute('type','text/css');//给该创建的标签设置属性
mystyle.innerHTML = 'body{background-color: #ffeb3b}'; //给style标签怎么增加内容
document.getElementsByTagName('head')[0].appendChild(mystyle)//再将这个style标签移到head下面去
```

> insertBefore  将一个元素插在另一个元素的前面

```html
<p id="js">biancheng</p>
<div id="list">
    <p id="se">javascript</p>
	<p id="ee">java</p>
	<p id="py">python</p>
</div>
<script>
var ee = document.getElementById('ee');
var py = document.getElementById('py');
var list = document.getElementById('list');
//要包含的节点，insertBefore（new，target）
list.insertBefore(py,ee);//将list中的py插入到ee的前面
</script>
```

# 9、 操作表单（验证）

> 表单是什么 form DOM树

- 文本框 text
- 下拉框 select
- 单选框 radio
- 多选框 checkbox
- 隐藏域 hidden
- 密码框 password
- …

表单的目的：提交信息

> 获得要提交的信息

```html
<form action="post">
    <p>
        <span>用户名：</span><input type="text" id="username">
    </p>
    <!--多选框的值，就是定义好的-->
    <p>
        <span>性别：</span>
        <input type="radio" name="sex" value="boy" id="boy"> 男
        <input type="radio" name="sex" value="girl" id="girl"> 女
    </p>
</form>
<script>
    var input_text = document.getElementById('username');
    var boy_radio = document.getElementById('boy');
    var girl_radio = document.getElementById('girl');
    //得到输入框的值
    //修改输入框的值
    input_text.value = '123';
    //对于单选框，多选框等等固定的值，boy_radio.value 只能取到当前的值
    boy_radio.checked; //查看返回的结果，是否为true，如果为true 则这个被选中
    girl_radio.checked = true;
</script>
```

> 提交表单 md5加密密码，表单优化
>
> MD5工具类链接  https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js

```html
<div>
<!--
表单绑定提交事件
onsubmit = 绑定一个提交检测的函数
true,false
讲这个结果返回给表单，使用onsubmit接收！
-->
    <form action="https://www.baidu.com" method="post" onsubmit="return submitMsg()">
        <p>
            <span>用户名</span><input type="text" id="username" name="username">
        </p>
        <p>
            <span>密码</span><input type="password" id="password">
        </p>
        <!-- onclick 被点击  可以写一个函数 就可以形成一个点击事件-->
        <button type="button" onclick="">提交</button>
        <p>
            <span></span><input type="password" id="pwd" name="password" hidden="hidden">
        </p>
        <p>
            <button type="submit" id="but">提交</button>
        </p>
    </form>
</div>
<script>
    function submitMsg() {
        let username = document.getElementById('username').value;
        let password = document.getElementById('password').value;
        if (username.length < 6 || password.length < 6) {
            window.alert("用户名或密码不符合规范");
            return false
        } else {
            password = "别抓了，看不到的,哈哈哈哈哈";
            document.getElementById('pwd').value = password;
            return true
        }
    }
</script>
```

# 10、 jQuery

javascript

jquery库，里面存在大量的javascript函数

> 获取jquery      https://www.bootcdn.cn/jquery/

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
</head>
<body>
</body>
</html>
```

> 使用jquery  

```html
<a href="" id="test-jquery">点我</a>
<script>
    //公式: $(selector).action()   $(css选择器).事件()
    //选择器就是css选择器
    $('#test-jquery').click(function () {
        alert('hello,jquery')
    })
</script>
```

> 选择器

```javascript
//原生态js，选择器少，麻烦不好记
document.getElementsByTagName();//标签
document.getElementById();//id
document.getElementsByClassName();//类
//jquery css 中的选择器他全部都能用
$('p').click(); //标签选择器
$('#id').click(); //id选择器
$('.class1').click(); //class选择器
//公式：$(selector).action()
```

教你怎么使用jquery有代码:  https://jquery.cuishifeng.cn/

> 事件

鼠标事件，键盘事件，其他事件

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
        <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
    <style>
        #divMove{
            width: 500px;
            height: 500px;
            border: 1px solid red;
        }
    </style>
</head>
<body>
<!-- 要求：获取鼠标当前的一个坐标-->
mouse:  <span id="mouseMove"></span>
<div id="divMove">
    在这里移动鼠标试试
</div>
<script>
    //当网页元素加载完毕之后，响应事件
    //$(document).ready(function(){           		$(function(){
        						   //缩写->>
    //});                                    		    });
    $(function () {
        $('#divMove').mousemove(function (e) {
                $('#mouseMove').text('x:'+e.pageX + 'y:'+e.pageY)
        })
    })
</script>
</body>
```

> 操作DOM

节点文本操作

```javascript
$('#test-ul li[name=python]').text();    //获得值
$('#test-ul li[name=python]').text('设置值');//设置值
$('#test-ul').html();    //获得值
$('#test-ul').html('<strong>123</strong>');  //设置值
```

css的操作

```javascript
$('#test-ul li[name=python]').css("color","red")
```

元素的显示和隐藏,本质==display：none==；

```javascript
$('#test-ul li[name=python]').show()
$('#test-ul li[name=python]').hide()
```

娱乐测试

```javascript
$(window).width()
$(window).height()
$('#test-ul li[name=python]').toggle();
```

> 小技巧

1、巩固JS（看JQuery源码   看游戏源码）

2、巩固HTML，CSS（扒网站，全部down下来，然后对应修改看效果~）

Layer弹窗组件

Element-ui