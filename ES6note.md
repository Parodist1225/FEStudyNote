# ECMAScript 6.0(阮一峰) 学习笔记
(引用部分多为CV，或有删改)

[toc]
### No1.ECMAScript6简介 
#####1.ECMAScript6和 JavaScript 的关系
> ECMA是一个标准化组织,为Javascript规定了使用标准，并将其称作是 ECMAScript。ECMAScript是JavaScript的规格，后者是前者的一种实现。

#####2.ES6 与 ECMAScript 2015 的关系
>ES6 既是一个历史名词，也是一个泛指，含义是 5.1 版以后的 JavaScript 的下一代标准

#####3.ECMAScript 的历史
>初学者一开始学习 JavaScript，其实就是在学 3.0 版的语法。

初学者->指我()
>**Node.js** 是 JavaScript 的服务器运行环境（runtime）。它对 ES6 的支持度更高。除了那些默认打开的功能，还有一些语法功能已经实现了，但是默认没有打开。使用下面的命令，可以查看 Node.js 默认没有打开的 ES6 实验性语法。

>// Linux & Mac
        $ node --v8-options | grep harmony

>// Windows
        $ node --v8-options | findstr harmony

ps:我试着在我的win11里以管理员权限打开了Powershell里运行了下，报错“找不到命令 findstr，但它确实存在于当前位置。”,改为输入node --v8-options | .\findstr harmony
成功查看--harmony (enable all completed harmony features)
<br>
#####4.Babel 转码器//略

>Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在老版本的浏览器执行。
****
### No2.let 和 const 命令
####①let
#####I.let与var相比
 **let**声明的变量只在它所在的**块级作用域**有效
 而var全局范围内都有效
PS:(因此,for循环的计数器，就很合适使用let命令)
一个有意思的例子:
>var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a\[6](); // 6
//每一轮循环的变量i都是重新声明的!

JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算！
换成var就是10！
**关于for循环**
for循环还有一个特别之处，设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域！
>for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
#####II.let不存在变量提升
变量提升：变量可以在声明之前使用，但值为undefined
>// var 的情况
console.log(foo); // 输出undefined
var foo = 2;
// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;

#####III.暂时性死区:TDZ
只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响
>var tmp = 123;
if (true) {
  tmp = 'abc'; // ReferenceError
  //此行以上即为tmp的死区
  let tmp;//不能变量提升
}

进一步思考：
>// 报错
let x = x;
// ReferenceError: x is not defined
只要变量在还**没有声明完成前**使用，就会报错。

#####IV.不允许重复声明
let不允许在相同作用域内，重复声明同一个变量。
####②块级作用域       即{}内
#####I.ES6 块级作用域
**允许块级作用域的任意嵌套,**
**每一层都是一个单独的作用域,**
**无法读取不同层的内部变量。**
内层作用域可以定义外层作用域的**同名**变量：
>{{{{
  let insane = 'Hello World';
  {let insane = 'Hello World'}
}}}};
#####II.块级作用域与函数声明
ES6明确允许在块级作用域之中声明函数。
块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。
!!<strong>浏览器的实现可以不遵守上面的规定，有自己的行为方式。
<ul><li>允许在块级作用域内声明函数。
<li>函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
<li>函数声明还会提升到所在的块级作用域的头部。
</ul>上面三条规则只对 ES6 的浏览器实现有效
</strong>

*由以上，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。*
>// 块级作用域内部的函数声明语句，建议不要使用
{
  let a = 'secret';
  function f() {
    return a;
  }
}
// 块级作用域内部，优先使用函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
####③const
#####I.const声明一个只读的常量。一旦声明，常量的值就不能改变
PS:const一旦声明变量，就必须**立即**初始化,如
>const a;报错

const的作用域与let命令相同：
1.只在声明所在的块级作用域内有效；
2.声明的常量也是不提升，
3.同样存在暂时性死区，只能在声明的位置后面使用。
#####II.const本质理解：
实际上，不是说变量不能改变，而是**变量指向的那个内存地址所保存的数据不得改动**

>数值、字符串、布尔值，值就保存在变量指向的那个内存地址，因此等同于常量。

>对象,数组，变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向一个固定的地址）
const foo = {};
// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123
// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only

**PS:参考c++里指针概念与python中元组里放列表，列表可以change元素的情形**
######冻结一个对象
>const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;

######ES6 声明变量的六种方法
var   function  let const  import  class

####④顶层对象的属性
**顶层对象，在浏览器环境指的是window对象，在 Node 指的是global对象。**
window对象有实体含义，指的是浏览器的窗口对象
*ES6规定，*
var命令和function命令声明的全局变量，依旧是顶层对象的属性；
let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性
**SO:**
>var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1
let b = 1;
window.b // undefined
####⑤globalThis 对象
**I.浏览器顶层对象是window（ Node 和 Web Worker 没有window。）**
**II.浏览器和 Web Worker 里面self也指向顶层对象（ Node 没有self。）**
**III.Node 里面，顶层对象是global（但其他环境都不支持。）**
>同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用this变量，但是有局限性：
1.全局环境中，this会返回顶层对象。**！Node 模块和 ES6 模块中，this返回的是当前模块。**
2.函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，**this会指向顶层对象**。
>（但是，严格模式下，这时this会返回undefined。）
3.不管是严格模式，还是普通模式，**new Function('return this')()，总是会返回全局对象。**
？？？如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么eval、new Function这些方法都可能无法使用。
>>取到顶层对象两个方法
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);
     PS:此刻我觉得方法二远更易于接受，一的"?",":"是啥啊...
// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};

**ES2020 引入globalThis作为顶层对象。
任何环境下，globalThis都是存在的，都可以从它拿到顶层对象，指向全局环境下的this!**

