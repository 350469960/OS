## 在javascript中关于变量与函数的提升
### 一、简介
在javascript中声明变量与函数的执行步骤:
1、先预解析变量或函数声明代码，会把用var声明的变量或者函数声明的代码块进行提升操作
2、然后再进行执行操作

关于变量声明提升:用var声明的变量提升,其只是变量提升了，而没有进行赋值的提升

### 二、关于变量与函数提升的注意点
1、变量提升的代码演示
```script
var a = 10;
console.log(a);//10
console.log(b);// undefined
var b = 20;
console.log(b);//20

/*变量提升演示*/
var a; // 变量提升  只提升不赋值
var b;// 变量提升  只提升不赋值
a = 10; // 赋值操作
console.log(a);// 10
console.log(b);// 由于b还没有赋值，所以 undefined
b = 20; // 赋值操作
console.log(b);// 20
```


2、两函数名相同的情况下，后面的函数声明会覆盖前面的函数声明
```script
var a = 10;
var b = 20;
 function funcName() {
     console.log(a);
 }

 function funcName(){
     console.log(b);
 }
 funcName();// 20

    /*变量与函数提升演示*/
    var a;
    var b;
    function funcName(){//后面的覆盖了前面的函数声明
        console.log(b);
    }
    a = 10;
    b = 20;
    funcName(); // 20
```


3、变量名与函数名相同的情况下，只会提升函数声明，而不提升变量声明
```script
var a = 10;
 function funcName() {
     console.log(a);
 }
funcName();// 10
var funcName = '字符串';
 console.log(funcName);// 字符串
funcName();// Uncaught TypeError:funcName is not a function

    /*变量与函数提升演示*/
var a;
function funcName() {
    console.log(a);
}
a = 10;
funcName();// 10
var funcName = '字符串';// 与函数同名变量声明忽略提升
console.log(funcName);//  字符串
//由于重新给funcName赋值，funcName是一个字符串了，而不是函数，无法调用
console.log(typeof funcName);// string
funcName();// Uncaught TypeError:funcName is not a function
```

4、用函数表达式声明函数时，函数声明提升，只会提升变量，而不是整个函数提升
```script
say()//Uncaught TypeError: say is not a function
var say = function() {
    console.log('hello');
};

/*函数声明演示*/
var say;// 函数表达式声明函数，只提升变量
say();// Uncaught TypeError: say is not a function
say = function () {
    console.log('hello');
};
```

5、变量与函数的提升是分作用域的
```script
function sayHello() {
    console.log('hello');// hello
    function sayHi() {
       console.log(a); // 10
       console.log(b); // undefined
    }
    sayHi();
    var b = 10;
}
var a = 10;
sayHello();
console.log(b);// Uncaught ReferenceError: b is not defined

/*函数与变量提升演示*/
function sayHello() {
    function sayHi() {
        console.log(a); // 10 a是全局变量，函数内部可以访问
        console.log(b); // undefined
    }
    var b;
    console.log('hello');// hello
    sayHi();//调用时b还没有赋值，所以undefined
    b = 10;
}
var a;
a = 10;
sayHello();// 函数调用
// b 是局部变量，函数之外无法访问
console.log(b);// Uncaught ReferenceError: b is not defined
```

### 三、关于函数提升与变量提升的测试题
第一题
```script
function show() {
    var a = 123;
    console.log(a); //?
}
show();
console.log(a); //?
```

第二题
```script
var str = "string";
show();
function show() {
    console.log(str); //？
    var str = "字符串";
    console.log(str); //？
}
```

第三题
```script
if("a" in window){
    var a = 10;
}
console.log(a); // ?
```

第四题
```script
function show(){
    if("a" in window){
        var a = 10;
    }
    console.log(a); // ?
}
show();
```

第五题
```script
var a = 1;
function show() {
    if(!a)
    {
        var a = 10;
    }
    console.log(a); //?
}
show();
```

第六题
```script
function Foo() {
    getName = function(){ alert(1); };
    return this;
}
Foo.getName = function() { alert(2); };
Foo.prototype.getName = function(){ alert(3); };
var getName = function() { alert(4); };
function getName(){ alert(5); }

Foo.getName(); // ?
getName(); // ?
Foo().getName(); // ?
getName(); // ?
new Foo.getName(); // ?
new Foo().getName(); // ?
new new Foo().getName(); // ?
```

### 、四 答案

第一题
```script
function show() {
    var a = 123;
    console.log(a); //123
}
show();
console.log(a); //Uncaught ReferenceError: a is not defined(未定义)
/*变量提升演示*/
function show() {
    var a;
    a = 123;
    console.log(a); //123
}
show();
console.log(a); //Uncaught ReferenceError: a is not defined(未定义)
```

第二题
```script
var str = "string";
show();
function show() {
    console.log(str); //undefined
    var str = "字符串";
    console.log(str); //字符串
}
/*变量提升演示*/
var str;
function show() {
    var str;
    console.log(str); //undefined
    str = "字符串";
    console.log(str); //字符串
}
str = "string";
show();
```

第三题
```script
if("a" in window){
    var a = 10;
}
console.log(a); // 10
/*变量提升演示*/
var a;// 全局变量
if("a" in window){ // true
    a = 10;
}
console.log(a); // 10
```

第四题
```script
function show(){
    if("a" in window){
        var a = 10;
    }
    console.log(a); // undefined
}
show();
/*变量提升演示*/
function show(){
    var a;// 局部变量 不是全局变量
    if("a" in window){// false
        a = 10;
    }
    console.log(a); // undefined
}
show();
```

第五题
```script
    var a = 1;
    function show() {
        if(!a)
        {
            var a = 10;
        }
        console.log(a); // 10
    }
    show();

    /*变量提升演示*/
var a;
function show() {
    var a;
    if(!a)// Blooean(undefined) == false  !a == true
    {
        a = 10;
    }
    console.log(a); // 10
}
a = 1;
show();
```

第六题
```script
function Foo() {
    getName = function(){ alert(1); };
    return this;
}
Foo.getName = function() { alert(2); };
Foo.prototype.getName = function(){ alert(3); };
var getName = function() { alert(4); };
function getName(){ alert(5); }

Foo.getName(); // 2
getName(); // 4
Foo().getName(); // 1
getName(); // 1
new Foo.getName(); // 2
new Foo().getName(); // 3
new new Foo().getName(); // 3

/*函数与变量提升演示*/
function Foo() {
    getName = function(){ alert(1); };//
    return this;
}
var getName;
//function getName(){ alert(5); }//被 getName = function() { alert(4); };覆盖
Foo.getName = function() { alert(2); };//构造函数的静态方法
Foo.prototype.getName = function(){ alert(3); };// 原型方法
getName = function() { alert(4); };


Foo.getName(); // ? 2  构造函数调用自己的静态方法
//getName = function(){ alert(1); }; 覆盖 getName = function() { alert(4); };
getName(); // ? 4 window.getName();  5已经被4覆盖
Foo().getName(); // ? 1 Foo()中this指向window，方法调用把getName = function() { alert(4); };用getName = function(){ alert(1); };覆盖
getName(); // ? 1 window.getName() 就是调用getName = function(){ alert(1); };
new Foo.getName(); // ? 2 Foo.getName() 构造函数调用自己的静态方法   new 2 == 2
new Foo().getName(); // ? 3 (new Foo()).getName() 对象调用原型方法
new new Foo().getName(); // ? 3  new 3 == 3
```
