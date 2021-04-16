# 那些年，踩过的JavaScript之坑
id: javascript-pit
title: 那些年，踩过的JavaScript之坑
date: 2014-01-10 13:39:36
tags: [JavaScript,Web]
---
你编，或者不编程，项目就在那里，还未完成。你调，或者不调试，BUG就在那里，早晚得改。你踩，或者不踩，坑都在那里，等下一个受害者。

## 变量作用域

### 未使用var关键字定义的变量都是全局变量
在JavaScript中定义变量时漏掉var并不会报错。

``` javascript
function foo() {
    value = 100; // value前没有var关键字，则声明了全局变量value并赋了值
}
foo();
console.log(value); // 输出 100
```
<!-- more --> 
### 没有块作用域，只有函数作用域
``` javascript
function foo() {
    for (var i = 0; i < 10; i++) {
        var value = "hello world";
    }
    console.log(i); // 输出 10
    console.log(value); // 输出 hello world
}
foo();
console.log(value); // ReferenceError: value is not defined
```

上面的代码中可以看出，for循环外依然能访问变量i和value，但函数foo外面则无法访问。

如何优雅地绕过此坑：

1. 在函数体的顶部声明可能用到的变量 
2. 函数体内不要使用同一名称命名不同意义的变量

### 变量的优先级
下面的代码会让人觉得“匪夷所思”
``` javascript
var x = 1;
function foo() {
    console.log(x); //输出 undefined，而不是1
    var x = 100;
    console.log(x); //输出 100
}
foo();
```
WHY?
1. JavaScript 是解释型语言，但它并不是直接逐步执行的，JavaScript解析过程分为先后两个阶段，一个是预处理阶段，另外一个就是执行阶段。在预处理阶段 JavaScript解释器将完成把JavaScript脚本代码转换到字节码，然后第二阶段JavaScript解释器借助执行环境把字节码生成机械 码，并顺序执行。

2. JavaScript的变量作用域是基于其特有的作用域链的，使用变量时JavaScript是按从下往上的顺序依次查找变量，没有找到就进入上一级直至全局变量。基于这一规则，函数体内部局部变量的优先级比同名的全局变量高。另外，函数中声明的变量在函数体内都可以使用，并可以重新赋值。

因此，上面的代码同于如下代码：
``` javascript
var x = 1;
function foo() {
    var x;
    console.log(x); //输出 undefined，而不是1
    x = 100;
    console.log(x); //输出 100
}
foo();
```

### this的指向
在函数执行时，this 总是指向调用该函数的对象。要判断 this 的指向，其实就是判断 this 所在的函数属于谁。

在《javaScript语言精粹》这本书中，把 this 出现的场景分为四类，简单的说就是：

1. 有对象就指向调用对象
2. 没调用对象就指向全局对象
3. 用new构造就指向新对象
4. 通过 apply 或 call 或 bind 来改变 this 的所指。

看下面的例子：
``` javascript 
var myObject = {
    value: 100,
    getValue: function() {
        var foo = function() {
            console.log(this); // 没调用对象this指向全局对象
            //console.log(this.value); // 输出 undefined
        };

        foo();

        // 通过 apply 或 call 或 bind 来改变 this 的所指
        foo.apply(myObject); // 输出 myObject
        foo.call(myObject); // 输出 myObject
        var newFoo = foo.bind(myObject);
        newFoo(); // 输出 myObject

        var SomeClass = function() {
            this.value = 200; // 用new构造this指向新对象
        }
        var myCreate = new SomeClass();
        console.log(myCreate.value); // 输出200

        console.log(this); // this指向myObject
        return this.value;
    }
};

console.log(myObject.getValue()); // 输出 100
```

## 闭包
闭包是指有权限访问另一个函数作用域的变量的函数，创建闭包的常见方式就是在一个函数内部创建另一个函数。**只要存在调用内部函数的可能，JavaScript就需要保留被引用的函数，而且JavaScript运行时需要跟踪引用这个内部函数的所有变量，直到最后一个变量废弃，JavaScript的垃圾收集器才能释放相应的内存空间。**

``` html
<button>0</button>
<button>1</button>
<button>2</button>
<button>3</button>
<script>
var elements = document.getElementsByTagName('button');
var n = elements.length;
for (var i = 0; i < n; i++) {
    elements[i].onclick = function() {
        console.log("This is element #" + i);
    };
}
</script>
```
乍一看，上面的代码应该是点击不同按钮会输出不同的内容，但发现点击四个button都是输出“This is element # 4”，很奇怪，为什么？

理一下执行过程：

当i的值为4的时，for循环执行完毕，但是因为每个button的onclick方法这时候为内部函数，所以i被闭包引用，内存不能被销毁，i的值会一直保持4，直到程序改变它或者所有的onclick函数销毁（主动把函数赋为null或者页面卸载）时才会被回收。这样每次我们点击button的时候，onclick函数会查找i的值（作用域链是引用方式），一查等于4，然后就输出了“This is element # 4”。

解决：
``` javascript
var elements = document.getElementsByTagName('button');
var n = elements.length;
for (var i = 0; i < n; i++) {
    (function(n) {
        elements[i].onclick = function() {
            console.log("This is element #" + n);
        }
    })(i);
}
```
第二种方式是使用了一个立即执行的函数又创建了一层闭包，函数声明放在括号内就变成了表达式，后面再加上括号括号就是调用了，这时候把i当参数传入，函数立即执行，num保存每次i的值(依次是0、1、2、3)，我们点击button的时候，就会得到我们想要的效果。

## 行尾的分号

Javascript代码中分号是可选的，似乎很方便，但是很不幸的是经常会遇到因为漏掉了分号出现的错误，而解释器有时对这种错误定位不明确，必须由我们自己追溯并尝试去猜测因为哪些分号漏写导致的问题。

``` javascript
function foo1() {
    return {
        key: "value"
    };
}
console.log(foo1()); // 输出 { key: 'value' }

function foo2() {
    return 
	{
        a: "hello"
    };
}
console.log(foo2()); // 输出 undefined
```

foo2中分号自动加到了return语句后面，所以得到了“奇怪”的结果。

## 操作符

### 双等号
==操作符比较时会进行类型的强制转换，它可以比较两个不同类型的对象，在执行比较之前它将会尝试把这两个对象转换成同一个类型。

``` javascript
console.log("" == 0);   //true - 空字符串会被强制转换为数字0.  
console.log(0 == "0");  //true - 数字0会被强制转换成字符串"0"  
console.log("" == "0"); //false - 两操作数都是字符串所以不执行强制转换 
```

如果使用三重等号（===），上面的三个比较都将返回false。

### 加减号

``` javascript
console.log(typeof (1 + "1"), 1 + "1"); // string 11 +号的操作数是数字与字符串，执行连接操作
console.log(typeof ("1" - 1), "1" - 1); // number 0 相减操作会尝试将操作数转换成数字类型
```

## 数字类型
### 浮点数运算
看一个例子：
``` javascript
console.log(0.1 + 0.2 === 0.3); // false
```

惊天BUG？

JavaScript只有一种数字类型Number，没有Integer和Float，Number是IEEE标准中双精度浮点运算(64位)类型。 浮点数的精度问题不是JavaScript特有的，因为有些小数以二进制表示位数是无穷的，这就意味着JavaScript中浮点数运算会有无法避免的精度丢失。
``` javascript
console.log(0.1 + 0.2 === 0.3); // false
console.log(0.1 + 0.2); // 0.30000000000000004，精度丢失
console.log(0.0 === 0); // true，integer和float没有区别
```

解决：
在判断浮点运算结果前对计算结果进行精度缩小，因为在精度缩小的过程总会自动四舍五入:
``` javascript
var result = (0.1 + 0.2).toFixed(1);
console.log(typeof result, result); // string 0.3，注意toFixed后的类型为string
```

### NaN
NaN的类型是Number，NaN和任何东西比较都是false。
``` javascript
console.log(typeof NaN); // number
console.log(NaN === NaN); // fasle
```

还有很多坑没记上来，还有很多坑未踩过去

END！！！