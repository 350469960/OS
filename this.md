---
title: JavaScript中的this关键字的用法和注意点
layout: page
date: 2017-02-17
modifiedOn: 2017-02-17
category: oop
---
## JavaScript中的this关键字的用法和注意点
### 一、this关键字的用法
#### this一般用于指向对象(绑定对象);
##### 01、在普通函数调用中，其内部的this指向全局对象(window);
function funcName(){
    this.name = 'Jack';
    console.log(this);// Window
}
funcName();
console.log(window.name);// Jack
console.log(this.name);// Jack
上述代码先声明一个函数名为funcName的函数，给函数内部的对象添加属性name,并打印this的指向(window)，分别打印this的属性值和全局对象(window)的属性值，二者相等说明this指向全局对象(window);

##### 02、在构造函数调用中，其内部的this指向新创建的对象
function Person() {
    this.name = '郭嘉';
    console.log(this);// Person {name: "郭嘉"}
}
var p1 = new Person();
console.log(p1.name); // 郭嘉
console.log(window.name);// 空
上述代码先声明一个函数名为Person的构造函数，给构造函数内部的对象添加属性name,并打印this的指向(新创建的对象：Person {name: "郭嘉"})，用Person构造函数实例化一个对象p1,分别打印p1的属性值和全局对象(window)的属性值，二者不相等说明this指向的是新创建的对象(Person {name: "郭嘉"})而不是全局对象(window);

补充：使用new关键字创建构造函数的实例对象的系统内部流程
/*用new关键字创建构造函数的实例的系统内部流程*/
//001 使用new 关键字在内部默认创建一个对象
//002 在构造函数内部,把默认创建的对象赋值给this
//003 通过this来设置对象的属性和方法
//004 默认把新创建的对象返回给我们
function Person() {
    //var this = new Object();//使用new 关键字在内部默认创建一个对象，并在构造函数内部,把默认创建的对象赋值给this
    this.name = '郭嘉';//通过this来设置对象的属性和方法
    console.log(this);// Person {name: "郭嘉"}
    //return this;// 默认把新创建的对象返回给我们
}

##### 03、在对象的方法调用中，其内部的this指向调用的对象本身
//以对象的方法来进行调用
var obj = {
    name:"张三", // 对象的属性
    getName:function () { // 对象的方法
        console.log(this);// Object {name: "张三"}
    }
};
obj.getName();// 对象的方法调用
console.log(obj.name); // 张三
console.log(window.name);// 空
上述代码先声明一个对象名为obj的对象，给对象添加属性name,和方法getName,并在方法内部打印this的指向(对象本身：Object {name: "张三"}),调用对象的方法,打印this的指向是对象本身(Object {name: "张三"})而不是全局对象(window);

##### 04、在事件处理中，其内部的this指向产生这个事件源的对象
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<button id='btn'>click</button>
<script>
   var btn = document.getElementById('btn');// 获取id为btn的标签
   btn.onclick = function () {// 按钮点击事件
       console.log(this);
// <button id='btn'>click</button>
   };
</script>
</body>
</html>
上述代码先用html创建一个按钮，再给这个按钮添加一个点击事件，并打印this的指向(<button id =”btn”>click</button>),得出结论是this指向产生事件源的对象(<button id =”btn”>click</button>);

##### 05、在定时器中其内部的this指针指向window对象
function Person() {
    // 备份指针
    console.log(this); // Person {}
    var self = this;
    setInterval(function () {
        console.log(self);// Person {}
        console.log(this);// Window
    }, 20);
}
new Person();
上述代码先创建一个person函数，再用self来备份一下this指针，再创建定时器，定时器内部的this永远指针指向window对象，有时候我们我们要用到指定的对象不是window对象，我们可以通过备份指针来实现；

##### 06、通过call或者apply方式调用中(函数上下文),this指向的是当前的上下文，主要是改变this的指向
call方法: 传入的参数数量不固定
第一个参数是要绑定给this的值(即函数体内this对象的指向)
第二个参数开始往后,每个参数都依次的传入作为函数的参数

apply: 接收两个参数
第一个参数是要绑定给this的值(即函数体内this对象的指向)
第二个就是一个参数数组
function Person() {
    this.name = 'Jack';
}
function Boy() {
    Person.call(this);
    console.log(this);// Boy {name: "Jack"}
}
var b1 = new Boy();
上述代码创建两个构造函数Person和Boy，使用call方法让Person构造函数中的this指向Boy构造函数，相当于把Person构造函数内对象的属性和方法深拷贝一份(包括引用类型：指针和堆空间的数据)

var obj = {
    name:"张三",
    getName:function (param1,param2) {
        console.log(this.name,param1,param2);
    }
}

obj.getName("123","456");      //张三 123 456

var obj2 = {name:"李四"};
obj.getName.call(obj2); //打印李四 undefined undefined

obj.getName.call(obj2,"测试字符串01","测试的字符串02"); // 李四 测试字符串01 测试的字符串02
obj.getName.apply(obj2,["123","456"]);   // 李四 123 456
上述代码先创建一个对象obj并添加属性name和方法getName,调用自身的方法得出的结果(张三 123 456),再创建一个对象obj2,添加属性name,obj.getName.call(obj2);这句话的意思是obj的方法getName借给obj2使用，其obj方法内部的this指向obj2;得出的结果是李四 undefined(没有传参)  undefined(没有传参)；call方法主要是改变this的指向；apply的方法作用和call一样，只是传的参数不一样，call可以只传一个参数（要绑定给this的值），也可以传多个；而apply接收两个参数（要绑定给this的值，[]）,第二个参数是数组，数组的长度不定。

### 二、this关键字用法的注意点
#### This的丢失问题
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="box"></div>
<script>
 var box = document.getElementById('box');// 获取标签
 var getId = document.getElementById;//
 var box = getId('box');// Uncaught TypeError: Illegal invocation (未捕获的类型错误)
 console.log(box);
</script>
</body>
</html>
上述代码先在html中创建一个id为box的div，然后再js中获取标签，有时候我们嫌获取标签的方法太长了，想要缩减一下(var getId = document.getElementById)，再调用，然而上述代码调用时则报错了！原因是方法内部的this指针丢失了，原先document.getElementById内部实现中用到this指针，其指向document对象，而getId调用时是按普通函数调用的，其内部的this指针指向window对象，所以执行到这一步的时候报错！

现在用apply方法来修正this的指向
//借用apply来修正this
document.getElementById = (function (func) {
    return function () {
        return func.apply(document,arguments);
    }
})(document.getElementById);
var getId = document.getElementById;
/*上句代码等同于下面这句代码
var getId = function () {
    return document.getElementById.apply(document,arguments);
}*/
var box = getId('box');       //不会报错
console.log(box);// <div id="box"></div>
上述代码先在document.getElementById方法内部添加一个让this指针一直指向document对象的操作;document.getElementById =(function(func){})(document.getElementById);这是一个立即执行函数，把document.getElementById当作实参传进去，而函数内部则返回另一个函数，而这个函数内部则执行改变this指向的操作document.getElementById.apply(document,arguments);意思是让this永远指向document对象；arguments指的是传进去的实参Id(box);

### 三、结论
#### This关键字永远指向实际调用者


