# ECMAScript 6.0(阮一峰) 学习笔记
(引用部分多为CV，或有删改)

[toc]
## No1.ECMAScript6简介 
##### 1.ECMAScript6和 JavaScript 的关系
> ECMA是一个标准化组织,为Javascript规定了使用标准，并将其称作是 ECMAScript。ECMAScript是JavaScript的规格，后者是前者的一种实现。

##### 2.ES6 与 ECMAScript 2015 的关系
>ES6 既是一个历史名词，也是一个泛指，含义是 5.1 版以后的 JavaScript 的下一代标准

##### 3.ECMAScript 的历史
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
##### 4.Babel 转码器//略

>Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在老版本的浏览器执行。
****
## No2.let 和 const 命令
#### ①let
##### I.let与var相比
 **let**声明的变量只在它所在的**块级作用域**有效
 而var全局范围内都有效
PS:(因此,for循环的计数器，就很合适使用let命令)
一个有意思的例子:
```js
>var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a\[6](); // 6
//每一轮循环的变量i都是重新声明的!
```
JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算！
换成var就是10！
**关于for循环**
for循环还有一个特别之处，设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域！
```js
>for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
##### II.let不存在变量提升
变量提升：变量可以在声明之前使用，但值为undefined
```js
>// var 的情况
console.log(foo); // 输出undefined
var foo = 2;
// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
##### III.暂时性死区:TDZ
只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响
```js
>var tmp = 123;
if (true) {
  tmp = 'abc'; // ReferenceError
  //此行以上即为tmp的死区
  let tmp;//不能变量提升
}
```
进一步思考：
```js
>// 报错
let x = x;
// ReferenceError: x is not defined
只要变量在还**没有声明完成前**使用，就会报错。
```
##### IV.不允许重复声明
let不允许在相同作用域内，重复声明同一个变量。
#### ②块级作用域       即{}内
##### I.ES6 块级作用域
**允许块级作用域的任意嵌套,**
**每一层都是一个单独的作用域,**
**无法读取不同层的内部变量。**
内层作用域可以定义外层作用域的**同名**变量：
```js
>{{{{
  let insane = 'Hello World';
  {let insane = 'Hello World'}
}}}};
```
##### II.块级作用域与函数声明
ES6明确允许在块级作用域之中声明函数。
块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。
!!<strong>浏览器的实现可以不遵守上面的规定，有自己的行为方式。
<ul><li>允许在块级作用域内声明函数。
<li>函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
<li>函数声明还会提升到所在的块级作用域的头部。
</ul>上面三条规则只对 ES6 的浏览器实现有效
</strong>

*由以上，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。*
```js
// 块级作用域内部的函数声明语句，建议不要使用
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
```
#### ③const
##### I.const声明一个只读的常量。一旦声明，常量的值就不能改变
PS:const一旦声明变量，就必须**立即**初始化,如
>const a;报错

const的作用域与let命令相同：
1.只在声明所在的块级作用域内有效；
2.声明的常量也是不提升，
3.同样存在暂时性死区，只能在声明的位置后面使用。
##### II.const本质理解：
实际上，不是说变量不能改变，而是**变量指向的那个内存地址所保存的数据不得改动**

>数值、字符串、布尔值，值就保存在变量指向的那个内存地址，因此等同于常量。

>对象,数组，变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向一个固定的地址）
```js
const foo = {};
// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123
// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
**PS:参考c++里指针概念与python中元组里放列表，列表可以change元素的情形**
###### 冻结一个对象
```js
>const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```
###### ES6 声明变量的六种方法
var   function  let const  import  class

#### ④顶层对象的属性
**顶层对象，在浏览器环境指的是window对象，在 Node 指的是global对象。**
window对象有实体含义，指的是浏览器的窗口对象
*ES6规定，*
var命令和function命令声明的全局变量，依旧是顶层对象的属性；
let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性
**SO:**
```js
>var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1
let b = 1;
window.b // undefined
```
#### ⑤globalThis 对象
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
```js
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
```
**ES2020 引入globalThis作为顶层对象。
任何环境下，globalThis都是存在的，都可以从它拿到顶层对象，指向全局环境下的this!**

## No3.变量的解构赋值
### I.数组的解构赋值
ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被
称为解构（Destructuring）。
1. 可以从数组中提取值，**按照对应位置，对变量赋值**
```js
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
2. 如果解构不成功，变量的值就等于undefined。
```js
let [foo] = [];
let [bar, foo] = [1];//foo均为undefined
```
3. 不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功
```js
let [x, y] = [1, 2, 3];
x // 1
y // 2
let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```
4. =右边应是可遍历的结构，具备 Iterator 接口
5. 默认值
解构赋值允许指定默认值
```js
let [x, y = 'b'] = ['a']; // x='a', y='b'
```
>ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效
```js
let [x = 1] = [undefined];
x // 1
let [x = 1] = [null];
x // null
//null不严格等于undefined
```
若默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。
```js
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2!!
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```
### II.对象的解构赋值
#### ①简介
1. 数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值
```js
let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"
```
2. 对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。
```js
// 例一
let { log, sin, cos } = Math;
// 例二
const { log } = console;
log('hello') // hello
```
3. 实质,先找到同名属性，然后再赋给对应的变量。真正被赋值的是变量baz，而不是模式foo。
```js
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined
```
4. 解构用于嵌套结构的对象(下例同时给模式赋值):
```js
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};
let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```
复杂一点的嵌套
```js
const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};
let { loc, loc: { start }, loc: { start: { line }} } = node;
line // 1
loc  // Object {start: Object}
start // Object {line: 1, column: 5}
```
5. 对象的解构赋值可以取到继承的属性
```js
const obj1 = {};
const obj2 = { foo: 'bar' };
Object.setPrototypeOf(obj1, obj2);
const { foo } = obj1;
foo // "bar"
```
#### ②默认值
默认值生效的条件是:对象的属性值严格等于undefined
```js
var {x, y = 5} = {x: 1};
x // 1
y // 5
var {x: y = 3} = {};
y // 3
var {x: y = 3} = {x: 5};
y // 5
```
#### ③注意点
（1）将一个已经声明的变量用于解构赋值，必须非常小心。
```js
// 错误的写法
let x;
{x} = {x: 1};
// SyntaxError: syntax error
//上面代码的写法会报错，
因为 JavaScript 引擎会将{x}理解成一个代码块，
//从而发生语法错误。
//只有不将大括号写在行首，避免 JavaScript 将其解释为代码块
// 正确的写法
let x;
({x} = {x: 1});
```
 （2）数组本质是特殊的对象，因此可以对数组进行对象属性的解构
```js
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```
(PS：0键对应1，[arr.length - 1]即2键对应取到3)
### III.字符串的解构赋值
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。
```JS
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
类似数组的对象都有一个length属性
```JS
let {length : len} = 'hello';
len // 5
```
### IV.数值和布尔值的解构赋值
解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。
```JS
let {toString: s} = 123;
s === Number.prototype.toString // true
let {toString: s} = true;
s === Boolean.prototype.toString // true
```
(ps:数值和布尔值的包装对象都有toString属性，因此变量s都能取到值。)
### V.函数参数的解构赋值
eg1:
```js
function add([x, y]){
  return x + y;
}
add([1, 2]); // 3
```
eg2(map函数应用):
```js
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```
使用默认值:
```js
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```
undefined就会触发函数参数的默认值。
```js
[1, undefined, 3].map((x = 'yes') => x);
// [ 1, 'yes', 3 ]
```
### VI.圆括号问题
建议只要有可能，就不要在模式中放置圆括号
尤其
* 变量声明语句
```js
let [(a)] = [1];
```
* 函数参数
```js
function f([z,(x)]) { return x; }
```
* 赋值语句的模式
```js
// 全部报错
({ p: a }) = { p: 42 };
([a]) = [5];
```
>可以使用圆括号的情况只有一种：**赋值**语句的**非模式部分**，可以使用圆括号。
```js
[(b)] = [3]; // 正确
//模式是取数组的第一个成员，跟圆括号无关

({ p: (d) } = {}); // 正确
//模式是p，而不是d

[(parseInt.prop)] = [3]; // 正确
```
### VII.变量的解构赋值用途
#### ①交换变量的值(简洁)
```js
let x = 1;
let y = 2;
[x, y] = [y, x];
```
#### ②从函数返回多个值
```js
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();
// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
#### ③函数参数的定义
```js
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);
// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
#### ④提取 JSON 数据 !!
```js
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};
let { id, status, data: number } = jsonData;
console.log(id, status, number);
// 42, "OK", [867, 5309]
```
#### ⑤函数参数的默认值
```js
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```
指定参数的默认值，就避免了在函数体内部再写`var foo = config.foo || 'default foo';`这样的语句
#### ⑥遍历 Map 结构
Map 结构原生支持 Iterator 接口，配合变量的解构赋值，获取键名和键值就非常方便。
```js
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');
for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```
#### ⑦输入模块的指定方法
```js
const { SourceMapConsumer, SourceNode } = require("source-map");
```

## No4.字符串的扩展
### I.字符的 Unicode 表示法
允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点
```js
"\u0061"
// "a"

//这种表示法只限于码点在\u0000~\uFFFF之间的字符。
//超出这个范围的字符，必须用两个双字节的形式表示
"\uD842\uDFB7"
// "𠮷"

"\u20BB7"
// " 7"
//如果直接在\u后面跟上超过0xFFFF的数值（比如\u20BB7），
//JavaScript 会理解成\u20BB+7。
//由于\u20BB是一个不可打印字符，
//所以只会显示一个空格，后面跟着一个7
```
只要将码点放入大括号，就能正确解读该字符
```js
"\u{20BB7}"
// "𠮷"
```
javaScript 共有 6 种方法可以表示一个字符。
```js
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```
### II.字符串的遍历器接口
字符串可以被for...of循环遍历,for of可以识别大于0xFFFF的码点，传统的for循环无法识别这样的码点。
```js
//字符串text只有一个字符，
//但是for循环会认为它包含两个字符（都不可打印），
//而for...of循环会正确识别出这一个字符
let text = String.fromCodePoint(0x20BB7);
for (let i = 0; i < text.length; i++) {
  console.log(text[i]);
}
// " "
// " "
for (let i of text) {
  console.log(i);
}
// "𠮷"
```
### III.直接输入 U+2028 和 U+2029
1. JavaScript 规定有5个字符，不能在字符串里面直接使用，只能使用转义形式
U+005C：反斜杠（reverse solidus)
U+000D：回车（carriage return）
U+2028：行分隔符（line separator）
U+2029：段分隔符（paragraph separator）
U+000A：换行符（line feed）
**举例来说，字符串里面不能直接包含反斜杠，一定要转义写成\\或者\u005c。**
2. JSON 格式允许字符串里面直接使用 U+2028（行分隔符）和 U+2029（段分隔符）。这样一来，服务器输出的 JSON 被JSON.parse解析，就有可能直接报错
```js
const json = '"\u2028"';
JSON.parse(json); // 可能报错
```
为了消除这个报错，ES2019 允许 JavaScript 字符串直接输入 U+2028（行分隔符）和 U+2029（段分隔符）。
```js
const PS = eval("'\u2029'");
```
3. 模板字符串现在就允许直接输入这两个字符。
另外，正则表达式依然不允许直接输入这两个字符，这是没有问题的，因为 JSON 本来就不允许直接包含正则表达式
### IV.JSON.stringify() 的改造
JSON 数据必须是 UTF-8 编码。
* \uD834\uDF06是两个码点，必须放在一起配对使用，代表字符𝌆。这是为了表示码点大于0xFFFF的字符的一种变通方法。单独使用\uD834和\uDFO6这两个码点是不合法的，或者颠倒顺序也不行，因为\uDF06\uD834并没有对应的字符。

* JSON.stringify()的问题在于，它**可能返回0xD800到0xDFFF之间的单个码点，即使它不对应任何字符**
```js
JSON.stringify('\u{D834}') // "\u{D834}"(没有对应字符)
```
如果遇到0xD800到0xDFFF之间的单个码点，或者不存在的配对形式，**现在！JSON.stringify()会返回转义字符串，留给应用自己决定下一步的处理**。
```js
JSON.stringify('\u{D834}') // ""\\uD834""
JSON.stringify('\uDF06\uD834') // ""\\udf06\\ud834""
```
### V.模板字符串
模板字符串（template string）是增强版的字符串，用**反引号（\`）** 标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。
```JS
// 普通字符串
`In JavaScript '\n' is a line-feed.`
// 多行字符串
console.log(`string text line 1
string text line 2`);
// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```
在模板字符串中需要使用反引号，则前面要用反斜杠转义.
```JS
实现计算
let x = 1;
let y = 2;
`${x} + ${y} = ${x + y}`
// "1 + 2 = 3"
let obj = {x: 1, y: 2};
`${obj.x + obj.y}`
// "3"
还能调用函数
function fn() {
  return "Hello World";
}
`foo ${fn()} bar`
// foo Hello World bar
成为函数的返回值
let func = (name) => `Hello ${name}!`;
func('Jack') // "Hello Jack!"
```
### VI.使用模板编译的一个实例（已保存在BY前端文件夹）
https://www.bookstack.cn/read/es6-3rd/spilt.6.docs-string.md
### VII.标签模板
#### ①定义
模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为“标签模板”功能（tagged template）
```JS
alert`hello`
// 等同于
alert(['hello'])
```
#### ②用法
"标签"指的就是函数，紧跟在后面的模板字符串就是它的参数
* 如果模板字符里面有变量，就不是简单的调用了，而是**会将模板字符串先处理成多个参数，再调用函数**
```JS
let a = 5;
let b = 10;
tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```
>tag是一个函数。整个表达式的返回值，就是tag函数处理模板字符串后的返回值。
tag函数的**第一个参数是一个数组**，该数组的成员是**模板字符串中那些没有变量替换的部分**，也就是说，变量替换只发生在数组的第一个成员与第二个成员**之间**、第二个成员与第三个成员**之间**，以此类推。
tag函数的其他参数，都是模板字符串各个变量被替换后的值。
下面将各参数能够混合使用
```js
let total = 30;
let msg = passthru`The total is ${total} (${total*1.05} with tax)`;
function passthru(literals) {
  let result = '';
  let i = 0;
  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i];
    }
  }
  return result;
}
msg // "The total is 30 (31.5 with tax)"
```
#### ③实际应用
1. 过滤 HTML 字符串，防止用户输入恶意内容。
```js
let message =
  SaferHTML`<p>${sender} has sent you a message.</p>`;
function SaferHTML(templateData) {
  let s = templateData[0];
  for (let i = 1; i < arguments.length; i++) {
    let arg = String(arguments[i]);
    // Escape special characters in the substitution.
    s += arg.replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;");
    // Don't escape special characters in the template.
    s += templateData[i];
  }
  return s;
}
```
上面代码中，sender变量往往是用户提供的，经过SaferHTML函数处理，里面的特殊字符都会被转义。
```js
let sender = '<script>alert("abc")</script>'; // 恶意代码
let message = SaferHTML`<p>${sender} has sent you a message.</p>`;
message
// <p>&lt;script&gt;alert("abc")&lt;/script&gt; has sent you a message.</p>
```
2. 多语言转换（国际化处理）。
```js
i18n`Welcome to ${siteName}, you are visitor number ${visitorNumber}!`
// "欢迎访问xxx，您是第xxxx位访问者！"
```
3. 甚至可以使用标签模板，在 JavaScript 语言之中嵌入其他语言。
4. 模板处理函数的第一个参数（模板字符串数组），还有一个raw属性。
```js
tag`First line\nSecond line`
function tag(strings) {
  console.log(strings.raw[0]);
  // strings.raw[0] 为 "First line\\nSecond line"
  // 打印输出 "First line\nSecond line"
}
```
>strings数组是["First line\nSecond line"]，
strings.raw数组就是["First line\\\nSecond line"]。
两者唯一的区别，就是字符串里面的斜杠都被转义了。比如，strings.raw 数组会将\n视为\\\和n两个字符，而不是换行符。这是为了方便取得转义之前的原始模板而设计的
#### ④模板字符串的限制(会默认转义)
标签模板里面，可以内嵌其他语言。但是，模板字符串**默认会将字符串转义**，导致无法嵌入其他语言.
为了解决这个问题，ES2018 放松了对标签模板里面的字符串转义的限制。如果遇到不合法的字符串转义，就返回undefined，而不是报错，并且从raw属性上面可以得到原始字符串
```js
function tag(strs) {
  strs[0] === undefined
  strs.raw[0] === "\\unicode and \\u{55}";
}
tag`\unicode and \u{55}`
```
>对字符串转义的放松，只在标签模板解析字符串时生效，不是标签模板的场合，依然会报错。
`let bad = `bad escape sequence: \unicode`; // 报错`
## No5.字符串的新增方法(复习高程)
