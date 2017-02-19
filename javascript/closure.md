---
title: JavaScript中闭包的作用和简单的用法
layout: page
date: 2017-02-18
modifiedOn: 2017-02-18
category: javascript
---
# 在JavaScript中闭包的作用和简单的用法

## 一、闭包的简介

     作用域链:在js中只有函数有作用域的概念，由于函数内能访问函数外部的数据，而函数外部不能访问函数内部的数据，由上述形成一种作用域访问的链式结构叫做作用域链。

     闭包：通过某种方式实现的一个封闭的、包裹的对外不公开的结构|空间，有人称之为封闭空间，以及匿名函数自调。闭包可以使函数外部访问函数内部的数据。

## 二、闭包的基本写法
     1、通过return来访问函数内部的变量
`(function(){var a = 10; return 变量 | 函数 });`

     2、通过立即执行函数访问函数内部的变量
        01、 (function(){})();// 常用
        02、 (function(){}());// 常用
        03、 ;function(){}();
        04、 +function(){}();
        05、 -function(){}();


## 三、闭包的作用
      1、闭包的作用

         01、提供一种间接访问函数内部变量的方法
         02、延长函数内部的局部变量的生命周期(慎用！局部变量过多会占大量内存)
         03、全局变量私有化，减少全局变量污染
         04、函数局部变量在函数执行完后释放内存(函数执行完毕，其内部的变量立即销毁)
         05、更新复杂变量(可以对变量进行校验和判断，保证安全性和稳定性)

      2、代码示例

        01、提供一种间接访问函数内部变量的方法

```script
 function A() {
    var num = 10;// 局部变量
    return num;// 访问内部数据
}
var B1 = A();
console.log(B1);// 10   说明局部变量没有销毁   延长函数内部的局部变量的生命周期
 ```
上述代码可以在外部访问函数A内部的局部变量，并延长局部变量num的生命周期，但也有缺陷，每次访问都是一次性的，无法修改保存其变量如下代码所示：

 ```script
function foo() {
    var obj = {
        name:"张三",
        age:28
    };
    return obj;
}
var obj1 = foo();
var obj2 = foo();
console.log(obj1 == obj2);  //false
```

上述代码所示两次访问的对象不是同一个`console.log(obj1 == obj2); // false`！

        03、全局变量私有化，减少全局变量污染

```script
(function () {
    window.onload = function () {// 全局变量私有化

    };
})();
```

```script
(function () {
    var d = document;
    var btn1 = d.getElementById('btn');
    var btn2 = d.getElementById('btn');
    var btn3 = d.getElementById('btn');
})();
```
        05、更新复杂变量(可以对变量进行校验和判断，保证安全性和稳定性)

        001、设置数据

```script
function test() {
    var str1 = '字符串1';
    return function (str) {
        str1 = str; // 设置数据
        return str1;
    }
}
var test1 = test();
console.log(test1('hello')); // hello
console.log(test1()); // undefined
```

上述代码是设置数据，但并没有判断数据，由于函数调用时没有传参，所以结果是`console.log(test1()); // undefined`,我们还可以在其内部添加校验和判断，代码如下：

```script
function test() {
    var str1 = '字符串1';
    return function (str) {
        if(typeof str == 'string'){// 判断传参是不是string类型
            str1 = str; // 设置数据
        }
        return str1;
    }
}
var test1 = test();
console.log(test1('hello')); // hello
console.log(test1()); // undefined
```

        002、返回多个数据

        - 以数组的形式返回

```script
function person() {
    var name = "黄忠";
    var age = 45;
    return [function () {//以数组的形式返回多个数据
        return name;
    },
        function () {
            return age;
        }];
}
var p1 = person();
console.log(p1[0]()); // 黄忠
console.log(p1[1]()); // 45
```

一般不怎么以数组的形式返回数据！

        - 以对象的形式返回

```script
function person() {
    var name = "黄忠";
    var age = 45;
    return {
        getName:function () {//以对象的形式返回多个数据
            return name;
        },
        getAge:function () {
            return age;
        }
    }
}
var p1 = person();
console.log(p1.getName()); // 黄忠
console.log(p1.getAge()); // 45
```

我们一般以这种形式返回多个数据！

        003、以对象形式返回多个数据并设置多个数据

```script
function person() {
    var name = "黄忠";
    var age = 45;
    return {
        getName:function () {//以对象的形式返回多个数据
            return name;
        },
        getAge:function () {
            return age;
        },
        setName:function (value) {
            //容错性处理 + 逻辑校验
            //判断
            if (typeof value != 'string')
            {
                throw "该函数只接受字符串类型的参数";
            }
            name = value;
        },
        setAge:function (num) {
            //容错性处理 + 逻辑校验
            //判断
            //console.log(num == NaN);// false
            //var str = String(num);// 把获取的实参转为字符串
            //var str1 = String(NaN);// 把NaN转为字符串
            //if ((str == str1) || typeof num != 'number' )
            //{//判断输入的是否为数字类型并且不为NaN
            //    throw "该函数只接受数字类型的参数并且不为NaN";
            //}
            //parseInt(num)数字为true，其他为false
            if (!(parseInt(num) || typeof num != 'number' )
            {//parseInt(num)在会截取数字开头的字符串转为数字 || 所以还要判断类型
               throw "该函数只接受数字类型的参数并且不为NaN";
            }
            age = num;
        }
    }
}
var p1 = person();
console.log(p1.getName()); // 黄忠
console.log(p1.getAge()); // 45
p1.setName('郭嘉');// 修改名字
p1.setAge(28);// 修改年龄
console.log(p1.getName()); // 郭嘉
console.log(p1.getAge()); // 28
```

上述代码在返回的对象中多添加了`setName`方法和`setAge`方法,并在方法内部做了容错处理和校验判断，这样使代码的安全性和稳定性更好！

## 四、闭包的应用场景
      1、for循环中
For循环中，for循环执行的速率非常快，有时候，我们希望保存变量i，用于其他地方。我们就可以用到闭包！代码如下：

```script
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<button>第一个</button>
<button>第二个</button>
<button>第三个</button>
<button>第四个</button>
<button>第五个</button>
<script>
    var btns = document.getElementsByTagName('button');
    //遍历按钮
    for(var i= 0;i < btns.length;i++){
        (function (a) {// 保存在形参中
            btns[a].onclick = function () {// 按钮点击事件  每点击对应的按钮弹出对应的数字
                alert(a+1);
            }
        })(i) ;// 把i当作实参传进去
    }
    console.log(i);// 5 //在js中只有函数有作用域的概念
</script>
</body>
</html>
```

但上述代码中变量i是全局变量会影响其他的for循环，我们也可以用闭包把for循环包起来，防止全局变量污染！代码如下：

```script
var btns = document.getElementsByTagName('button');
//遍历按钮
(function () {
    for(var i= 0;i < btns.length;i++){
        (function (a) {// 保存在形参中
            btns[a].onclick = function () {// 按钮点击事件  每点击对应的按钮弹出对应的数字
                alert(a+1);
            }
        })(i) ;// 把i当作实参传进去
    }
})();
console.log(i);// Uncaught ReferenceError: i is not defined // i 未定义
```

上述代码把for循环包起来，就把全局变量私有化了，外面就无法访问了，所以报错`未定义`。

    2、在for循环中处理事件机制时我们经常用到闭包！

```script
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<button>第一个</button>
<button>第二个</button>
<button>第三个</button>
<button>第四个</button>
<button>第五个</button>
<script>
    var btns = document.getElementsByTagName('button');
    //遍历按钮
    for(var i= 0;i < btns.length;i++){
        btns[i].onclick = (function (a) {
            return function () {
                alert(a+1);
            }
        })(i);
    }
</script>
</body>
</html>
```
    3、在定时器中使用闭包

```script
for (var i = 0; i < 10; i++) {
    setTimeout((function (j) {
        return function () {
            console.log(j);
        }
    })(i),1000);
}
```

## 五、结论

闭包的原理:变量的访问原则(即上一级的作用域无法访问下一级的作用域),其实函数本身就是闭包
