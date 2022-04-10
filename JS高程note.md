#JavaScript高级程序设计（第4版）学习笔记
(引用部分多为CV或代码，有删改调整)
(善良的黄字绿字部分是"饼饼"标注过的吗？%%%)
(※暂时有待加强记忆)
(M这里有暂时超纲的东西,考虑先学懂后再细看)
(@待丰富)
[toc]
## No0.前言：各种浏览器极其对应的js引擎
Chrome - Blink/V8
Firefox- Gecko/SpiderMonkey，
Safari - WebKit/JavaScriptCore
微软- Trident/EdgeHTML/Chakra

~~提到的希望~~具有的的前置技术：
* [ ] Java
* [ ] PHP
* [x] Python  a little
* [ ] Ruby
* [ ] Golang
* [x] HTML   a little
* [x] CSS    a little
****
2022.3.21
## No1. 什么是 JavaScript
### I.历史(略)
### II.JavaScript 实现
#### ①完整的 JavaScript 实现
 1. 核心（ECMAScript）
 2. 文档对象模型（DOM）
 3. 浏览器对象模型（BOM）

**Web 浏览器只是 ECMAScript 实现可能存在的一种宿主环境（hostenvironment）**
宿主环境提供ECMAScript 的基准实现和与环境自身交互必需的扩展。
扩展（比如 DOM）使用 ECMAScript 核心类型和语法，提供特定于环境的额外功能。
**其他宿主环境还有服务器端 JavaScript 平台 Node.js**
#### ②文档对象模型（DOM，Document Object Model）※
DOM是一个应用编程接口（API），**提供与网页内容交互的方法和接口**。用于在 HTML 中使用扩展的 XML。DOM 将整个页面抽象为一组分层节点。HTML 或 XML 页面的每个组成部分都是一种**节点(node)**，包含不同的数据
*PS:经常用DOM树描述各分层节点的联系*
>注意 DOM 并非只能通过 JavaScript 访问，而且确实被其他很多语言实现了。不过对于浏览器来说，DOM 就是用ECMAScript 实现的，如今已经成为 JavaScript 语言的一大组成部分。
#### ③浏览器对象模型（BOM） 
**提供与浏览器交互的方法和接口**,用于支持访问和操作浏览器的窗口。使用 BOM，开发者可以操控浏览器显示页面之外的部分

## No2. HTML 中的 JavaScript
### I.\<script>元素
#### ①.\<script>元素有下列 7 个(尚在使用)属性。
> async：可选。**表示应该立即开始下载脚本，但不能阻止其他页面动作，比如下载资源或等待其他脚本加载**。**只对外部脚本文件有效**。
 charset：可选。使用 src 属性指定的代码字符集。这个属性很少使用，因为大多数浏览器不在乎它的值。
 crossorigin：可选。配置相关请求的CORS（跨源资源共享）设置。默认不使用CORS。crossorigin="anonymous"配置文件请求不必设置凭据标志。crossorigin="use-credentials"设置凭据标志，意味着出站请求会包含凭据。
 defer：可选。**表示脚本可以延迟到文档完全被解析和显示之后再执行**。**只对外部脚本文件有效**。在 IE7 及更早的版本中，对行内脚本也可以指定这个属性。
 integrity：可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性（SRI，Subresource Integrity）。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于**确保内容分发网络（CDN，Content Delivery Network）不会提供恶意内容**。
 src：可选。**!!表示包含要执行的代码的外部文件**。
 type：表示代码块中**脚本语言的内容类型**（也称 MIME 类型）。按照惯例，这个值始终都是**"text/javascript"**，尽管"text/javascript"和"text/ecmascript"都已经废弃了。JavaScript 文件的 MIME 类型通常是"application/x-javascript"，不过给type 属性这个值有可能导致脚本被忽略。在非 IE 的浏览器中有效的其他值还有"application/javascript"和"application/ecmascript"。如果这个值是 module，则代码会被当成 ES6 模块，而且只有这时候代码中才能出现 import 和 export 关键字。
#### ②.行内嵌入与引入外部文件

**在\<script>元素中的代码被计算完成之前，页面的其余内容不会被加载，也不会被显示**\<script>...\</script> 
在使用行内 JavaScript 代码时，要注意代码中不能出现字符串</script>  实在想用,考虑转义字符“\”
要包含外部文件中的 JavaScript，就必须使用 src 属性。这个属性的值是一个 URL，指向包含JavaScript 代码的文件:
`<script src="example.js"></script>`

>浏览器不会检查所包含 JavaScript 文件的扩展名
(PS:不一定非得是js，可ts)

>浏览器在解析外部（网站）资源时，会向 src 属性指定的路径发送一个 GET 请求，请求不受浏览器同源策略限制，但返回并被执行的 JavaScript 则受限制。来自外部域的代码会被当成加载它的页面的一部分来加载和解释.
PS:**!!须要确保该链接的可靠性**

>浏览器都会按照\<script>在页面中出现的顺序依次解释它们，前提是它们没有使用 defer 和 async 属性

#### ③\<script>标签位置
(PS:犹记得实习期间刚学时放head里还是放body里,坑的教训)
1.head里的坏处:把所有 JavaScript文件都放在<head>里，也就意味着必须把所有 **JavaScript 代码都下载、解析和解释完成后，才能开始渲染页面**（**页面在浏览器解析到\<body>的起始标签时开始渲染**）。对于需要很多 JavaScript 的页面，这会**导致页面渲染的明显延迟，在此期间浏览器窗口完全空白**。
2.现代 Web 应用程序通常将所有 JavaScript 引用放在\<body>
元素中的页面内容**后面**

#### ④推迟执行脚本(defer)※
脚本会被延迟到整个页面都解析完毕后再运行。在\<script>元素上
设置 defer 属性
`<script defer src="example1.js"></script>`

即告诉浏览器**立即下载，但延迟执行**

只对外部脚本文件才有效，最好只包含一个这样的脚本
#### ⑤ 异步执行脚本(async)※
1.脚本不需要等待其他脚本，同时也不阻塞文档渲染，即异步加载
2.只适用于外部脚本，会告诉浏览器立即开始下载,与 defer 不同的是标记为 async 的脚本并不保证能按照它们出现的次序执行!
3.**异步脚本不应该在加载期间修改 DOM！**
4.**异步脚本保证会在页面的 load 事件前执行，但可能会在 DOMContentLoaded之前或之后**
#### ⑥动态加载脚本(document.createElement('script'))※
1.JavaScript 可以使用 DOM API，所以通过向 DOM 中动态添加 script 元素同样可以加载指定的脚本。只要创建一个 script 元素并将其添加到DOM
```js
let script = document.createElement('script');
script.src = 'my.js';
document.head.appendChild(script)； 
```
*(PS:实习时搞的百度搜索用了这种动态加载方法，实时获取了真实搜索结果数据并存入一个json中)*
2.这种方式创建的\<script>元素是以异步方式加载的，相当于添加了 async 属性!
想让预加载器知道这些动态请求文件的存在，可以在文档头部显式声明它们：
`<link rel="preload" href="my.js"> `

>(已退出舞台)XHTML 中的变化:
在 XHTML 中使用 JavaScript 必须指定 type 属性且值为text/javascript

`<!--   >用黑科技!   -->`

### II.\<script>行内代码与外部文件(这里有解释放外部文件的好处)
在配置浏览器请求外部文件时，要重点考虑的一点是它们会占用多少带宽。在 SPDY/HTTP2 中，预请求的消耗已显著降低，以**轻量、独立** JavaScript 组件形式向客户端送达脚本更具优势。
>在初次请求时，如果浏览器支持 SPDY/HTTP2，就可以从同一个地方取得一批文件，并将它们逐个放到浏览器缓存中。
在第二个页面请求时，由于你已经把应用程序切割成了轻量可缓存的文件，第二个页面也依赖的某些组件此时已经存在于浏览器缓存中了。
### III.文档模式(doctype)(简略)
**1.混杂模式（quirks mode）**
让 IE 像 IE5 一样（支持一些非标准的特性）
**2.标准模式（standards mode）**
让 IE 具有兼容标准的行为
>上两种模式的主要区别只体现在通过 CSS 渲染的内容方面，但对JavaScript 也有一些关联影响，或称为副作用

**3.准标准模式（almost standards mode）:**
这种模式下的浏览器支持很多标准的特性，但是没有标准规定得那么严格。主要区别在于如何对待图片元素周围的空白（在表格中使用图片时最明显）。
(PS:这块尚未自己实验区分对比)
### IV.\<noscript>元素(适配不认js的browser)(少见)
用于给不支持 JavaScript 的浏览器提供替代内容
\<noscript>元素可以包含任何可以出现在<body>中的 HTML 元素
>1.浏览器不支持脚本；
2.浏览器对脚本的支持被关闭。
任何一个条件被满足，包含在\<noscript>中的内容就会被渲染

## No3.JavaScript语言基础 

### I.语法
#### ①基础
1.区分大小写
2.标识符:变量、函数、属性或函数参数的名称。
**标识符可以由一或多个下列字符组成：
第一个字符必须是一个字母、下划线（_）或\$；**
>第一个字符不能是数字！

**剩下的其他字符可以是字母、下划线\$或数字**
3.驼峰大小写命名
4.注释
>//单行注释  
/* 这是多行
注释 */ 

5.严格模式（strict mode）
意义:ECMAScript 3 的一些不规范写法在这种模式下会被处理，对于不安全的活动将抛出错误
启用方法:脚本(或函数内部)开头加上这一行：
`"use strict";//是一个预处理指令`

6.语句结尾加;
有助于在**某些情况下提升性能**，因为解析器会尝试在合适的
位置补上分号以纠正语法错误!
### II.关键字与保留字
>ES6关键字
<strong>break do in typeof
case else instanceof var
catch export new void
class extends return while
const finally super with
continue for switch yield
debugger function this
default if throw
delete import try </strong>

>未来的保留字:**enum**
严格模式下保留:
<strong>implements package public
interface protected static
let private</strong>
模块代码中保留:
**await**

### III.变量
ECMAScript 变量是松散类型的，意思是变量可以用于保存**任何**类型的数据。每个变量只不过是一个用于保存任意值的命名占位符
(PS:不必像C艹里的int double....)
#### ①var声明
不初始化的情况下，变量会保存一个特殊值 undefined
1. var 声明作用域
var 操作符定义的变量会成为包含它的函数的局部变量，退出后销毁
```js
function test() {
 var message = "hi"; // 局部变量
 //message = "hi";//全局变量,下面就不会报错了
 (不推荐|严格模式下ReferenceError)
}
test();
console.log(message); // 出错！
```
定义多个变量
```js
var a = "hi",
 b = false,
 c = 29;
``` 
2. var 声明提升
变量会自动提升到函数作用域顶部!
```js
function foo() {
 console.log(a);
 var a = 26;
}
foo(); // undefined
```
其实解析时是:
```js
function foo() {
 **var a**//hoist
 console.log(a);
 a = 26;
}
foo(); // 26
```
#### ②let声明
1.对比
let 声明的范围是块作用域，不可重复声明，不提升
var 声明的范围是函数作用域，可重复声明
```js
var name;
let name; // SyntaxError
let age;
var age; // SyntaxError
```
2.TDZ
在 let 声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone
3.全局声明
```js
var name = 'Matt';
console.log(window.name); // 'Matt'
let age = 26;
console.log(window.age); // undefined
```
4.条件声明（(let不可依赖)
**因为 let 的作用域是块，所以不可能检查前面是否已经使用 let 声明过同名变量，同时也就不可能在没有声明的情况下声明它**！
5. for 循环中的 let 声明
```js
for ([此处var/let有不同输出结果] i = 0; i < 5; ++i) {
 setTimeout(() => console.log(i), 0)
}
// let 0、1、2、3、4
// var 5、5、5、5、5 
```
PS：(var退出循环时,迭代变量保存的是**导致循环退出的值**：5。在之后执行超时逻辑时，**所有的 i 都是同一个变量**，因而输出的都是同一个最终值!)
(let则是**为每个迭代循环声明一个新的迭代变量**,console.log 输出的也就是循环执行过程中**每个迭代变量的值**)
### ③const 声明
1.与 let 基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量
2.const 声明的**限制只适用于它指向的变量的引用**。换句话说，如果 const 变量引用的是一个对象，那么**修改这个对象内部的属性并不违反 const 的限制**。
```js
const person = {};
person.name = 'Matt'; // ok 
```
(PS:参考py中的元组中存列表，列表可修改添加元素，列表本身的指向不可更改)
3.对 for-of 和 for-in 循环特别有意义
```js
for (const key in {a: 1, b: 2}) {
 console.log(key);
}
// a, b 
```
### ④ 声明风格及最佳实践
1. 不使用 var
2. const 优先，let 次之
### IV.数据类型
6 种原始类型：Undefined、Null、Boolean、Number、String 和 Symbol
1 种复杂数据类型： Object
#### ①typeof 操作符
* "undefined"表示值未定义；
* "boolean"表示值为布尔值；
* "string"表示值为字符串；
* "number"表示值为数值；
* "object"表示值为对象（而不是函数）或 null；
* "function"表示值为函数；
* "symbol"表示值为符号。
>typeof null //"object"
null 被认为是一个对空对象的引用!!

PS:to be strict,函数在 ECMAScript 中被认为是对象
****    
2022.3.22
#### ②Undefined 类型
变量声明但未初始化，为undefined类型
>增加这个特殊值的目的就是为了正式明确空对象指针（null）和未初始化变量的区别

>1.对未初始化的变量调用 typeof 时，返回的结果是"undefined"
>2.对未声明的变量调用它时，返回的结果**还是**"undefined"

(ps:因此，**建议声明了一个变量的同时初始化赋值**,来用typeof选出未声明的变量)

**undefined 是一个假值(false)**
```js
let message; // 这个变量被声明了，只是值为 undefined
// age 没有声明
if (message) {
 // 这个块不会执行
}
if (!message) {
 // 这个块会执行
}
if (age) {
 // 这里会报错
}
```
#### ③Null 类型
null 值表示一个空对象指针,**假值(false)**
typeof 传一个 null 会返回"object"
>**在定义将来要保存对象值的变量时，建议使用 null 来初始化**

PS:这样做只要检查这个变量的值是不是 null ,就可以知道这个变量是否在后来被重新赋予了一个对象的引用，比如：
`
if (car != null) {
 // car 是一个对象的引用
} `

#### ④Boolean 类型
1. Boolean()转型函数
两个布尔值**不同于数值**， true 不等于 1，false 不等于 0

|数据类型 |转换为 true 的值| 转换为 false 的值|
|------- | -----------    | -----------     | 
|Boolean |true |false|
|String |非空字符串| ""|（空字符串）|
|Number| 非零数值（包括无穷值）| 0、NaN|
|Object| 任意对象 |null|
|Undefined| N/A（不存在）| undefined
#### ⑤Number 类型
0. 基本的整数
八进制字面量，第一个数字必须是0
(八进制字面量在严格模式下是无效的)
十六进制字面量，必须让真正的数值前缀 0x
##### 1. 浮点值
定义浮点值，数值中必须包含小数点，而且小数点后面必须至少有一个数字(建议小数点前也加上)
`let floatNum1 = 1.; // 小数点后面没有数字，当成整数 1 处理
let floatNum2 = 10.0; // 小数点后面是零，当成整数 10 处理`
科学计数法
`let floatNum = 3.125e7; // 等于 31250000`
浮点值的精确度最高可达 **17 位小数**，但在算术计算中远不如整数精确。例如，0.1 加 0.2 得到的不是 0.3，而是 0.300 000 000 000 000 04！！！！！！
（ps：这种微小的四舍五入错误因为使用了 IEEE 754 数值）
##### 2. 值的范围
Number.MIN_VALUE :5e-324
Number.MAX_VALUE :1.797 693 134 862 315 7e+308
(超出即Infinity==Number.POSITIVE_INFINITY)
>确定一个值是不是有限大(isFinite()):
```js
let result = Number.MAX_VALUE + Number.MAX_VALUE;
console.log(isFinite(result)); // false 
```
##### 3. NaN
Not a Number!!!!任何**涉及 NaN 的操作始终返回 NaN**
```js
console.log(0/0); // NaN
console.log(-0/+0); // NaN `
NaN 不等于包括 NaN 在内的任何值
console.log(NaN == NaN); // false
```
isNaN方法
```js
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false，10 是数值
console.log(isNaN("10")); // false，可转换为数值 10
console.log(isNaN("blue")); // true，不可转换为数值
console.log(isNaN(true)); // false，可转换为数值 1
```

##### 4.数值转换
* Number()
```js
let num1 = Number("Hello world!"); // NaN
let num2 = Number(""); // 0
let num3 = Number("000011"); // 11
(八进制忽略0，十六进制返回对应十进制数)
let num4 = Number(null); // 0
let num5 = Number(undefined); // NaN
```
* parseInt()
字符串最前面的空格会被忽略，从第一个非空格字符开始转换。如果第一个字符不是数值字符、加号或减号，parseInt()立即返回 NaN
```js
let num1 = parseInt("1234blue"); // 1234
let num2 = parseInt(""); // NaN (此处与Number不同！) 
let num3 = parseInt("0xA"); // 10，解释为十六进制整数
let num4 = parseInt(22.5); // 22
let num5 = parseInt("70"); // 70，解释为十进制值
//可以加入第二个参数表示进制
let num1 = parseInt("A", 16); // 10，解释为十六进制整数
```
* parseFloat()
从位置 0 开始检测每个字符。同样，它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。
这意味着**第一次出现的小数点是有效的，但第二次出现的小数点就无效了，此时字符串的剩余字符都会被忽略**
十六进制数值始终会返回 0。因为parseFloat()只解析十进制值
```js
let num1 = parseFloat("1234blue"); // 1234，按整数解析
let num2 = parseFloat("0xA"); // 0
let num3 = parseFloat("22.5"); // 22.5
let num4 = parseFloat("22.34.5"); // 22.34
let num5 = parseFloat("0908.5"); // 908.5 
```
#### ⑥.String 类型
表示零或多个 16 位 Unicode 字符序列
可由双引号（"）、单引号（'）或反引号（`）标示
###### 1. 字符字面量
|  字 面 量	|含义  |
|---	|---	|
|  \n 	|   换行	|
|  \t	|  制表 	|
|  \b 	|   退格	|
|  \r	|   	回车|
|  \f 	|  换页 	|

转义序列表示一个字符，只算一个字符
字符串的长度可以通过其 length 属性获取：
`console.log(text.length);`
###### 2. 字符串的特点
字符串是不可变的（immutable），一旦创建，它们的值就不能变了。
要修改某个变量中的字符串值，必须先**销毁**原始的字符串，然后将包含新值的另一个字符串保存到该变量
###### 3. 转换为字符串
toString()方法:返回当前值的字符串等价物
```js
let age = 11;
let ageAsString = age.toString(); // 字符串"11"
```
(Ps:可以在()加入数字x表示转化成x进制数)
数的字符串表示，比如：
```js
let num = 10;
console.log(num.toString(2)); // "1010"
```
String()方法
(Ps:null 和 undefined 没有 toString()方法,考虑用String()
**不确定一个值是不是 null 或 undefined，可以使用 String()转型函数**)
`console.log(num.String())"null" `
###### 4. 模板字面量
ECMAScript 6 新增了使用模板字面量定义字符串的能力
模板字面量**保留换行字符，可以跨行定义字符串**：
(PS:此处指定义模板时规定的换行，空格也全部保留)
```js
let myMultiLineString = 'first line\nsecond line';
let myMultiLineTemplateLiteral = `first line
second line`;
console.log(myMultiLineString);
console.log(myMultiLineTemplateLiteral);
(二者输出均为.二者可===)
// first line
// second line"
```
###### 5. 字符串插值  ※   6.咋丢了？ ※
模板字面量不是字符串，而是一种特殊的 JavaScript 句法表达式，只不过求值后得到的是字符串.
字符串插值通过在${}中使用一个 JavaScript 表达式实现：
```js
let value = 5;
let exponent = 'second';
let Temp ='${ value } to the ${ exponent } power is ${ value * value }'; 
 console.log(Temp); // 5 to the second power is 25 
 ```
(Ps:参考python字符串中,""前加f的情形下用{}包括变量名来取其值)
(所有插入的值都会使用 toString()强制转型为字符串)

在插值表达式中可以调用函数和方法：

```js
//这个函数的作用就是返回只将首字母大写的字符串
function capitalize(word) {
return ${ word[0].toUpperCase() }${ word.slice(1) };
}//**此处slice(切片)(参数表示begin位置,返回一个数组，数组中含有 原数组从位置begin开始直到最后的一个 的所有元素)**
console.log(${ capitalize('hello') },${ capitalize('world') }!); // Hello, World!
```
###### 6. 模板字面量标签函数
标签函数（tag function）:可以**自定义插值行为**。标签函数会**接收** 被插值记号分隔后的模板function name(strings,**表达式参数**);
eg:
 ```js
let a = 6;
let b = 9;
function simpleTag(strings, aVal, bVal, sum) {
 console.log(strings);
 console.log(aVal);
 console.log(bVal);
 console.log(sum);
 return 'foobar';//此例子随便列举了个返回值，标准实现效果见下框代码
}
let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`; //这里的赋值会触发tag函数
// ["" ,  " + " , " = " , ""](ps:此即 被插值记号分隔后的模板  )
// 6
// 9
// 15
console.log(taggedResult); // "foobar" 
```
表达式参数的数量是可变的，所以通常应该使用剩余操作符（rest operator）将它们收集到一个数组中：
(PS:
* 剩余操作符:
函数的参数以 … 为前缀，则该参数是剩余参数（rest parameter）。
* 剩余参数表示参数个数不确定的参数列表。
* 在函数被调用时,该形参会成为一个数组,数组中的元素都是传递给该函数的多出来的实参的值。)

**有 n 个插值的模板字面量，传给标签函数的表达式参数的个数始终是 n**，**而传给标签函数的第一个参数所包含的字符串个数则始终是n+1**。
下面代码把这些字符串和对表达式求值的结果**拼接**为返回的字符串：
(PS:**下面代码中n=3,strings包含4个字符串个数,strings[0]=""**)
```js
let a = 6;
let b = 9;
function zipTag(strings, ...expressions) {
console.log(strings);
 for(const expression of expressions) {
 console.log(expression);
 }
 return strings[0] +
 expressions.map((e, i) => `${e}${strings[i + 1]}`)//先输出''，
 //e指向expressions里的参数(a,b,a+b)，i=0初始(说实话我也不知道why i=0)
 .join('');//.join('')方法，用' '分割数组各元素)
}
let taggedResult = zipTag`${ a } + ${ b } = ${ a + b }`;
// ["" ,  " + " , " = " , ""]这个是strings数组
// 6
// 9
// 15
console.log(taggedResult); // "6 + 9 = 15" 
```
(PS：**map() 方法创建一个新数组，新数组中的每个元素是元素组对应元素调用一次提供的函数后的返回值。**)
###### 7. 原始字符串
String.raw 标签函数：直接获取原始的模板字面量内容
```js
console.log(`\u00A9`); // ©
console.log(String.raw`\u00A9`); // \u00A9
```

#### ⑦. Symbol 类型
ES6新增，**符号是原始值，且符号实例是唯一、不可变的**。(PS:这意味着两个Symbol做==比较为false)
符号的用途是确保对象属性使用唯一标识符，不会发生属性冲突的危险
###### 1. 符号的基本用法
符号需要使用 Symbol()函数初始化
`let sym = Symbol();`
>调用 Symbol()函数时，也可以传入一个字符串参数作为对符号的描述（description），将来可以通过这个字符串来调试代码。但是，这个字符串参数与符号定义或标识完全无关
`let sym = Symbol('meiyong');`

**Symbol()函数不能与 new 关键字一起作为构造函数使用**
避免创建符号包装对象(object)
(PS:用 Boolean、String 或 Number创建new,均为object 见下面7.Object 类型)
###### 2. 使用全局符号注册表
如果运行时的不同部分**需要共享和重用符号实例**，那么可以**用一个字符串作为键，在全局符号注册表中创建并重用符号**:
使用 Symbol.for()方法(*传入的参数都会被转化为字符串*)
`let fooGlobalSymbol = Symbol.for('foo');`
(PS:后面可再次创一个指向'foo'的Symbol,二者===
* Symbol.for()方法它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局。
* 这与Symbol()不同的地方在于，Symbol()不会先搜索，而是直接创建一个新值)
可使用Symbol.keyFor()来查询全局注册表

)
```js
// 创建全局符号
let s = Symbol.for('foo');
console.log(Symbol.keyFor(s)); // foo
// 创建普通符号
let s2 = Symbol('bar');
console.log(Symbol.keyFor(s2)); // undefined,s2不是一个全局注册表 
```
###### 3. 使用符号作为属性
```js
let s1 = Symbol('foo'),;
let o = {
 [s1]: 'foo val'
}; 
```
>一点拓展
1.Object.getOwnPropertyNames()返回对象实例的常规属性数组
2.Object.getOwnPropertySymbols()返回对象实例的符号属性数组
3.Object.getOwnPropertyDescriptors()会返回同时包含常规和符号属性描述符的对象
4.Reflect.ownKeys()会返回两种类型的键;
*mark此位置,考虑find方法,利用正则表达式匹配字符串遍历寻找*

###### 4.常用内置符号 
ES6引入了常用内置符号（well-known symbol），用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为。这些内置符号都以 Symbol 工厂函数字符串属性的形式存在。内置符号属性都是不可写、不可枚举、不可配置的。
应用eg:🤔
for-of 循环会在相关对象上使用 Symbol.iterator 属性，那么就可以通过在自定义对象上重新定义Symbol.iterator 的值，来改变 for-of 在迭代该对象时的行为
>在提到 ECMAScript 规范时，经常会引用符号在规范中的名称，前缀为@@。比如，@@iterator 指的就是 Symbol.iterator。

###### 5. Symbol.asyncIterator  M
这个符号表示实现异步迭代器 API 的函数
mark,看完迭代器再说www






###### 6. Symbol.hasInstance ※
该方法决定一个构造器对象是否认可一个对象是它的实例
(*咋感觉有点像亲子鉴定*)
instanceof 操作符可以用来**确定一个对象实例的原型链上是否有原型**
```js
function Foo() {}
let f = new Foo();
console.log(f instanceof Foo); // true
```
ES6中:
```js
function Foo() {}
let f = new Foo();
console.log(Foo[Symbol.hasInstance](f)); // true
```
>这个属性定义在 Function 的原型上，因此默认在所有函数和类上都可以调用..
instanceof操作符会在原型链上寻找这个属性定义，就跟在原型链上寻找其他属性一样,
可以在继承的类上通过**静态方法**重新定义这个函数：
```js
class Bar {}
class Baz extends Bar {
 static [Symbol.hasInstance]() {
 return false;
 }
}
let b = new Baz();
console.log(Bar[Symbol.hasInstance](b)); // true,通过继承原型链上还是有Bar
console.log(b instanceof Bar); // true
console.log(Baz[Symbol.hasInstance](b)); // false,因为前面定义中return false了
console.log(b instanceof Baz); // false 
```
###### 7. Symbol.isConcatSpreadable ※
一个布尔值，如果是 true，则意味着对象应该用 Array.prototype.concat()打平其数组元素
(PS:**打平：数组打平(拍平)也称数组扁平化(flat)，就是将数组里面的数组打开，最后合并为一个数组**)
(PS:**concat() 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组**
eg:
```js
const array1 = ['a'];
const array2 = ['d'];
const array3 = array1.concat(array2);
console.log(array3);
// expected output: Array ["a","d"]
```
)

* 数组对象默认情况下会被打平到已有的数组
```js
let initial = ['foo'];
let array = ['bar'];
console.log(array[Symbol.isConcatSpreadable]); // undefined
console.log(initial.concat(array)); // ['foo', 'bar']
```
* false 会导致**整个对象**被追加到数组末尾
```js
array[Symbol.isConcatSpreadable] = false;
console.log(initial.concat(array)); // ['foo', ['bar']] 
```
* 类数组对象
(**所谓类数组对象，就是指可以通过索引属性访问元素并且拥有length 属性的对象**)
默认情况下会被追加到数组末尾
```js
let arrayLikeObject = { length: 1, 0: 'baz' };
console.log(arrayLikeObject[Symbol.isConcatSpreadable]); // undefined
console.log(initial.concat(arrayLikeObject)); // ['foo', {length: 1, 0: 'baz'}]
```
* true会导致这个类数组对象被打平到数组实例
(其他不是类数组对象的对象在 Symbol.isConcatSpreadable 被设置为 true 的情况下将被忽略)
```js
arrayLikeObject[Symbol.isConcatSpreadable] = true;
console.log(initial.concat(arrayLikeObject)); // ['foo', 'baz']
```
###### 8. Symbol.iterator M 迭代器





###### 9. Symbol.match   ※
用**正则表达式**去匹配字符串。由 String.prototype.match()方法使用
```js
console.log('foobar'.match(/bar/));
// ["bar", index: 3, input: "foobar", groups: undefined]
```
给这个方法传入非正则表达式值会导致该值被转换为 RegExp 对象!!
如果让方法直接使用参数，则可以重新定义 Symbol.match 函数以取代默认对正则表达式求值的行为，从而让match()方法使用非正则表达式实例。
Symbol.match 函数接收一个参数(下面②接的参数str)，是(target)调用 match()方法的字符串实例。：
```js
①class FooMatcher {
 static [Symbol.match](target) {
 return target.includes('foo');//这个根据是否include返回true/false
 }
}
console.log('foobar'.match(FooMatcher)); // true包括 foo
console.log('barbaz'.match(FooMatcher)); // false不包括 foo
②class StringMatcher {
 constructor(str) {
 this.str = str;//可接受非RegExp对象
 }
 [Symbol.match](target) {
 return target.includes(this.str);
 }
}
console.log('foobar'.match(new StringMatcher('foo'))); // true  foobar(target)包括foo(str)
console.log('barbaz'.match(new StringMatcher('qux'))); // false barbaz不包括qux
```
###### 10. Symbol.replace  
```js
// 'foobarbaz' 
console.log('foobarbaz'.replace(/bar/, 'qux'));
// 'fooquxbaz' 
```
给这个方法传入非正则表达式值会导致该值被转换为RegExp对象!!
同上(9)可以重新定义 Symbol.replace 函数以取代默认对正则表达式求值的行为
###### 11. Symbol.search  
该方法返回字符串中匹配正则表达式的索引
(PS:有点像数组考虑0时的位置)。
```js
console.log('foobar'.search(/bar/));
// 3 
```
###### 12. Symbol.species  ※
一个函数值，该函数作为创建派生对象的构造函数.
在**内置类型**中最常用，用于**对内置类型实例方法的返回值暴露实例化派生对象**的方法。用 Symbol.species 定义静态(**静态方法调用直接在类本身上进行，不能在类的实例上调用**)的获取器（getter）方法，可以**覆盖**新创建实例的原型定义(下面Baz创建时就被覆盖了)：
* getter:get语法将**对象属性**（下面例子是log属性）绑定到查询该属性时**将被调用的函数**(latest函数)
```js
const obj = {
  log: ['a', 'b', 'c'],
  get latest() {
    if (this.log.length === 0) {
      return undefined;
    }
    return this.log[this.log.length - 1];
  }
};
console.log(obj.latest);
// expected output: "c"
```
了解了getter后，
```js
class Bar extends Array {}
class Baz extends Array {
  // 覆盖 species 到父级的 Array **构造函数** 上
 static get [Symbol.species]() {
 return Array;
 }
}
let bar = new Bar();//Bar(0)
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true
bar = bar.concat('bar');//Bar(1)
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true

let baz = new Baz();//Baz(0)
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); // true
baz = baz.concat('baz'); //这样做了后baz变成Array(1)
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); //false
//在concat了一个'baz'后,baz就执行get返回Array了
//baz从Baz(0)变成Array(1)
//原型链上不再有Baz
/)
```


###### 13. Symbol.split
在匹配正则表达式的索引位置拆分字符串。由 String.prototype.split()方法使用
```js
console.log('foobarbaz'.split(/bar/));
// ['foo', 'baz']
```
###### 14. Symbol.toPrimitive @
将对象转换为相应的原始值。
对于一个自定义对象实例，通过在这个实例的 Symbol.toPrimitive 属性上**定义一个函数**可以**改变默认行为**
根据提供给这个函数的参数（string、number 或 default），可以控制返回的原始值：
```js
class Foo {}
let foo = new Foo();
console.log(3 + foo); // "3[object Object]"
console.log(3 - foo); // NaN
console.log(String(foo)); // "[object Object]"

class Bar {
 constructor() {
 this[Symbol.toPrimitive] = function(hint) {
 switch (hint) {
 case 'number':
 return 3;
 case 'string':
 return 'string bar';
 case 'default':
 default:
 return 'default bar';
 }
 }
 }
} 
let bar = new Bar();
console.log(3 + bar); // "3default bar"
console.log(3 - bar); // 0
console.log(String(bar)); // "string bar" 
```
###### 15. Symbol.toStringTag 
该字符串用于创建对象的默认字符串描述
通过 toString()方法获取对象标识时，会**检索由 Symbol.toStringTag 指定的实例标识符**，默认为"Object"。内置类型已经指定了这个值，但自定义类实例还需要明确定义：
```js
class Foo {}
let foo = new Foo();
console.log(foo); // Foo {}
console.log(foo.toString()); // [object Object]
console.log(foo[Symbol.toStringTag]); // undefined

class Bar {
 constructor() {
 this[Symbol.toStringTag] = 'Bar';
 }
}
let bar = new Bar();
console.log(bar); // Bar {}
console.log(bar.toString()); // [object Bar]
console.log(bar[Symbol.toStringTag]); // Bar 
```

###### 16. Symbol.unscopables 
该对象所有的以及继承的属性，都会从关联对象的 with 环境绑定中排除
设置这个符号并让其映射对应属性的键值为 true，就可以
阻止该属性出现在 with 环境绑定中，如下例所示：
```js
let o = { foo: 'bar' };
with (o) {
 console.log(foo); // bar
}
o[Symbol.unscopables] = {
 foo: true
};
with (o) {
 console.log(foo); // ReferenceError
} 
```
>不推荐用with,这个也不推荐用

#### ⑧.Object 类型
对象其实就是一组数据和功能的集合。对象通过 new 操作符后跟对象类型的名称来创建。
`let o = new Object(); `
>每个 Object 实例都有如下属性和方法。
 (1)constructor：用于创建当前对象的函数。在前面的例子中，这个属性的值就是 Object()函数。
 (2)hasOwnProperty(propertyName)：用于判断当前对象实例（不是原型）上是否存在给定的属性。**要检查的属性名必须是字符串**(如o.hasOwnProperty("name"))或符号。
 (3)isPrototypeOf(object)：用于判断当前对象是否为另一个对象的原型。
 (4)propertyIsEnumerable(propertyName)：用于判断给定的属性是否可以使用 for-in 语句枚举。与 hasOwnProperty()一样，属性名必须是字符串。
 //后三个差别较细微
 (5)toLocaleString()：返回对象的字符串表示，该字符串反映**对象所在的本地化执行环境**。//执行环境
 (6)toString()：返回对象的字符串表示。
 (7)valueOf()：返回对象对应的字符串、数值或布尔值表示。通常与 toString()的返回值相同。
>>严格来讲，ECMA-262 中对象的行为不一定适合 JavaScript 中的其他对象。比如浏览器环境中的 BOM 和 DOM 对象，都是由宿主环境定义和提供的宿主对象。而宿主对象不受 ECMA-262 约束，所以它们可能会也可能不会继承 Object。


### V.操作符
#### ①一元操作符
1. 递增/递减操作符
参考c++
```javascript
let s1 = "2";
let s2 = "z";
let b = false;
let f = 1.1;
let o = {
 valueOf() {
 return -1;
 }
};//对对象使用
s1++; // 值变成数值 3
s2++; // 值变成 NaN
b++; // 值变成数值 1
f--; // 值变成 0.10000000000000009（因为浮点数不精确）
o--; // 值变成-2 
```
2. 一元加和减
\+ -，正负，注意可以用来转换为Number类型
#### ②位操作符 
>有符号整数使用 32 位的前 31 位表示整数值。第 32 位表示数值的符号，0 表示正，1 表示负。这
一位称为符号位（sign bit）
正值以真正的二进制格式存储
负值以一种称为二补数（或补码）的二进制编码存储
在处理有符号整数时，我们无法访问第 31 位

js里，64 位数值会转换为 32 位数值，然后执行位操作，最后再把结果从 32 位转换为 64 位存储起来
>特殊值NaN 和Infinity在位操作中都会被当成 0 处理
1. 按位非 ~
返回数值的一补数
```js
let num1 = 25; // 二进制  00000000000000000000000000011001
let num2 = ~num1; // 二进制 11111111111111111111111111100110
console.log(num2); // -26 
```
**位操作的速度快得多**
2. 按位与 &
有两个操作数。按位与就是将两个数的每一个位对齐，然后基于**真值表中的规则**，对每一位执行相应的与操作(与运算)
3. 按位或 |
基本同&，(或运算)
4. 按位异或 ^
同上，(异或运算)
PS:**比对同样两个值执行按位或操作得到的结果小 1**
5. 左移 <<
```js
let oldValue = 2; // 等于二进制 10
let newValue = oldValue << 5; // 等于二进制 1000000，即十进制 64 
```
**左移保留它所操作数值的符号**
6. 有符号右移 >>
左移的逆运算
**ECMAScript 会用符号位的值来填充右移的空位，以得到完整的数值**
7. 无符号右移 >>>
**无符号右移会给空位补 0(无视符号位)**
正数不受影响，负数转为正数
#### ③布尔操作符
1. 逻辑非  !
```js
console.log(!false); // true
console.log(!"blue"); // false
console.log(!0); // true
console.log(!""); // true
console.log(!NaN/null/undefined); // true
```
2. 逻辑与 &&
对于a && b
(1)如果a是对象，则返回b。
(2)如果b是对象，则只有a求值为 true 才会返回该对象。
(3)如果a,b都是对象，则返回b。
(4) 如果有一个是 null/NaN/undefined，则返回 null/NaN/undefined。
**具有短路性**
3. 逻辑或 ||
如果有一个操作数不是布尔值，那么逻辑或操作符也不一定返回布尔值,遵循的规则同&&，**具有短路性**
```js
let myObject = a || b;
//如果 a 不是 null，则它的值就会赋给myObject。
//如果 a 是 null，则 b的值就会赋给 myObject。
```
#### ④乘性操作符
如果有不是数值的操作数，则先在后台用 Number()函数将其转换为数值**下面仅列出一些特例**
1. 乘法操作符 *
>Infinity 乘以 0，则返回 NaN
2. 除法操作符 /
>Infinity 除以 Infinity，则返回 NaN。
0 除以 0，则返回 NaN。
3. 取模操作符
>被除数是无限值，除数是有限值，则返回 NaN
被除数是有限值，除数是 0，则返回 NaN
Infinity 除以 Infinity，则返回 NaN
被除数是有限值，除数是无限值，则返回被除数
#### ⑤指数操作符
ECMAScript 7 新增了指数操作符，Math.pow()现在有了自己的操作符**
```js
console.log(Math.pow(16, 0.5); // 4
console.log(16** 0.5); // 4
```
#### ⑥加性操作符
* 如果有任一操作数是字符串、布尔值、null、undefined，则先在后台使用 Number()将其转
换为数值，然后再根据前面的规则执行数学运算。
* 如果有任一操作数是对象，则调用其 valueOf()方法取得表示它的数值。如果对象没有 valueOf()方法，则调用其 toString()方法，然后再将得到的字符串转换为数值。
1. 加法操作符
>Infinity 加-Infinity，则返回 NaN
+0 加+0，则返回+0
-0 加+0，则返回+0
-0 加-0，则返回-0
```js
let result2 = 5 + "5"; // 一个数值和一个字符串
console.log(result2); // "55"
//有字符串，另一个转化为字符串再前后拼接
```
2. 减法操作符
>±Infinity 减 ±Infinity，则返回 NaN
Infinity 减-Infinity，则返回 Infinity
-Infinity 减 Infinity，则返回-Infinity
+0 减+0，则返回+0
+0 减-0，则返回-0
-0 减-0，则返回+0
#### ⑦关系操作符 > <...
* 如果操作数都是字符串，则逐个比较字符串中对应字符的编码
`let result = "Brick" < "alphabet"; // true`
(PS:ASCII码中所有大写字母均小于小写字母)
`let result = "23" < "3"; // true`
(PS:字符"2"的编码是 50，而字符"3"的编码是 51)
```JS
let result = "a" < 3; 
// 因为"a"会转换为 NaN，所以结果是 false
```
* 如果有任一操作数是对象，则调用其 valueOf()方法，取得结果后再根据前面的规则执行比较。
如果没有 valueOf()操作符，则调用 toString()方法，取得结果后再根据前面的规则执行比较
#### ⑧相等操作符
1. 等于和不等于(在比较之前执行转换)
* null 和 undefined 相等
* null 和 undefined 不能转换为其他类型的值再进行比较
* 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等
* 如果一个操作数是对象，另一个操作数不是，则调用对象的 valueOf()方法取得其原始值比较
* **如果有任一操作数是 NaN，则相等操作符返回 false，不相等操作符返回 true**`NaN == NaN`//false!!
2. 全等和不全等(比较之前不执行转换)
```js
let result1 = ("55" == 55); // true，转换后相等
let result2 = ("55" === 55); // false，不相等，因为数据类型不同
let result3 = ("55" != 55); // false，转换后相等
let result4 = ("55" !== 55); // true，不相等，因为数据类型不同
null === undefined //false~~!!
```
#### ⑨条件操作符
```js 
variable = boolean_expression ? true_value : false_value;`
let max = (num1 > num2) ? num1 : num2;//始终取到num1 2中的max！
```
#### ⑩赋值操作符 =
可以用来+=之类的简写
#### ⒒逗号操作符 ,
```js
let num1 = 1, num2 = 2, num3 = 3; 
let num = (5, 1, 4, 8, 0); // num 的值为 0
```
### VI.语句
#### ①if语句
```js
if (condition1) {
  statement1
} 
else if(condition2){
   statement2
}
else {
  statement3
} 
```
>condition可以是任何表达式，并且求值结果不一定是布尔值。ECMAScript 会自动调用 Boolean()函数将这个表达式的值转换为布尔值
#### ②do-while 语句
```js
do {
 statement
} while (expression);
```
>后测试循环经常用于这种情形：循环体内代码在退出前至少要执行一次
#### ③while 语句
```js
while(expression) {
  statement 
}
```
#### ④for 语句
 >for 循环的初始化代码中，其实是可以不使用变量声明关键字的。不过，初始化定义的迭代器变量在循环执行完成后几乎不可能再用到了。因此，最清晰的写法是使用**let 声明迭代器变量**，这样就可以将这个变量的作用域限定在循环中
#### ⑤for-in 语句
```js
for (const propName in window) {
 document.write(propName);
} 
```
>例子使用 for-in 循环显示了 BOM 对象 window 的所有属性。每次执行循环，都会给变量propName 赋予一个 window 对象的属性作为值，直到window 的所有属性都被枚举一遍。为了确保这个局部变量不被修改，推荐使用 const

* **for-in 语句不能保证返回对象属性的顺序**
* 如果 for-in 循环要迭代的变量是 null 或 undefined，则不执行循环体
#### ⑥for-of 语句  @
```js
for (const el of [2,4,6,8]) {
 document.write(el);
} 
```
* 显示了一个包含 4 个元素的数组中的所有元素。
* for-of 循环会按照可迭代对象的 next()方法产生值的顺序迭代元素
>ES2018 对 for-of 语句进行了扩展，增加了 for-await-of 循环，以支持生成期约（promise）的异步可迭代对象
#### ⑦标签语句
给语句加标签(取个名,感觉像c艹里那个[标签]：一段语句,用goto [标签]跳转执行位置)
label: statement
#### ⑧break 和 continue 语句
* break 语句用于立即退出循环，强制执行循环后的下一条语句
* continue 语句也用于立即退出循环，但会再次从循环顶部开始执行
```js
let num = 0;
outermost:
for (let i = 0; i < 10; i++) {
 for (let j = 0; j < 10; j++) {
   if (i == 5 && j == 5) {
 continue outermost;
 }
 num++;
 }
}
console.log(num); // 95
```
(PS:55次之时continue到外层循环处，
从i=6继续，意味着i=5,j=5~9的内层循环num++不会执行，就总共少了5次)
#### ⑨with 语句
将**代码作用域**设置为特定的对象
with (expression) statement;
```js
with(location) {
 let qs = search.substring(1);
 let hostName = hostname;
 let url = href;
} 
```
(PS:每个变量首先会被认为是一个局部变量。
如果没有找到该局部变量，则会搜索 location 对象，看它是否有一个同名的属性。如果有，则该变量会被求值为 location 对象的属性)
>由于 with 语句影响性能且难于调试其中的代码，通常不推荐在产品代码中使用 with语句，且严格模式下报错
#### ⑩switch 语句
（同C语言)
```js
switch (expression) {
 case value1:
 statement
 break;
 case value2:
 statement
 break;
 default:
 statement
} 
``` 
switch 语句可以用于所有数据类型（在很多语言中，它只能用于数值），因此可以使用字符串甚至对象。
其次，**条件的值**不需要是常量，也**可以是变量或表达式**
>switch 语句在比较每个条件的值时使用全等操作符，因此不会强制转换数据类型（比如，字符串"10"不等于数值 10）

### VII.函数 @ No.10
封装语句！
function functionName(arg0, arg1,...,argN) {
 statements
} 

return 语句也可以不带返回值。这时候，函数会立即停止执行并返回 undefined。这种用法最常
用于提前终止函数执行，并不是为了返回值
>最佳实践是函数要么返回值，要么不返回值。只在个条件下返回值的函数会带来麻烦，尤其是调试时。
严格模式对函数也有一些限制：
* 函数不能以 eval 或 arguments 作为名称；
* 函数的参数不能叫 eval 或 arguments；
* 两个命名参数不能拥有同一个名称
### VIII.ECMAScript和JavaScript
>* JavaScript 的核心语言特性在 ECMA-262 中以**伪语言 ECMAScript**的形式来定义。ECMAScript 包含所有基本语法、操作符、数据类型和对象，能完成基本的计算任务，但**没有提供获得输入和产生输出的机制**。
* 与其他语言不同，ECMAScript 不区分整数和浮点值，只有 Number 一种数值数据类型。
* Object 是一种复杂数据类型，它是这门语言中所有对象的基类。
* 严格模式为这门语言中某些容易出错的部分施加了限制。

## No.4
### I.原始值与引用值
* 原始值（primitive value）————最简单的数据。6 种原始值：Undefined、Null、Boolean、Number、String 和 Symbol。
* 引用值（reference value）————由多个值构成的对象

1. 保存原始值的变量是按值访问的，因为我们操作的就是存储在变量中的实际值。
2. 引用值是**保存在内存中的对象**。JSc 不允许直接访问内存位置，因此不能直接操作对象所在的内存空间。在操作对象时，实际上操作的是**对该对象的引用**（reference）而非实际的对象本身。为此，保存引用值的变量是按引用（by reference）访问的。
#### ①动态属性
只有引用值（如对象）可以动态随时添加、修改和删除其属性和方法
>若使用new，如new String("xxx")定义，为Object
#### ②复制值
1. 原始值复制即创建一个新的独立变量
2. 引用值复制的值实际上是一个指针，它指向存储在堆内存中的对象。操作完成后，两个变量实际上指向同一个对象
eg:
```js
let obj1 = new Object();
let obj2 = obj1;
obj1.name = "Nicholas";
console.log(obj2.name); // "Nicholas"
```
#### ③传递参数
**所有函数的参数都是按值传递的**;
如果是原始值，那么就跟原始值变量的复制一样;
如果是引用值，那么就跟引用值变量的复制一样
```js
function setName(obj) {
 obj.name = "Nicholas";
 obj = new Object();//此处已经创建了一个新object对象
 obj.name = "Greg";
}
let person = new Object();
setName(person);
console.log(person.name); // "Nicholas" 
//参数的值改变了，原始的引用不变！说明对象是按值传递的而非按引用传递！
```
(PS:person被复制给obj,两者指向同一个对象，即使对象是按值传进函数的，obj 也会通过引用访问对象，obj 指向的对象保存在全局作用域的堆内存上，此处修改，外面的原引用person也被修改)
(ps:带有"Greg"的obj对象在函数结束时即被销毁；
这虽然是，在局部作用域中修改对象而变化反映到全局，但对象是按值传递的而非按引用传递！)
#### ④确定类型
>楔子：typeof 虽然对原始值很有用，但它对引用值的用处不大。我们通常不关心一个值是不是对象，而是**想知道它是什么类型的对象**。为了解决这个问题，ECMAScript 提供了 instanceof 操作符!!
```js
console.log(person instanceof Object); // 变量 person 是 Object 吗？
console.log(colors instanceof Array); // 变量 colors 是 Array 吗？
console.log(pattern instanceof RegExp); // 变量 pattern 是 RegExp 吗？
```
返回 bool
* 如果变量是给定引用类型（由其原型链决定）的实例，则 instanceof 操作符返回 true
* 所有引用值都是 Object 的实例，因此通过 instanceof 操作符检测任何引用值和Object 构造函数都会返回 true
* 用 instanceof 检测原始值，则始终会返回 false
### Ⅱ.执行上下文与作用域
#### 〇.上下文解释
1. 上下文定义：
变量或函数的上下文**决定了它们可以访问哪些数据，以及它们的行为**。每个上下文都有一个关联的变量对象（variable object），而这个上下文中定义的所有变量和函数都存在于这个对象上。
>* 在浏览器中，全局上下文就是我们常说的 window 对象，因此所有通过 var 定义的全局变量和函数都会成为 window 对象的属性和方法。
>* 使用 let 和 const 的顶级声明不会定义在全局上下文中，但在作用域链解析上效果是一样的。上下文在其所有代码都执行完毕后会被销毁，包括定义在它上面的所有变量和函数（全局上下文在应用程序退出前才会被销毁，比如关闭网页或退出浏览器）。
2. 函数的上下文：
**每个函数调用都有自己的上下文**。当代码执行流进入函数时，函数的上下文被推到一个上下文栈上。在函数执行完之后，上下文栈会弹出该函数上下文，将控制权返还给之前的执行上下文。**ECMAScript程序的执行流就是通过这个上下文栈进行控制的**

3. 作用域链 ：
上下文中的代码在执行时会创建变量对象的一个作用域链（scope chain）。这个作用域链**决定了各级上下文中的代码在访问变量和函数时的顺序**。代码**正在执行的上下文的变量对象始终位于作用域链的最前端**。
>如果上下文是函数，则其活动对象（activation object）用作变量对象。活动对象最初只有一个定义变量：arguments。（全局上下文中没有这个变量。）
作用域链中的下一个变量对象来自包含上下文，再下一个对象来自再下一个包含上下文。以此类推直至全局上下文；全局上下文的变量对象始终是作用域链的最后一个变量对象。

eg:
(大段文字解释不易，上代码😭)
```js
var color = "blue";
function changeColor() {
 if (color === "blue") {
 color = "red";
 } else {
 color = "blue";
 }
}
changeColor();
```
(ps:函数 changeColor()的作用域链包含两个对象:
1.它自己的变量对象（就是定义 arguments 对象的那个）
2.全局上下文的变量对象。这个函数内部之所以能够访问*全局*变量color，就是因为可以在作用域链中找到它)
**内部上下文可以通过作用域链访问外部上下文中的一切，但外
部上下文无法访问内部上下文中的任何东西**
>函数参数被认为是当前上下文中的变量，因此也跟上下文中的其他变量遵循相同的访问规则
#### ①作用域链增强
* with语句
* try/catch 语句的 catch 块

这两种情况下，都会在作用域链前端添加一个变量对象。
* 对 with 语句来说，会向作用域链前端添加指定的对象；如 `with(location){xx}`将 location 对象作为上下文，因此location会被添加到作用域链前端
* 对 catch 语句而言，则会创建一个新的变量对象，这个变量对象会包含要抛出的错误对象的声明。

#### ②变量声明
1.  使用 var 的函数作用域声明
用 var 声明变量时，变量会被自动添加到最接近的上下文。在函数中，最接近的上下文就是函数的局部上下文
2. 关于let const
由于 const 声明暗示变量的值是单一类型且不可修改，JavaScript 运行时编译器可以将其所有实例都替换成实际的值，而不会通过查询表进行变量查找。
>开发实践表明，如果开发流程并不会因此而受很大影响，就应该**尽可能地多使用const 声明**，除非确实需要一个将来会重新赋值的变量(let也比var好)。这样可以从根本上保证提前发现重新赋值导致的bug
3. 标识符查找
搜索开始于作用域链前端，以给定的名称搜索对应的标识符。如果在局部上下文中找到该标识符，则搜索停止，变量确定；如果没有找到变量名，则继续沿作用域链搜索

如果局部上下文中有一个同名的标识符，那就不能在该上下文中引用父上下文中的同名标识符，如下面的例子所示：
```js
var color = 'blue';
function getColor() {
 let color = 'red';
 return color;
}
console.log(getColor()); // 'red'
```
(ps:在局部变量 color 声明之后的任何代码都无法访问全局变量color，除非使用完全限定的写法 window.color。)
### Ⅲ.垃圾回收
1. JS垃圾回收定义:执行环境负责在代码执行时管理内存
2. **实现基本思路**:确定哪个变量不会再使用，然后释放它占用的内存。
这个过程是周期性的，即垃圾回收程序每隔一定时间（或者说在代码执行过程中某个预定的收集时间）就会自动运行
下面引入标记未使用的变量的两个方法
#### ①标记清理(常用)
1. 实现思路
>* 当变量进入上下文，比如在函数内部声明一个变量时，这个变量会被加上存在于上下文中的标记。
>* 而在上下文中的变量，逻辑上讲，永远不应该释放它们的内存，因为只要上下文中的代码在运行，就有可能用到它们。
>* 当变量离开上下文时，也会被加上离开上下文的标记.
2. 具体标记方法
>给变量加标记的方式有很多种。
比如，当变量进入上下文时，反转某一位；或者可以维护“在上下文中”和“不在上下文中”两个变量列表，可以把变量从一个列表转移到另一个列表。
3. 执行
>垃圾回收程序运行的时候
>* 会标记内存中存储的所有变量（记住，标记方法有很多种）。
>* 然后，它会将所有在上下文中的变量，以及被在上下文中的变量引用的变量的标记去掉。
>* 在此之后再被加上标记的变量就是待删除的了，原因是任何在上下文中的变量都访问不到它们了。
>* 随后垃圾回收程序做一次内存清理，销毁带标记的所有值并收回它们的内存。
#### ②引用计数(不常用)
1. 实现思路
对每个值都记录它被引用的次数
2. 具体计数方法
>* 声明变量并给它赋一个引用值时，这个值的引用数为 1。
>* 如果同一个值又被赋给另一个变量，那么引用数+ 1。
>* 如果保存对该值引用的变量被其他值给覆盖了，那么引用数- 1。
>* 当一个值的引用数为 0 时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。
>* 垃圾回收程序下次运行的时候就会释放引用数为 0 的值的内存。
3. 此方法BUG：循环引用
```js
function problem() {
 let objectA = new Object();
 let objectB = new Object();
 objectA.someOtherObject = objectB;
 objectB.anotherObject = objectA;
} 
```
(PS:在这个例子中，objectA 和 objectB 通过各自的属性相互引用，意味着它们的引用数都是 2.
它们的引用数永远不会变成 0。如果函数被多次调
用，则会导致大量内存永远不会被释放)
#### ③垃圾回收性能
1. 问题产生： 
垃圾回收程序会周期性运行，如果内存中分配了很多变量，则可能造成性能损失，因此垃圾回收的时间调度很重要
2. 解决思路：
在写代码时就要做到：无论什么时候开始收集垃圾，都能让它尽快结束工作。
* 现代垃圾回收程序会基于对 JavaScript 运行时环境的探测来决定何时运行。
* eg:“在一次完整的垃圾回收之后，V8 的堆增长策略会根据活跃对象的数量外加一些余量来确定何时再次垃圾回收。”
* JavaScript 引擎的垃圾回收程序被调优为动态改变分配变量、字面量或数组槽位等会触发垃圾回收的阈值:
如果垃圾回收程序回收的内存不到已分配的 15%，这些变量、字面量或数组槽位的阈值就会翻倍。如果有一次回收的内存达到已分配的 85%，则阈值重置为默认值
> IE 中，window.CollectGarbage()方法会立即触发垃圾回收。
#### ④内存管理
优化内存占用的最佳手段就是保证在执行代码时只保存必要的数据。如果数据不再必要，那么**把它设置为 null**，从而释放其引用。这也可以叫作**解除引用**——最适合全局变量和全局对象的属性。
>解除对一个值的引用并不会自动导致相关内存被回收。解除引用的关键在于确保相关
的值已经不在上下文里了，因此它在下次垃圾回收时会被回收
1. 通过 const 和 let 声明提升性能
因为 const和 let 都以块（而非函数）为作用域，所以相比于使用 var，使用这两个新关键字可能会更早地让垃圾回收程序介入，尽早回收应该回收的内存
2. 隐藏类和删除操作
```js
function Article() {
 this.title = 'Inauguration Ceremony Features Kazoo Band';
}
let a1 = new Article();
let a2 = new Article(); 
```
V8 会在后台配置，让这两个类实例共享相同的隐藏类，因为这两个实例共享同一个构造函数和原型。假设之后又添加了下面这行代码：
`a2.author = 'Jake';`
此时两个 Article 实例就会对应两个不同的隐藏类。
根据这种操作的频率和隐藏类的大小，这**有可能对性能产生明显影响。**
解决方案就是避免 JavaScript 的“先创建再补充"（ready-fire-aim）式的动态属性赋值，并在构造函数中一次性声明所有属性，如下所示：
```js
function Article(opt_author) {
 this.title = 'Inauguration Ceremony Features Kazoo Band';
 this.author = opt_author;
}
let a1 = new Article();
let a2 = new Article('Jake'); 
```
>(PS:)使用 delete 关键字会导致生成相同的隐藏类片段.因此不如设置null
3. 内存泄漏
* 意外声明全局变量是最常见但也最容易修复的内存泄漏问题(在 window 对象上创建的属性，只要 window 本身不被清理就不会消失)
* 使用 JavaScript 闭包很容易在不知不觉间造成内存泄漏。
```js
let outer = function() {
 let name = 'Jake';
 return function() {
 return name;
 };
};
```
* 调用 outer()会导致分配给 name 的内存被泄漏。以上代码执行后创建了一个内部闭包，只要返回的函数存在就不能清理 name!(闭包一直在引用着)
4. 静态分配与对象池
* 如果有很多对象被初始化，然后一下子又都超出了作用域，那么浏览器就会采用更激进的方式调度垃圾回收程序运行，这样会影响性能。**解决方案是不要动态创建矢量对象**
* 不动态创建意味着需修改已有的对象.
关于如何里创建矢量可以不让垃圾回收调度程序盯上，**在初始化的某一时刻，可以创建一个对象池，用来管理一组可回收的对象**。应用程序可以向这个对象池请求一个对象、设置其属性、使用它，然后在操作完成后再把它还给对象池。
* 由于没发生对象初始化，垃圾回收探测就不会发现有对象更替，因此垃圾回收程序就不会那么频繁地运行。
```js
// vectorPool 是已有的对象池
let v1 = vectorPool.allocate();
let v2 = vectorPool.allocate();
let v3 = vectorPool.allocate();
v1.x = 10;
v1.y = 5;
v2.x = -3;
v2.y = -6;
addVector(v1, v2, v3);
console.log([v3.x, v3.y]); // [7, -1]
vectorPool.free(v1);
vectorPool.free(v2);
vectorPool.free(v3);
// 如果对象有属性引用了其他对象
// 则这里也需要把这些属性设置为 null
v1 = null;
v2 = null;
v3 = null; 
```
>对象池只按需分配矢量（在对象不存在时创建新的，在对象存在时则复用存在的），本质上是一种**贪婪算法**，有单调增长但为静态的内存。这个对象池必须使用某种结构维护所有对象，**数组是比较好的选择**(PS:但数组大小最好提前确定，比如要是发现数组长度100不够用，会删掉它创个200的，这会引起不必要的垃圾回收)
### IV.一些小结
* **原始值大小固定，因此保存在栈内存上**。
* **引用值是对象，存储在堆内存上**。
* 包含引用值的变量实际上只包含指向相应对象的一个指针，而不是对象本身(SO从一个变量到另一个变量复制引用值只会复制指针，因此结果是两个变量都指向同一个对象)
* typeof 操作符可以确定值的原始类型，而 **instanceof 操作符用于确保值的引用类型**
* **函数或块的局部上下文**不仅可以访问自己作用域内的变量，而且也可以访问任何包含上下文乃至全局上下文中的变量。
* **全局上下文**只能访问全局上下文中的变量和函数，不能直接访问局部上下文中的任何数据。
* 解除变量的引用不仅可以消除循环引用，而且对垃圾回收也有帮助。**为促进内存回收，全局对象、全局对象的属性和循环引用都应该在不需要时解除引用。**
## No5.基本引用类型 
1. 引用值（或者对象）是某个特定引用类型的实例。在ECMAScript 中，引用类型是把数据和功能组织到一起的结构，很像“类”但不是“类”。
2. JavaScript 是一门面向对象语言，但ECMAScript 缺少传统的面向对象编程语言所具备的某些基本结构，包括类和接口。**引用类型**有时候也被称为**对象定义**，因为它们描述了自己的对象应有的属性和方法。
3. 对象被认为是某个特定引用类型的实例。新对象通过使用 new 操作符后跟一个构造函数（constructor）来创建。
eg:`let now = new Date();`
### I.Date
>Date 类型将日期保存为自协调世界时（UTC，Universal Time Coordinated）时间 1970 年 1 月 1 日午夜（零时）至今所
经过的毫秒数。
* Date.parse()方法接收一个表示日期的字符串参数，将这个字符串转换为表示该日期的毫秒数,可以直接`let someDate = new Date("May 23, 2019");`
* Date.UTC()方法也返回日期的毫秒表示，但使用的是跟 Date.parse()不同的信息来生成这个值
```js
// GMT 时间 2005 年 5 月 5 日下午 5 点 55 分 55 秒
let allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55));
```
(ps:小时，月数是零起点的.直接Data创建的对应本地时间)
```js
// 本地时间 2005 年 5 月 5 日下午 5 点 55 分 55 秒
let allFives = new Date(2005, 4, 5, 17, 55, 55); 
```
* Date.now()方法，返回表示方法执行时日期和时间的毫秒数
#### ①继承的方法
Date 类型重写了 toLocaleString()、toString()和 valueOf()方法
1. `toString() - Thu Feb 1 2019 00:00:00 GMT-0800 (Pacific Standard Time)`
2. `toLocaleString() - 2/1/2019 12:00:00 AM`
3. Date 类型的 valueOf()方法被重写后返回的是日期的毫秒表示。
因此，操作符（如小于号和大于号）可以直接使用它返回的值
>会因浏览器而异。因此不能用于在用户界面上一致地显示日期。仅调试用吧
#### ②日期格式化方法
* toDateString()显示日期中的周几、月、日、年（格式特定于实现）；
* toTimeString()显示日期中的时、分、秒和时区（格式特定于实现）；
* toLocaleDateString()显示日期中的周几、月、日、年（格式特定于实现和地区）；
* toLocaleTimeString()显示日期中的时、分、秒（格式特定于实现和地区）；
* toUTCString()显示完整的 UTC 日期（格式特定于实现）。
>会因浏览器而异。因此不能用于在用户界面上一致地显示日期。仅调试用吧
#### ③日期/时间组件方法
//get改set并加上参数即为重新设置
* getTime() 返回日期的毫秒表示；与 valueOf()相同
* getFullYear() 返回 4 位数年（即 2019 而不是 19）
* getMonth() 返回日期的月（0 表示 1 月，11 表示 12 月）
* getDate() 返回日期中的日（1~31）
* getDay() 返回日期中表示周几的数值（0 表示周日，6 表示周六）
* getHours() 返回日期中的时（0~23）
* getMinutes() 返回日期中的分（0~59）
* getSeconds() 返回日期中的秒（0~59）
### II.RegExp(正则表达式)
1. 字面量形式定义`let expression = /pattern/flags;`
这个正则表达式的 pattern（模式）可以是任何简单或复杂的正则表达式，包括字符类、限定符、分组、向前查找和反向引用。
每个正则表达式可以带零个或多个 flags（标记），用于控制正则表达式的行为
**匹配模式的标记**:
 g：全局模式，表示查找字符串的全部内容，而不是找到第一个匹配的内容就结束。
 i：不区分大小写，表示在查找匹配时忽略 pattern 和字符串的大小写。
 m：多行模式，表示查找到一行文本末尾时会继续查找。
 y：粘附模式，表示只查找从 lastIndex 开始及之后的字符串。
 u：Unicode 模式，启用 Unicode 匹配。
 s：dotAll 模式，表示元字符.匹配任何字符（包括\n 或\r）
```js
// 匹配第一个"bat"或"cat"，忽略大小写
let pattern1 = /[bc]at/i;
// 匹配所有以"at"结尾的三字符组合，忽略大小写
let pattern2 = /.at/gi; 
```
所有元字符在模式中也必须转义
即( [ { \ ^ $ | ) ] } ? * + .
```js
// 匹配第一个"[bc]at"，忽略大小写
let pattern3 = /\[bc\]at/i;
// 匹配所有".at"，忽略大小写
let pattern4 = /\.at/gi; 
```
2. RegExp 构造函数定义
```js
// 跟 pattern1 一样
let pattern2 = new RegExp("[bc]at", "i");
```
>因为 RegExp 的模式参数是字符串，所以在某些情况下需要二次转义。所有元字符都必须二次转义，包括转义字符序列，如\n（\转义后的字符串是`\\`，在正则表达式字符串中则要写成`\\\\`）。
如字面量`/\[bc\]at/ `对应的字符串`"\\[bc\\]at"`
#### ①RegExp 实例属性
* 每个 RegExp 实例都有下列属性
```js
let pattern1 = /\[bc\]at/i;//同
//let pattern1 = new RegExp("\\[bc\\]at", "i");
console.log(pattern1.global); // false
console.log(pattern1.ignoreCase); // true
console.log(pattern1.multiline); // false
console.log(pattern1.lastIndex); // 0
console.log(pattern1.source); // "\[bc\]at"
console.log(pattern1.flags); // "i" 
```
#### ②RegExp 实例方法
1. exec()
如果找到了匹配项，则**返回包含第一个匹配信息的数组**；如果没找到匹配项，则返回null
```js
let text = "mom and dad and baby";
let pattern = /mom( and dad( and baby)?)?/gi;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches.input); // "mom and dad and baby"
console.log(matches[0]); // "mom and dad and baby"
console.log(matches[1]); // " and dad and baby"
console.log(matches[2]); // " and baby" 
```
* 模式包含两个捕获组：最内部的匹配项" and baby"，以及外部的匹配项" and dad and baby"。调用 exec()后找到了一个匹配项。因为**整个字符串匹配模式，所以 matchs数组的 index 属性就是 0**。数组的第一个元素是匹配的整个字符串，第二个元素是匹配第一个捕获组的字符串，第三个元素是匹配第二个捕获组的字符串;
>如果没有设置全局标记g，则无论对同一个字符串调用多少次 exec()，也只会返回第一个匹配的信息。下面的代码若不加g, lastIndex(反映上次匹配的最后一个字符的索引)始终为0.matches[0]始终为cat
```js
let text = "cat, bat, sat, fat";
let pattern = /.at/g;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat
console.log(pattern.lastIndex); // 3
matches = pattern.exec(text);
console.log(matches.index); // 5
console.log(matches[0]); // bat
console.log(pattern.lastIndex); // 8
matches = pattern.exec(text);
console.log(matches.index); // 10
console.log(matches[0]); // sat
console.log(pattern.lastIndex); // 13 
```
* 如果模式设置了粘附标记 y，则每次调用 exec()就只会在 lastIndex 的位置上寻找匹配项。粘附标记覆盖全局标记。
```js
let text = "cat, bat, sat, fat";
let pattern = /.at/y;
let matches = pattern.exec(text);
console.log(matches.index); // 0
console.log(matches[0]); // cat
console.log(pattern.lastIndex); // 3
// 以索引 3 对应的字符开头找不到匹配项，因此 exec()返回 null
// exec()没找到匹配项，于是将 lastIndex 设置为 0
matches = pattern.exec(text);
console.log(matches); // null
console.log(pattern.lastIndex); // 0
// 向前设置 lastIndex 可以让粘附的模式通过 exec()找到下一个匹配项：
pattern.lastIndex = 5;
matches = pattern.exec(text);
console.log(matches.index); // 5
console.log(matches[0]); // bat
console.log(pattern.lastIndex); // 8 
```
2. test()(经常用在 if 语句中)
```js
let text = "000-00-0000";
let pattern = /\d{3}-\d{2}-\d{4}/;
if (pattern.test(text)) {
 console.log("The pattern was matched.");
} 
```
3. toLocaleString()和 toString()
* 返回的都是其字面量的形式
```js
let pattern = new RegExp("\\[bc\\]at", "gi");
console.log(pattern.toString()); // /\[bc\]at/gi
console.log(pattern.toLocaleString()); // /\[bc\]at/gi
```
#### ③RegExp 构造函数属性
* 下面注释为简写形式
```js
let text = "this has been a short summer";
let pattern = /(.)hort/g;
//用于搜索任何后跟"hort"的字符，
//并把第一个字符放在了捕获组中
if (pattern.test(text)) {
 console.log(RegExp.input); // this has been a short summer
 console.log(RegExp.leftContext); // this has been a
 console.log(RegExp.rightContext); // summer
 console.log(RegExp.lastMatch); // short
 console.log(RegExp.lastParen); // s
//console.log(RegExp.$_); // this has been a short summer
//console.log(RegExp["$`"]); // this has been a
//console.log(RegExp["$'"]); // summer
//console.log(RegExp["$&"]); // short
//console.log(RegExp["$+"]); // s 
} 
```
* 下面模式包含两个捕获组。
调用 test()搜索字符串之后返回true，通过 RegExp 构造函数的$1 和$2 属性取得两个捕获组匹配的内容
```js
let text = "this has been a short summer";
let pattern = /(..)or(.)/g;
if (pattern.test(text)) {
 console.log(RegExp.$1); // sh
 console.log(RegExp.$2); // t
} 
```
>RegExp 构造函数的所有属性都没有任何 Web 标准出处，因此不要在生产环境中使
用它们。
#### ④模式局限
ECMAScript 对正则表达式的支持有了长足的进步，但仍然缺少 Perl 语言中的一些高级特性
### III.原始值包装类型
为了方便操作原始值，ECMAScript 提供了 3 种特殊的引用类型：Boolean、Number 和 String。
每当用到**某个原始值的方法或属性时**，后台都会创建一个相应原始包装类型的对象.
```js
let s1 = "some text";
let s2 = s1.substring(2);
```
等同于
```js
let s1 = new String("some text");
//在通过 new 实例化引用类型后，
//得到的实例会在离开作用域时被销毁，
//自动创建的原始值包装对象只存在于访问它的那行代码执行期间
let s2 = s1.substring(2);
s1 = null; 
```
下面第二行代码运行时会临时创建一个 String 对象，而当第三行代码执行时，这个对象已经被销毁了。实际上，第三行代码在这里创建了自己的 String 对象，但这个对象没有 color 属性
```js
let s1 = "some text";
s1.color = "red";
console.log(s1.color); // undefined
```
**原始值包装类型的实例上调用 typeof 会返回"object"**
```js
let value = "25";
let number = Number(value); // 转型函数
console.log(typeof number); // "number"
let obj = new Number(value); // 构造函数
console.log(typeof obj); // "object" 
```
#### ①Boolean

```js
let falseObject = new Boolean(false);
//引用值（Boolean 对象）
//这个object的值是false,但object本身为true
let result = falseObject && true;
console.log(result); // true
let falseValue = false;
result = falseValue && true;
console.log(result); // false 
```
Boolean 对象是 Boolean 类型的实例，在使用instaceof 操作符时返回 true，但对原始值则返回 false
```js
console.log(typeof falseObject); // object
console.log(typeof falseValue); // boolean
console.log(falseObject instanceof Boolean); // true
console.log(falseValue instanceof Boolean); // false 
```
>强烈建议永远不要使用Boolean,**用原始布尔值即可**
#### ②Number
```js
let numberObject = new Number(10);
//valueOf()方法返回 Number 对象表示的原始数值
let num = 10;
console.log(num.toString()); // "10"
console.log(num.toString(2)); // "1010"
console.log(num.toString(8)); // "12"
console.log(num.toString(10)); // "10"
console.log(num.toString(16)); // "a" 
```
1. toFixed()方法返回包含指定小数点位数的数值字符串，如：
```js
let num = 10;
console.log(num.toFixed(2)); 
// "10.00" (若不够精确，四舍五入)
```
2. toExponential()，返回以科学记数法（也称为指数记数法）表示的数值字符串
```js
let num = 10;
console.log(num.toExponential(1)); // "1.0e+1" 
```
3. toPrecision()方法会根据数值和精度来决定调用 toFixed()还是toExponential()。
```js
let num = 99;
console.log(num.toPrecision(1)); // "1e+2"
console.log(num.toPrecision(2)); // "99"
console.log(num.toPrecision(3)); // "99.0"
```
>不建议直接实例化 Number 对象,同上面Boolean
```js
let numberObject = new Number(10);
let numberValue = 10;
console.log(typeof numberObject); 
// "object"
console.log(typeof numberValue); 
// "number"
console.log(numberObject instanceof Number); 
// true
console.log(numberValue instanceof Number); 
// false 
```
4. isInteger()方法与安全整数
* 辨别一个数值是否保存为整数
```js
console.log(Number.isInteger(1)); 
// true
console.log(Number.isInteger(1.00)); 
// true
console.log(Number.isInteger(1.01)); 
// false
```
>围从 Number.MIN_SAFE_INTEGER(2的53次方+1)到Number.MAX_SAFE_INTEGER(2的53次方 - 1)。对超出这个范围的数值，即使尝试保存为整数，IEEE 754 编码格式也意味着二进制值可能会表示一个完全不同的数值
* 鉴别整数是否在这个范围内，可以使用Number.isSafeInteger()方法：
```js
console.log(Number.isSafeInteger(-1 * (2 ** 53))); // false
console.log(Number.isSafeInteger(-1 * (2 ** 53) + 1)); // true 
```
#### ④String
* String 是对应字符串的引用类型
* 每个 String 对象都有一个 length 属性，表示字符串中字符的数量
1. JavaScript 字符
>JavaScript 字符串由 16 位码元（code unit）组成。对多数字符来说，每 16 位码元对应一个字符

* charAt()方法返回给定索引位置的字符
```js
let message = "abcde";
console.log(message.charAt(2)); // "c" 
```
JavaScript 字符串使用了两种 Unicode 编码混合的策略：UCS-2 和 UTF-16。对于可以采用 16 位编码
的字符（U+0000~U+FFFF），这两种编码实际上是一样的
* 用 charCodeAt()方法可以查看指定码元的字符编码。这个方法返回指定索引位置的码元值
```js
let message = "abcde";
// Unicode "Latin small letter C"的编码是 U+0063
console.log(message.charCodeAt(2)); // 99
//99即十六进制的63
```
* fromCharCode()方法接受任意多个数值，并返回将所有数值对应的字符拼接起来的字符串：
```js
// 0x0061 === 97
// 0x0062 === 98
// 0x0063 === 99
// 0x0064 === 100
// 0x0065 === 101
console.log(String.fromCharCode(97, 98, 99, 100, 101)); // "abcde" 
```
>16 位只能唯一表示65 536 个字符。这对于大多数语言字符集是足够了，在 Unicode 中称为基本多语言平面（BMP）。为了表示更多的字符，Unicode 采用了一个策略，即每个字符使用另外 16 位去选择一个增补平面。这种每个字符使用两个 16 位码元的策略称为代理对。
```js
// "smiling face with smiling eyes" 表情符号的编码是 U+1F60A
// 0x1F60A === 128522
let message = "ab☺de";
console.log(message.length); // 6
console.log(message.charAt(1)); // b 
console.log(message.charAt(2)); // <?>
console.log(message.charAt(3)); // <?>
//这些方法仍然将 16 位码元当作一个字符，
//事实上索引 2 和索引 3 对应的码元应该被看成一个代理
对，只对应一个字符 
console.log(String.fromCodePoint(0x1F60A)); // ☺
console.log(message.codePointAt(3)); // 56842
```
* codePointAt()接收 16 位码元的索引并返回该索引
位置上的码点（code point）。**码点是 Unicode 中一个字符的完整标识**。比如，"c"的码点是 0x0063，而
"☺"的码点是 0x1F60A。码点可能是 16 位，也可能是 32 位，而 codePointAt()方法可以从指定码元位置识别完整的码点。
```js
//迭代字符串可以智能地识别代理对的码点：
console.log([..."ab☺de"]); // ["a", "b", "☺", "d", "e"] 
```
2. normalize()方法
某些 Unicode 字符可以有多种编码方式。有的字符既可以通过一个 BMP 字符表示，也可以通过一个代理对表示。
```js
// U+00C5：上面带圆圈的大写拉丁字母 A
console.log(String.fromCharCode(0x00C5)); // Å
// U+212B：长度单位“埃”
console.log(String.fromCharCode(0x212B)); // Å
// U+004：大写拉丁字母 A
// U+030A：上面加个圆圈
console.log(String.fromCharCode(0x0041, 0x030A)); // Å
```
上面三个==结果为false,为了规范化引入normalize()方法，参数为"NFD"，"NFC"，"NFKD"，"NFKC"
3. 字符串操作方法
* concat()，将一个或多个字符串拼接成一个新字符串
```js
let stringValue = "hello ";
let result = stringValue.concat("world", "!");
//实际上+拼接更常用
console.log(result); // "hello world!"
console.log(stringValue); // "hello" 
```
* 切片
```js
let stringValue = "hello world";
console.log(stringValue.slice(3)); // "lo world"
console.log(stringValue.slice(3, 7)); // "lo w"
//第七位"o"不包含
console.log(stringValue.substring(3)); // "lo world"
console.log(stringValue.substring(3,7)); // "lo w"
console.log(stringValue.substr(3)); // "lo world"
console.log(stringValue.substr(3, 7)); // "lo worl" 
//substr()第二个参数表示返回的子字符串数量
```
* 切片 负参数
slice即倒着数(也可理解为长度加上负参数)
substring转化为0
substr第一个负参数同slice,第二个同substring
```js
let stringValue = "hello world";
console.log(stringValue.slice(-3)); // "rld"
console.log(stringValue.slice(3, -4)); // "lo w"
console.log(stringValue.substring(-3)); // "hello world"
console.log(stringValue.substring(3, -4)); // "hel"
console.log(stringValue.substr(-3)); // "rld"
console.log(stringValue.substr(3, -4)); // "" (empty string)
//第二个为返回的字符串数量为0，故空
```
4. 字符串位置方法
indexOf()方法从字符串开头开始查找子字符串，
而 lastIndexOf()方法从字符串末尾开始查找子字符串，
如果没找到，则返回-1
```js
let stringValue = "hello world";
console.log(stringValue.indexOf("o")); // 4
console.log(stringValue.lastIndexOf("o")); // 7 
```
都可以接收可选的第二个参数，表示开始搜索的位置.下面示例遍历找到所有目标字母的位置
```js
let stringValue = "Lorem ipsum dolor sit amet, consectetur adipisicing elit";
let positions = new Array();
let pos = stringValue.indexOf("e");
while(pos > -1) {
 positions.push(pos);
 pos = stringValue.indexOf("e", pos + 1);
}
console.log(positions); // [3,24,32,35,52]
```
5. 字符串包含方法
* startsWith()检查开始于索引 0 的匹配项，
* endsWith()检查开始于索引(string.length-substring.length)的匹配项
(PS:即"."前面的length-后面括号里的string)
* includes()检查整个字符串
```js
let message = "foobarbaz";
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("bar")); // false
console.log(message.endsWith("baz")); // true
console.log(message.endsWith("bar")); // false
console.log(message.includes("bar")); // true
console.log(message.includes("qux")); // false
```
startsWith()和 includes()方法接收可选的第二个参数，表示开始搜索的位置 
6. trim()方法
这个方法会创建字符串的一个副本，删除前、后所有空格符，再返回结果
```js
let stringValue = " hello world ";
let trimmedStringValue = stringValue.trim();
console.log(stringValue); // " hello world "
console.log(trimmedStringValue); // "hello world" 
```
trimLeft()和 trimRight()方法分别用于从字符串开始和末尾清理空格符。
7. repeat()方法
接收一个整数参数，表示要将字符串复制多少次，然后返回拼接所有副本后的结果。
```js
let stringValue = "na ";
console.log(stringValue.repeat(16) + "batman");
// na na na na na na na na na na na na na na na na batman
```
8. padStart()和 padEnd()方法
* 如果小于指定长度，则在相应一边填充字符，直至满足长度条件。这两个方法的第一个参数是长度，第二个参数是可选的填充字符串，默认为空格
```js
let stringValue = "foo";
console.log(stringValue.padStart(6)); // " foo"
console.log(stringValue.padStart(9, ".")); // "......foo"
console.log(stringValue.padEnd(6)); // "foo "
console.log(stringValue.padEnd(9, ".")); // "foo......"
```
可选的第二个参数并不限于一个字符。如果提供了多个字符的字符串，则会将其拼接并截断以匹配指定长度。
* 如果长度小于或等于字符串长度，则会返回原始字符串。
9. 字符串迭代与解构
迭代
```js
let message = "abc";
let stringIterator = message[Symbol.iterator]();
console.log(stringIterator.next()); // {value: "a", done: false}
console.log(stringIterator.next()); // {value: "b", done: false}
console.log(stringIterator.next()); // {value: "c", done: false}
console.log(stringIterator.next()); // {value: undefined, done: true}
```
解构
```js
let message = "abcde";
console.log([...message]); // ["a", "b", "c", "d", "e"]
```
10. 字符串大小写转换
```js
let stringValue = "hello world";
console.log(stringValue.toLocaleUpperCase()); // "HELLO WORLD"
console.log(stringValue.toUpperCase()); // "HELLO WORLD"
console.log(stringValue.toLocaleLowerCase()); // "hello world"
console.log(stringValue.toLowerCase()); // "hello world"
```
>在少数语言中（如土耳其语），Unicode 大小写转换需应用特殊规则，要使用地区特定的方法才能实现正确转换.如果不知道代码涉及什么语言，则最好使用地区特定的转换方法。
11. 字符串模式匹配方法
* match()
```js
let text = "cat, bat, sat, fat";
let pattern = /.at/;
// 等价于 pattern.exec(text)
let matches = text.match(pattern);
console.log(matches.index); // 0
console.log(matches[0]); // "cat"
console.log(pattern.lastIndex); // 0 
```
* search()
。这个方法返回模式第一个匹配的位置索引，如果没找到则返回-1。search()始终从字符串开头向后匹配模式。
```js
let text = "cat, bat, sat, fat";
let pos = text.search(/at/);
console.log(pos); // 1
``` 
* replace()
```js
let text = "cat, bat, sat, fat";
let result = text.replace("at", "ond");
console.log(result); // "cond, bat, sat, fat"
result = text.replace(/at/g, "ond");
console.log(result); // "cond, bond, sond, fond"
```
第二个参数是字符串的情况下，有几个特殊的字符序列，可以用来插入正则表达式操作的值
```js
$$ $
$& 匹配整个模式的子字符串。与 RegExp.lastMatch 相同
$' 匹配的子字符串之前的字符串。与 RegExp.rightContext 相同
$` 匹配的子字符串之后的字符串。与 RegExp.leftContext 相同
$n 匹配第 n 个捕获组的字符串，其中 n 是 0~9。比如，$1 是匹配第一个捕获组的字符串，$2 是匹配第二个
捕获组的字符串，以此类推。如果没有捕获组，则值为空字符串
$nn 匹配第 nn 个捕获组字符串，其中 nn 是 01~99。比如，$01 是匹配第一个捕获组的字符串，$02 是匹配第
二个捕获组的字符串，以此类推。如果没有捕获组，则值为空字符串
```
```js
let text = "cat, bat, sat, fat";
result = text.replace(/(.at)/g, "word ($1)");
console.log(result); // word (cat), word (bat), word (sat), word (fat)
```
第二个参数是函数（这个函数应该**返回一个字符串**，表示应该把匹配项替换成什么。使用函数作为第二个参数可以更细致地控制替换过程）
```js
function htmlEscape(text) {
 return text.replace(/[<>"&]/g, function(match, pos, originalText) {
 switch(match) {
 case "<":
 return "&lt;";
 case ">":
 return "&gt;";
 case "&":
 return "&amp;";
 case "\"":
 return "&quot;";
 }
 });
}
console.log(htmlEscape("<p class=\"greeting\">Hello world!</p>"));
// "&lt;p class=&quot;greeting&quot;&gt;Hello world!</p>"
```
* split()
把一个字符串分割成一个字符串数组，第一个参数是分割成字符串数组所指定的边界
```js
let colorText = "red,blue,green,yellow";
let colors1 = colorText.split(","); // ["red", "blue", "green", "yellow"]
let colors2 = colorText.split(",", 2); // ["red", "blue"]
let colors3 = colorText.split(/[^,]+/); // ["", ",", ",", ",", ""] 
//[^,]表示任何 非,(逗号)开头 的字符，+表示一个或者多个
```
(PS:color3得到一个包含逗号的数组;返回的数组前后包含两个空字符串,因为正则表达式指定的分隔符【非,(逗号)开头 的字符】出现在了字符串开头（即"red"）和末尾（"yellow"）)
(可以把除了“，”的所有字符都想象为一把🗡刀来切割成“，，，”，其中最前面和最后面被分割多了两个""空字符！)
12. localeCompare()方法
按照字母表顺序，比较字符串，字符串参数顺序(大写字母在前)
字符串顺序在后为+1
```js
let stringValue = "yellow";
console.log(stringValue.localeCompare("brick")); // 1
console.log(stringValue.localeCompare("yellow")); // 0
console.log(stringValue.localeCompare("zoo")); // -1
```
13. HTML 方法
这些方法基本上已经没有人使用了，因为结果通常不是语义化的标记
### IV.单例内置对象
ECMA-262 对内置对象的定义是“任何由 ECMAScript 实现提供、与宿主环境无关，并在 ECMAScript程序开始执行时就存在的对象”
大部分内置对象，包括 Object、Array 和 String。
另外两个单例内置对象：Global 和 Math。
#### ①Global
Global 对象是 ECMAScript 中最特别的对象，因为代码不会显式地访问它。ECMA-262 规定 Global对象为一种兜底对象，它所针对的是不属于任何对象的属性和方法。**事实上，不存在全局变量或全局函数这种东西。在全局作用域中定义的变量和函数都会变成 Global 对象的属性**
1. URL 编码方法
2. eval()方法
* eval(),一个完整的 ECMAScript 解释器，接收一个参数，即一个要执行的 ECMAScript（JavaScript）字符串
```js
let msg = "hello world";
eval("console.log(msg)"); // "hello world"
//第二行会被替换成一行真正的函数调用代码
//console.log(msg)
```
* eval()定义的任何变量和函数都不会被提升，因为在解析代码的时候，它们被包含在一个字符串中。它们**只是在 eval()执行的时候才会被创建**
```js
eval("let msg = 'hello world';");
console.log(msg); // Reference Error: msg is not defined
//上面let换成var有输出,因为var可以变量提升
--------
eval("function sayHi() { console.log('hi'); }");
sayHi();//这里调用的时候会执行上面的定义
```
>在严格模式下，在 eval()内部创建的变量和函数无法被外部访问(上面代码块会失效)。赋值给 eval 也会导致错误

>在使用 eval()的时候必须极为慎重，特别是在解释用户输入的内容时。因为这个方法会对 XSS 利用暴露出很大的攻击面。恶意用户可能插入会导致你网站或应用崩溃的代码
3. Global 对象属性
undefined、NaN 和 Infinity 等特殊值都是 Global 对象的属性。
此外，所有原生引用类型构造函数，比如 Object 和 Function，也都是Global 对象的属性。
4. window 对象
ECMA-262 没有规定直接访问 Global 对象的方式，**浏览器将 window 对象实现为 Global对象的代理**。所有全局作用域中声明的变量和函数都变成了 window 的属性
* 获取 Global 对象的方式是使用如下的代码：
```js
let global = function() {
 return this;
}(); 
```
>当一个函数在没有明确（通过成为某个对象的方法，或者通过 call()/apply()）指定 this 值的情况下执行时，this 值等于Global 对象
#### ②Math
>Math 对象上提供的计算要比直接在 JavaScript 实现的快得多，因为 Math 对象上的
计算使用了 JavaScript 引擎中更高效的实现和处理器指令
1. Math 对象属性
```js
Math.E        自然对数的基数 e 的值
Math.LN10     10为底的自然对数
Math.LN2      2为底的自然对数
Math.LOG2E    以2为底 e 的对数
Math.LOG10E   以10为底 e 的对数
Math.PI       π 的值
Math.SQRT1_2  1/2 的平方根
Math.SQRT2 2  的平方根
```
2. min()和 max()方法
```js
let max = Math.max(3, 54, 32, 16);
console.log(max); // 54
let min = Math.min(3, 54, 32, 16);
console.log(min); // 3 
//使用扩展操作符：
let values = [1, 2, 3, 4, 5, 6, 7, 8];
let max = Math.max(...val); 
```
3. 舍入方法
Math.ceil()方法始终向上舍入为最接近的整数。
Math.floor()方法始终向下舍入为最接近的整数。
Math.round()方法执行四舍五入。
Math.fround()方法返回数值最接近的单精度（32 位）浮点值表示
```js
console.log(Math.ceil(25.1)); // 26
console.log(Math.floor(25.9)); // 25
console.log(Math.round(25.5)); // 26
console.log(Math.round(25.1)); // 25
console.log(Math.fround(0.4)); // 0.4000000059604645
console.log(Math.fround(0.5)); // 0.5
console.log(Math.fround(25.9)); // 25.899999618530273
```
4. random()方法
Math.random()方法返回一个 0~1 范围内的随机数，其中包含 0 但不包含 1
```js
//基于如下公式使用 Math.random()从一组整数中随机选择一个数：
number = Math.floor(Math.random() * total_number_of_choices + first_possible_value)
//想从 1~10 范围内随机选择一个数：
let num = Math.floor(Math.random() * 10 + 1);
//想选择一个 2~10 范围内的值，
let num = Math.floor(Math.random() * 9 + 2);
//通过函数来算出可选总数和最小可能的值可能更方便，
function selectFrom(lowerValue, upperValue) {
 let choices = upperValue - lowerValue + 1;
 return Math.floor(Math.random() * choices + lowerValue);
}
let num = selectFrom(2,10);
console.log(num); // 2~10 范围内的值，其中包含 2 和 10
```
5. 其他方法
```js
Math.abs(x)          返回 x 的绝对值
Math.exp(x)          返回 Math.E 的 x 次幂
Math.expm1(x)        等于 Math.exp(x) - 1
Math.log(x)          返回 x 的自然对数
Math.log1p(x)        等于 1 + Math.log(x)
Math.pow(x, power)   返回 x 的 power 次幂
Math.hypot(...nums)  返回 nums 中每个数平方和的平方根
Math.clz32(x)        返回 32 位整数 x 的前置零的数量
Math.sign(x)         返回表示 x 符号的 1、0、-0 或-1
Math.trunc(x)        返回 x 的整数部分，删除所有小数
Math.sqrt(x)         返回 x 的平方根
Math.cbrt(x)         返回 x 的立方根
Math.acos(x)         返回 x 的反余弦
Math.acosh(x)        返回 x 的反双曲余弦
Math.asin(x)         返回 x 的反正弦
Math.asinh(x)        返回 x 的反双曲正弦
Math.atan(x)         返回 x 的反正切
Math.atanh(x)        返回 x 的反双曲正切
Math.atan2(y, x)     返回 y/x 的反正切
Math.cos(x)          返回 x 的余弦
Math.sin(x)          返回 x 的正弦
Math.tan(x)          返回 x 的正切
```
### V.小结索引
①JavaScript 中的对象称为引用值，几种内置的引用类型可用于创建特定类型的对象。
1. 引用值与传统面向对象编程语言中的类**相似**，但实现不同。
2. Date 类型提供关于日期和时间的信息，包括当前日期、时间及相关计算。
3. RegExp 类型是 ECMAScript 支持正则表达式的接口，提供了大多数基础的和部分高级的正则表达式功能。
②JavaScript 比较独特的一点是，函数实际上是 Function 类型的实例，也就是说**函数也是对象。所以函数也有方法**，可以用于增强其能力。
③由于原始值包装类型的存在，**JavaScript 中的原始值可以被当成对象来使用**。有 3 种原始值包装类型：Boolean、Number 和 String。它们都具备如下特点。
**1.每种包装类型都映射到同名的原始类型。
2.以读模式访问原始值时，后台会实例化一个原始值包装类型的对象，借助这个对象可以操作相应的数据。
3.涉及原始值的语句执行完毕后，包装对象就会被销毁。**
④当代码开始执行时，全局上下文中会存在两个内置对象：Global 和 Math。其中，Global 对象在大多数 ECMAScript 实现中无法直接访问。浏览器将Global实现为 window 对象。所有全局变量和函数都是 Global 对象的属性。Math 对象包含辅助完成复杂计算的属性和方法。
## No6.集合引用类型
### I.Object
大多数引用值的示例使用的是 Object 类型。Object 是ECMAScript 中最常用的类型之一，很适合存储和在应用程序间交换数据
* 显式地创建 Object 的实例有两种方式
1. new 操作符和 Object 构造函数
```js
let person = new Object();
//同let person = {};
person.name = "Nicholas";
person.age = 29;
```
2. 对象字面量（object literal）表示法
(属性名可以是字符串或数值)
```js
let person = {
 name: "Nicholas",
 age: 29.
 5: true .
};
```
>在使用对象字面量表示法定义对象时，并不会实际调用 Object 构造函数。
3. 实际开发中一个很好的例子
```js
function displayInfo(args) {
 let output = "";
 if (typeof args.name == "string"){
 output += "Name: " + args.name + "\n";
 }
 if (typeof args.age == "number") {
 output += "Age: " + args.age + "\n";
 }
 alert(output);
}
displayInfo({
 name: "Nicholas",
 age: 29
});
displayInfo({
 name: "Greg"
}); 
```
### II.Array
#### ①创建数组
1. 使用 Array 构造函数
```js
let colors = Array(3); // 创建一个包含 3 个元素的数组
let names = Array("Greg"); // 创建一个只包含一个元素，即字符串"Greg"的数组
```
2. 用数组字面量（array literal）表示法
```js
let colors = ["red", "blue", "green"]; 
// 创建一个包含 3 个元素的数组
let names = []; // 创建一个空数组
```
3. from()用于将类数组结构转换为数组实例，而 of()用于将一组参数转换为数组实例。
```js
// 1.字符串会被拆分为单字符数组
console.log(Array.from("Matt")); // ["M", "a", "t", "t"]
// 2.可以使用 from()将集合和映射转换为一个新数组
const m = new Map().set(1, 2)
 .set(3, 4);
const s = new Set().add(1)
 .add(2)
 .add(3)
 .add(4);
console.log(Array.from(m)); // [[1, 2], [3, 4]]
console.log(Array.from(s)); // [1, 2, 3, 4]
// 3.arguments 对象可以被轻松地转换为数组
function getArgsArray() {
 return Array.from(arguments);
}
console.log(getArgsArray(1, 2, 3, 4)); // [1, 2, 3, 4]
// 4.转换带有必要属性的自定义对象
const arrayLikeObject = {
 0: 1,
 1: 2,
 2: 3,
 3: 4,
 length: 4
};
console.log(Array.from(arrayLikeObject)); // [1, 2, 3, 4] 
// 5.Array.from()对现有数组执行浅复制
const a1 = [1, 2, 3, 4];
const a2 = Array.from(a1);
console.log(a1); // [1, 2, 3, 4]
alert(a1 === a2); // false
```
#### ②数组空位
```js
const options = [,,,,,]; // 创建包含 5 个元素的数组
console.log(options.length); // 5
console.log(options); // [,,,,,] 
```
空位值为 undefined
>实践中要避免使用数组空位。如果确实需要空位，则可以显式地用 undefined 值代替。
#### ③数组索引
数组 length 属性的独特之处在于，它不是只读的。通过修改 length 属性，可以从数组末尾删除或添加元素。新添加的元素都将以undefined 填充
```js
let colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
colors[colors.length] = "black"; // 添加一种颜色（位置 3）
colors[colors.length] = "brown"; // 再添加一种颜色（位置 4）
```
>数组最多可以包含 4 294 967 295 个元素
#### ④检测数组
```js
if (value instanceof Array){
 // 操作数组
} 
```
但是，使用 instanceof 的问题是假定只有一个全局执行上下文。**如果网页里有多个框架，则可能涉及两个不同的全局执行上下文**，因此就会有两个不同版本的 Array 构造函数。如果要把数组从一个框架传给另一个框架，则这个数组的构造函数将有别于在第二个框架内本地创建的数组.isArray()为解决办法
```js
if (Array.isArray(value)){
 // 操作数组
} 
```
#### ⑤迭代器方法
keys()返回数组索引的迭代器，values()返回数组元素的迭代器，而 entries()返回
索引/值对的迭代器：
```js
const a = ["foo", "bar", "baz", "qux"];
// 因为这些方法都返回迭代器，所以可以将它们的内容
// 通过 Array.from()直接转换为数组实例
const aKeys = Array.from(a.keys());
const aValues = Array.from(a.values());
const aEntries = Array.from(a.entries());
console.log(aKeys);
// [0, 1, 2, 3]
console.log(aValues); 
// ["foo", "bar", "baz", "qux"]
console.log(aEntries);
 // [[0, "foo"], [1, "bar"], [2, "baz"], [3, "qux"]]
```
使用 ES6 的解构可以非常容易地在循环中拆分键/值对：
```js
const a = ["foo", "bar", "baz", "qux"];
for (const [idx, element] of a.entries()) {
 alert(idx);
 alert(element);
}
// 0
// foo
// 1
// bar
// 2
// baz
// 3
// qux 
```
#### ⑥复制和填充方法
1. fill()
```js
const zeroes = [0, 0, 0, 0, 0];
// 用 5 填充整个数组
zeroes.fill(5);
console.log(zeroes); // [5, 5, 5, 5, 5]
zeroes.fill(0); // 重置
// 用 6 填充索引大于等于 3 的元素
zeroes.fill(6, 3);
console.log(zeroes); // [0, 0, 0, 6, 6]
zeroes.fill(0); // 重置
// 用 7 填充索引大于等于 1 且小于 3 的元素
zeroes.fill(7, 1, 3);
console.log(zeroes); // [0, 7, 7, 0, 0];
zeroes.fill(0); // 重置
// 用 8 填充索引大于等于 1 且小于 4 的元素
// (-4 + zeroes.length = 1)
// (-1 + zeroes.length = 4)
zeroes.fill(8, -4, -1);
console.log(zeroes); // [0, 8, 8, 8, 0];
```
2. copyWithin()
```js
let ints,
 reset = () => ints = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
reset();
// 从 ints 中复制索引 0 开始的内容，插入到索引 5 开始的位置

// 在源索引或目标索引到达数组边界时停止

ints.copyWithin(5);
console.log(ints); // [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]
reset();
// 从 ints 中复制索引 5 开始的内容，插入到索引 0 开始的位置
ints.copyWithin(0, 5);
console.log(ints); // [5, 6, 7, 8, 9, 5, 6, 7, 8, 9]
reset();
// 从 ints 中复制索引 0 开始到索引 3 结束(不包括3)的内容
// 插入到索引 4 开始的位置
ints.copyWithin(4, 0, 3);
alert(ints); // [0, 1, 2, 3, 0, 1, 2, 7, 8, 9]
reset();
// JavaScript 引擎在插值前会完整复制范围内的值
// 因此复制期间不存在重写的风险
ints.copyWithin(2, 0, 6);
alert(ints); // [0, 1, 0, 1, 2, 3, 4, 5, 8, 9]
reset();
// 支持负索引值，与 fill()相对于数组末尾计算正向索引的过程是一样的
ints.copyWithin(-4, -7, -3);
//ints.copyWithin(6,3,7)
alert(ints); // [0, 1, 2, 3, 4, 5, 3, 4, 5, 6]
```
#### ⑦转换方法
```js
let colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
alert(colors.toString()); // red,blue,green
alert(colors.valueOf()); // red,blue,green
alert(colors); // red,blue,green
```
继承的方法 toLocaleString()以及 toString()都返回数组值的逗号分隔的字符串。如果想使用不同的分隔符，则可以使用 join()方法。join()方法接收一个参数，即字符串分隔符.
```js
let colors = ["red", "green", "blue"];
alert(colors.join(",")); // red,green,blue
alert(colors.join("||")); // red||green||blue
```
#### ⑧栈方法
* push()方法接收任意数量的参数，并将它们添加到数组末尾，返回数组的最新长度。
* pop()方法则用于删除数组的最后一项，同时减少数组的 length 值，返回被删除的项
```js
let colors = new Array(); // 创建一个数组
let count = colors.push("red", "green"); // 推入两项
alert(count); // 2
count = colors.push("black"); // 再推入一项
alert(count); // 3
let item = colors.pop(); // 取得最后一项
alert(item); // black
alert(colors.length); // 2 
```
#### ⑨队列方法
队列以先进先出（FIFO，First-In-First-Out）形式限制访问。**队列在列表末尾添加数据，但从列表开头获取数据**。因为有了在数据末尾添加数据的 push()方法，所以要模拟队列就差一个从数组开头取得数据的方法了。
* shift()，它会删除数组的第一项并返回它，然后数组长度减 1。使用 shift()和 push()，可以把数组当成队列来使用：
```js
let colors = new Array(); // 创建一个数组
let count = colors.push("red", "green"); // 推入两项
count = colors.push("black"); // 再推入一项
alert(count); // 3
let item = colors.shift(); // 取得第一项
alert(item); // red
alert(colors.length); // 2
```
* unshift()方法,在数组开头添加任意多个值，然后返回新的数组长度
#### ⑩排序方法
reverse()和 sort()同python
* sort()升序排列，但是会执行String()方法
```js
let values = [0, 1, 5, 10, 15];
values.sort();
alert(values); // 0,1,10,15,5
//字符串"10"在字符串"5"的前头，所以 10 还是会排到 5 前面
```
优化：
```js
function compare(value1, value2) {
 if (value1 < value2) {
 return -1;
 } else if (value1 > value2) {
 return 1;
 } else {
 return 0;
 }
}

let values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // 0,1,5,10,15 
```
-1和1交换即降序排列,可简写为箭头函数
```js
let values = [0, 1, 5, 10, 15];
values.sort((a, b) => a < b ? 1 : a > b ? -1 : 0);
alert(values); // 15,10,5,1,0 
```
>reverse()和 sort()都返回调用它们的数组的引用。

#### 11.操作方法 ※splice()
* concat()方法(参考Symbol 7. Symbol.isConcatSpreadable ※)
```js
let colors = ["red", "green", "blue"];
let newColors = ["black", "brown"];
let moreNewColors = {
 [Symbol.isConcatSpreadable]: true,
 length: 2,
 0: "pink",
 1: "cyan"
};
newColors[Symbol.isConcatSpreadable] = false;
// 强制不打平数组
let colors2 = colors.concat("yellow", newColors);
// 强制打平类数组对象
let colors3 = colors.concat(moreNewColors);
console.log(colors); // ["red", "green", "blue"]
console.log(colors2); // ["red", "green", "blue", "yellow", ["black", "brown"]]
console.log(colors3); // ["red", "green", "blue", "pink", "cyan"] 
```
* slice()
```js
let colors = ["red", "green", "blue", "yellow", "purple"];
let colors2 = colors.slice(1);
let colors3 = colors.slice(1, 4);
alert(colors2); // green,blue,yellow,purple
alert(colors3); // green,blue,yellow
```
* splice()
1. 删除。需要给 splice()传 2 个参数：要删除的第一个元素的位置和要删除的元素数量。
2. 插入。splice()在删除(第二个参数不为0)元素的同时可以在指定位置插入新元素，同样要传入 3 个参数：**开始位置、要删除元素的数量 、 要插入的任意多个元素**。要插入的元素数量不一定跟删除的元素数量一致。
```js
let colors = ["red", "green", "blue"];
let removed = colors.splice(0,1); // 删除第一项
alert(colors); // green,blue
alert(removed); // red，只有一个元素的数组

removed = colors.splice(1, 0, "yellow", "orange"); // 在位置 1 插入两个元素
alert(colors); // green,yellow,orange,blue
alert(removed); // 空数组

removed = colors.splice(1, 1, "red", "purple"); // 插入两个值，删除一个元素
alert(colors); // green,red,purple,orange,blue
alert(removed); // yellow，只有一个元素的数组
```
#### 12.搜索和位置方法
1. 严格相等
indexOf()和 lastIndexOf()都返回要查找的元素在数组中的位置，如果没找到则返回-1。includes()返回布尔值，表示是否至少找到一个与指定元素匹配的项。在比较第一个参数跟数组每一项时，会使用**全等（===）**比较，也就是说两项必须严格相等
```js
let person = { name: "Nicholas" };
let people = [{ name: "Nicholas" }];
let morePeople = [person];
alert(people.indexOf(person)); // -1
alert(morePeople.indexOf(person)); // 0
alert(people.includes(person)); // false
alert(morePeople.includes(person)); // true
```
2. 断言函数
find()和 findIndex()方法使用了断言函数。这两个方法都从数组的最小索引开始。
* find()返回第一个匹配的元素
* findIndex()返回第一个匹配元素的索引。
这两个方法也都接收第二个可选的参数，用于指定断言函数内部 this 的值
```js
const people = [
 {
 name: "Matt",
 age: 27
 },
 {
 name: "Nicholas",
 age: 29
 }
];
alert(people.find((element, index, array) => element.age < 28));
// {name: "Matt", age: 27}
alert(people.findIndex((element, index, array) => element.age < 28));
// 0 
```
#### 13.迭代方法 ※
* every()：对数组每一项都运行传入的函数，如果对每一项函数都返回 true，则这个方法返回 true。
* some()：对数组每一项都运行传入的函数，如果有一项函数返回 true，则这个方法返回 true。
```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let everyResult = numbers.every((item, index, array) => item > 2);
alert(everyResult); // false
let someResult = numbers.some((item, index, array) => item > 2);
alert(someResult); // true 
```

* filter()：对数组每一项都运行传入的函数，函数返回 true 的项会组成数组之后返回。
```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let filterResult = numbers.filter((item, index, array) => item > 2);
alert(filterResult); // 3,4,5,4,3
```
* forEach()：对数组每一项都运行传入的函数，没有返回值。
(相当于使用 for 循环遍历数组)
* map()：对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组。
```js
let numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
let mapResult = numbers.map((item, index, array) => item * 2);
alert(mapResult); // 2,4,6,8,10,8,6,4,2 
```
#### 14.归并方法
* reduce()方法从数组第一项开始遍历到最后一项。
* reduceRight()从最后一项开始遍历至第一项
传给 reduce()和 reduceRight()的函数接收 4 个参数：**上一个归并值、当前项、当前项的索引和数组本身**。
(PS:每次他们都返回上一次的结果；若是从头开始，prev为数组第一项，cur为第二项)
```js
//使用 reduce()函数执行累加数组中所有数值的操作：
let values = [1, 2, 3, 4, 5];
let sum = values.reduce((prev, cur, index, array) => prev + cur);
alert(sum); // 15
```
### III.定型数组
定型数组（typed array）目的是提升向原生库传输数据的效率。
实际上，JavaScript 并没有“TypedArray”类型，它所指的其实是一种特殊的包含数值类型的数组。
#### ①ArrayBuffer
Float32Array 实际上是一种“视图”，可以允许 JavaScript 运行时访问一块名为 ArrayBuffer 的预分配内存。
* ArrayBuffer 是所有定型数组及视图引用的基本单位。
* ArrayBuffer()是一个普通的 JavaScript 构造函数，可用于在内存中分配特定数量的字节空间。
```js
const buf = new ArrayBuffer(16); // 在内存中分配 16 字节
alert(buf.byteLength); // 16
```
ArrayBuffer 一经创建就不能再调整大小。不过，可以使用 slice()复制其全部或部分到一个新实例中：
```js
const buf1 = new ArrayBuffer(16);
const buf2 = buf1.slice(4, 12);
alert(buf2.byteLength); // 8
```
>ArrayBuffer 某种程度上类似于 C++的 malloc()，但也有几个明显的区别。
1. malloc()在分配失败时会返回一个 null 指针。ArrayBuffer 在分配失败时会抛出错误。
2. malloc()可以利用虚拟内存，因此最大可分配尺寸只受可寻址系统内存限制。ArrayBuffer分配的内存不能超过 Number.MAX_SAFE_INTEGER（2^53^-1）字节。
3. malloc()调用成功不会初始化实际的地址。声明 ArrayBuffer 则会将所有二进制位初始化为 0。
4. 通过 malloc()分配的堆内存除非调用 free()或程序退出，否则系统不能再使用。而通过声明ArrayBuffer 分配的堆内存可以被当成垃圾回收，不用手动释放。 
#### ②DataView（M @)
要读取或写入 ArrayBuffer，就必须通过视图
#### ③定型数组184
2. 合并、复制和修改定型数组
set()从提供的数组或定型数组中把值复制到当前定型数组中指定的索引位置：
```js
// 创建长度为 8 的 int16 数组
const container = new Int16Array(8);
// 把定型数组复制为前 4 个值
// 偏移量默认为索引 0
container.set(Int8Array.of(1, 2, 3, 4));
console.log(container); // [1,2,3,4,0,0,0,0]
// 把普通数组复制为后 4 个值
// 偏移量 4 表示从索引 4 开始插入
container.set([5,6,7,8], 4);
console.log(container); // [1,2,3,4,5,6,7,8]
// 溢出会抛出错误
container.set([5,6,7,8], 7);
// RangeError
```
subarray()会基于从原始定型数组中复制的值返回一个新定型数组。
```js
const source = Int16Array.of(2, 4, 6, 8);
// 把整个数组复制为一个同类型的新数组
const fullCopy = source.subarray();
console.log(fullCopy); // [2, 4, 6, 8]
// 从索引 2 开始复制数组
const halfCopy = source.subarray(2);
console.log(halfCopy); // [6, 8]
// 从索引 1 开始复制到索引 3
const partialCopy = source.subarray(1, 3);
console.log(partialCopy); // [4, 6] 
```
### IV.Map
（Object 2.0)
#### ①.基本API
初始化
```js
// 使用嵌套数组初始化映射
const m1 = new Map([
 ["key1", "val1"],
 ["key2", "val2"],
 ["key3", "val3"]
]);
alert(m1.size); // 3
```
初始化之后，可以使用 set()方法再添加键/值对。
* 可以使用 get()和 has()进行查询
* 可以通过 size 属性获取映射中的键/值对的数量
* 可以使用 delete()和 clear()删除值。
```js
const m = new Map();
alert(m.has("firstName")); // false
alert(m.get("firstName")); // undefined
alert(m.size); // 0

m.set("firstName", "Matt")
 .set("lastName", "Frisbie");
alert(m.has("firstName")); // true
alert(m.get("firstName")); // Matt
alert(m.size); // 2

m.delete("firstName"); // 只删除这一个键/值对
alert(m.has("firstName")); // false
alert(m.has("lastName")); // true
alert(m.size); // 1

m.clear(); // 清除这个映射实例中的所有键/值对
alert(m.size); // 0  
```
set()方法返回映射实例，因此可以把多个操作连缀起来，包括初始化声明：
```js
const m = new Map().set("key1", "val1");
m.set("key2", "val2")
 .set("key3", "val3");
alert(m.size); // 3 
```
与 Object 只能使用数值、字符串或符号作为键不同<strong> Map 可以使用任何 JavaScript 数据类型作为键。与 Object 类似，映射的值是没有限制的。例如下面使用了函数作为键。</strong>
```js
const m = new Map();
const functionKey = function() {};
m.set(functionKey, "functionValue");
alert(m.get(functionKey)); // functionValue
```
(PS:若是使用+0作为键，用-0也能获取+0对应的值；
NaN也是如此)
#### ②.顺序与迭代
不同于Object,Map 实例会维护键值对的插入顺序，故可根据插入顺序执
行迭代操作
```js
const m = new Map([
 ["key1", "val1"],
 ["key2", "val2"],
 ["key3", "val3"]
]);

alert(m.entries === m[Symbol.iterator]); // true

for (let pair of m.entries()) {
 alert(pair);
}
// [key1,val1]
// [key2,val2]
// [key3,val3] 
console.log([...m]); 
// [[key1,val1],[key2,val2],[key3,val3]]
m.forEach((val, key) => alert(`${key} -> ${val}`));
// key1 -> val1
// key2 -> val2
// key3 -> val3 

//keys()和 values()分别返回以插入顺序生成键和值的迭代器
for (let key of m.keys()) {
 alert(key);
}
// key1
// key2
// key3 
```
作为键的字符串原始值是不能修改的,
即使修改了作为键的对象的属性，对象在映射内部仍然引用相同的值
```js
const keyObj = {id: 1};
const m = new Map([
 [keyObj, "val1"]
]);
for (let key of m.keys()) {
 key.id = 2;
 alert(key); // {id: 2}
 alert(m.get(keyObj)); // val1
}
alert(keyObj); // {id: 2}
```
#### ③.选择 Object 还是 Map
1. 内存占用
相同内存下.Map 大约可以比 Object 多存储 50%的键/值对。
2. 插入性能
插入 Map 在所有浏览器中一般会稍微快一点儿
3. 查找速度
但如果只包含少量键/值对，Object 有时候速度更快
4. 删除性能
使用 delete 删除 Object 属性的性能饱受诟病;
Map 的 delete()操作都比插入和查找更快;
### V.WeakMap(弱映射)
WeakMap 中的“weak”（弱），描述的是 JavaScript 垃圾回收程序对待“弱映射”中键的方式
#### ①基本API
```js
const wm = new WeakMap();
```
**弱映射中的键只能是 Object 或者继承自 Object 的类型** ，
值的类型没有限制。
```js
// 原始值可以先包装成对象再用作键
const stringKey = new String("key1");
const wm3 = new WeakMap([
 stringKey, "val1"
]);
alert(wm3.get(stringKey)); // "val1"
```
初始化之后可以使用 set()再添加键/值对，可以使用 get()和 has()查询，还可以使用 delete()删除  （同Map)
#### ②弱键
只要键存在，键/值对就会存在于映射中，并被当作对值的引用，就不会被当作垃圾回收。
```js
const wm = new WeakMap();
wm.set({}, "val");
因为没有指向这个对象的其他引用，
所以当这行代码执行完成后，这个对象键就会被当作垃圾回收。
然后这个键/值对就从弱映射中消失了，
使其成为一个空映射。
在这个例子中，因为值也没有被引用，
所以这对键/值被破坏以后，值本身也会成为垃圾回收的目标。
```
```js
const wm = new WeakMap();
const container = {
 key: {}
};
wm.set(container.key, "val");
function removeReference() {
 container.key = null;
}
container 对象维护着一个对弱映射键的引用，
因此这个对象键不会成为垃圾回收的目标。
如果调用了 removeReference()，
就会摧毁键对象的最后一个引用，
垃圾回收程序就可以把这个键/值对清理掉
```
#### ③不可迭代键  ※
* WeakMap 中的键/值对任何时候都可能被销毁，无迭代其键/值对的能力。
* 不可能在不知道对象引用的情况下从弱映射中取得值。即便代码可以访问 WeakMap 实例，也没办法看到其中的内容
* WeakMap 实例限制只能用对象作为键，是为了保证只有通过键对象的引用才能取得值。
如果允许原始值，那就没办法区分初始化时使用的字符串字面量和初始化之后使用的一个相等的字符串了。
（PS：为了使初始化时用的一个量和初始化之后一个对应原始值冲突）
#### ④使用弱映射
1. 私有变量
**私有变量会存储在弱映射中，以对象实例为键，以私有成员的字典为值**
用一个闭包把 WeakMap 包装起来，弱映射与外界完全隔离开了：
```js
const User = (() => {
 const wm = new WeakMap();
 class User {
 constructor(id) {
 this.idProperty = Symbol('id');
 this.setId(id);
 }
 setPrivate(property, value) {
 const privateMembers = wm.get(this) || {};
 privateMembers[property] = value;
 wm.set(this, privateMembers);
 }
 getPrivate(property) {
 return wm.get(this)[property];
 }
 setId(id) {
 this.setPrivate(this.idProperty, id);
 }
 getId(id) {
 return this.getPrivate(this.idProperty);
 }
 }
 return User;
})();
const user = new User(123);
alert(user.getId()); // 123
user.setId(456);
alert(user.getId()); // 456
//若不加闭包，下面代码就会成立，不算是私有
alert(wm.get(user)[user.idProperty]); // 456
```
2. DOM 节点元数据
WeakMap 实例不会妨碍垃圾回收，所以非常适合保存关联元数据
```js
//使用Map,原来的登录按钮从 DOM 树中被删掉了。
//但由于映射中还保存着按钮的引用，所以对应的 DOM 节点仍然会逗留在内存中，
//除非明确将其从映射中删除或者等到映射本身被销毁。
const m = new Map();
const loginButton = document.querySelector('#login');
// 给这个节点关联一些元数据
m.set(loginButton, {disabled: true});

//使用的是弱映射，那么当节点从 DOM 树中被删除后，
//垃圾回收程序就可以立即释放其内存（假设没有其他地方引用这个对象）
const wm = new WeakMap();
```
### VI.Set（集合数据结构）（加强的 Map）
Set 在很多方面都像是加强的 Map，这是因为它们的大多数 API 和行为都是共有的
#### ①基本API
```js
// 使用数组初始化集合
const s1 = new Set(["val1", "val2", "val3"]);
alert(s1.size); // 3 
```
* 可以使用 add()增加值，使用 has()查询，通过 size 取得元素数量，以及使用 delete()和 clear()删除元素.**delete()返回一个布尔值，表示集合中是否存在要删除的值**
* 与 Map 类似，Set 可以包含任何 JavaScript 数据类型作为值。集合也使用 SameValueZero 操作（ECMAScript 内部定义，无法在语言中使用），基本上相当于使用严格对象相等的标准来检查值的匹配性。
#### ②顺序与迭代
```js
集合的 entries()方法返回一个迭代器，
可以按照插入顺序产生包含两个元素的数组，
这两个元素是集合中每个值的重复出现：
const s = new Set(["val1", "val2", "val3"]);
for (let pair of s.entries()) {
 console.log(pair);
}
// ["val1", "val1"]
// ["val2", "val2"]
// ["val3", "val3"]
for (let value of s.values()) {
//同for (let value of s[Symbol.iterator]()) {
 alert(value);
}
// val1
// val2
// val3
```
#### ③定义正式集合操作※
很多开发者都喜欢使用 Set 操作，但需要手动实现：或者是子类化 Set，或者是定义一个实用函数库。要把两种方式合二为一，可以在子类上实现静态方法，然后在实例方法中使用这些静态方法
1. 某些 Set 操作是有关联性的，因此最好让实现的方法能支持处理任意多个集合实例。
2. Set 保留插入顺序，所有方法返回的集合必须保证顺序。
3. 尽可能高效地使用内存。扩展操作符的语法很简洁，但尽可能避免集合和数组间的相互转换能够节省对象初始化成本。
4. 不要修改已有的集合实例。union(a, b)或 a.union(b)应该返回包含结果的新集合实例


### VII.WeakSet
#### ①基本API
描述JavaScript 垃圾回收程序对待“弱集合”中值的方式(与Weakmap中弱键对应)
**弱集合中的值只能是 Object 或者继承自 Object 的类型**
#### ②弱值
```js
const ws = new WeakSet();
ws.add({});
```
add()方法初始化了一个新对象，并将它用作一个值。因为没有指向这个对象的其他引用，所以当这行代码执行完成后，这个对象值就会被当作垃圾回收。然后，这个值就从弱集合中消失了，使其成为一个空集合。
```js
const ws = new WeakSet();
const container = {
 val: {}
};
ws.add(container.val);
function removeReference() {
 container.val = null;
}
```
这一次，container 对象维护着一个对弱集合值的引用，因此这个对象值不会成为垃圾回收的目标。
#### ③不可迭代值
#### ④使用弱集合
```js
const disabledElements = new Set();
//const disabledElements = new WeakSet();
const loginButton = document.querySelector('#login');
// 通过加入对应集合，给这个节点打上“禁用”标签
disabledElements.add(loginButton);
```
通过查询元素在不在 disabledElements 中，就可以知道它是不是被禁用了。假如元素从 DOM 树中被删除了，它的引用却仍然保存在 Set 中，因此垃圾回收程序也不能回收它。
* 为了让垃圾回收程序回收元素的内存，可以使用 WeakSet：
只要 WeakSet 中任何元素从 DOM 树中被删除，垃圾回收程序就可以忽略其存在，而立即释放其内存（假设没有其他地方引用这个对象）。
### VIII.迭代与扩展操作
4 种原生集合类型定义了默认迭代器：
 Array
 所有定型数组
 Map
 Set
扩展操作符在对可迭代对象执行浅复制时特别有用，
只需简单的语法就可以复制整个对象：
```JS
let arr1 = [1, 2, 3];
let arr2 = [...arr1];
console.log(arr1); // [1, 2, 3]
console.log(arr2); // [1, 2, 3]
console.log(arr1 === arr2); // false
```
也可以构建数组的部分元素：
```JS
let arr1 = [1, 2, 3];
let arr2 = [0, ...arr1, 4, 5];
console.log(arr2); // [0, 1, 2, 3, 4, 5]
```
浅复制意味着只会复制对象引用：
```JS
let arr1 = [{}];
let arr2 = [...arr1];
arr1[0].foo = 'bar';
console.log(arr2[0]); // { foo: 'bar' }
```
### IX.小结
ECMAScript 6 新增了一批引用类型：Map、WeakMap、Set 和 WeakSet。这些类型为组织应用程序数据和简化内存管理提供了新能力。

## No.7迭代器与生成器
### I.理解迭代
循环是迭代机制的基础
迭代会在一个有序集合上进行。（“有序”可以理解为集合中所有项都可以按照既定的顺序被遍历到，特别是开始和结束项有明确的定义。）
1. 迭代之前需要事先知道如何使用数据结构。数组中的每一项都只能先通过引用取得数组对象，然后再通过[]操作符取得特定索引位置上的项。这种情况并不适用于所有数据结构。
2. 遍历顺序并不是数据结构固有的。通过递增索引来访问数据是特定于数组类型的方式，并不适用于其他具有隐式顺序的数据结构。
ES5 新增了 Array.prototype.forEach()方法，向通用迭代需求迈进了一步（但仍然不够理想）：
```JS
let collection = ['foo', 'bar', 'baz'];
collection.forEach((item) => console.log(item));
// foo
// bar
// baz 
```
>这个方法解决了单独记录索引和通过数组对象取得值的问题。不过，没有办法标识迭代何时终止。因此这个方法只适用于数组，而且回调结构也比较笨拙。
### II.迭代器模式
* 迭代器模式描述了一个方案，把有些结构称为“可迭代对象”（iterable），因为它们实现了正式的 Iterable 接口，而且可以通过迭代器 Iterator 消费。
* 可以把可迭代对象理解成数组或集合这样的集合类型的对象。它们包含的元素都是有限的，而且都具有无歧义的遍历顺序
* 可迭代对象不一定是集合对象，也可以是仅仅具有类似数组行为的其他数据结构，比如for计数循环。该循环中生成的值是暂时性的，但循环本身是在执行迭代。
#### ①可迭代协议
实现 Iterable 接口（可迭代协议）要求同时具备两种能力：
支持迭代的自我识别能力和创建实现Iterator 接口的对象
很多内置类型都实现了 Iterable 接口：
 字符串
 数组
 映射
 集合
 arguments 对象
 NodeList 等 DOM 集合类型
**实现可迭代协议的所有类型都会自动兼容接收可迭代对象的任何语言特性。**
接收可迭代对象的原生语言特性包括：
1. for-of 循环
2. 数组解构
```js
let arr = ['foo', 'bar', 'baz'];
let [a, b, c] = arr;
console.log(a, b, c); // foo, bar, baz
```
3. 扩展操作符
```js
let arr2 = [...arr];
console.log(arr2); // ['foo', 'bar', 'baz'] 
```
Array.from()
```js
let arr3 = Array.from(arr);
console.log(arr3); // ['foo', 'bar', 'baz']
```
创建集合
```js
let set = new Set(arr);
console.log(set); // Set(3) {'foo', 'bar', 'baz'}
```
创建映射
```js
let pairs = arr.map((x, i) => [x, i]);
console.log(pairs); // [['foo', 0], ['bar', 1], ['baz', 2]]
let map = new Map(pairs);
console.log(map); // Map(3) { 'foo'=>0, 'bar'=>1, 'baz'=>2 } 
```
 Promise.all()接收由期约组成的可迭代对象
 Promise.race()接收由期约组成的可迭代对象
 yield*操作符，在生成器中使用
#### ②迭代器协议
* 每次成功调用 next()，都会返回一个 IteratorResult 对象，其中包含迭代器返回的下一个值。若不调用 next()，则无法知道迭代器的当前位置
* next()方法返回的迭代器对象 IteratorResult 包含两个属性：done 和 value。
* done 是一个布尔值，表示是否还可以再次调用 next()取得下一个值；
* value 包含可迭代对象的下一个值（done 为false），或者 undefined（done 为 true）。
* done: true 状态称为“耗尽”
```js
let arr = ['foo', 'bar'];
// 执行迭代
console.log(iter.next()); // { done: false, value: 'foo' }
console.log(iter.next()); // { done: false, value: 'bar' }
console.log(iter.next()); // { done: true, value: undefined }
```
(ps:如果可迭代对象在迭代期间被修改了，那么迭代器也会反映相应的变化。
迭代器维护着一个指向可迭代对象的引用，因此迭代器会阻止垃圾回收程序回收可迭代对象
>一个显式的迭代器实现：
```js
class Foo { 
[Symbol.iterator]() {
 return {
 next() {
 return { done: false, value: 'foo' };
 }
 }
 }
}
let f = new Foo();
// 打印出实现了迭代器接口的对象
console.log(f[Symbol.iterator]()); // { next: f() {} }
```
#### ③自定义迭代器
与 Iterable 接口类似，任何实现 Iterator 接口的对象都可以作为迭代器使用。
为了让一个可迭代对象能够创建多个迭代器，必须每创建一个迭代器就对应一个新计数器。可以把计数器变量放到闭包里，然后通过闭包返回迭代器：
```js
class Counter {
 constructor(limit) {
 this.limit = limit;
 }
 [Symbol.iterator]() {
 let count = 1,
 limit = this.limit;
 return {
 next() {
 if (count <= limit) {
 return { done: false, value: count++ };
 } else {
 return { done: true, value: undefined };
 }
 }
 };
 }
}
let counter = new Counter(3);
for (let i of counter) { console.log(i); }
// 1
// 2
// 3
for (let i of counter) { console.log(i); }
// 1
// 2
// 3 
```
#### ④提前终止迭代器
可选的 return()方法用于指定在迭代器提前关闭时执行的逻辑.可能在下面的情况下使用：
1. for-of 循环通过 break、continue、return 或 throw 提前退出；
2. 解构操作并未消费所有值
如果迭代器没有关闭，则还可以继续从上次离开的地方继续迭代。比如，数组的迭代器就是不能关闭的：
```js
let a = [1, 2, 3, 4, 5];
let iter = a[Symbol.iterator]();
for (let i of iter) {
 console.log(i);
 if (i > 2) {
 break
 }
}
// 1
// 2
// 3
for (let i of iter) {
 console.log(i);
}
// 4
// 5 
```
* return()方法是可选的，所以并非所有迭代器都是可关闭的。要知道某个迭代器是否可关闭，可以测试这个迭代器实例的 return 属性是不是函数对象
>仅仅给一个不可关闭的迭代器增加这个方法并不能让它变成可关闭的。这是因为调用 return()不会强制迭代器进入关闭状态。即便如此，return()方法还是会被调用
```js
let a = [1, 2, 3, 4, 5];
let iter = a[Symbol.iterator]();
iter.return = function() {
 console.log('Exiting early');
 return { done: true };
};
for (let i of iter) {
 console.log(i);
 if (i > 2) {
 break
 }
}
// 1
// 2
// 3
// 提前退出
for (let i of iter) {
 console.log(i);
}
// 4
// 5
```
### III.生成器
#### ①生成器基础
**生成器的形式是一个函数，函数名称前面加一个星号（*）(\*位置旁有无空格不影响）表示它是一个生成器。只要是可以定义函数的地方，就可以定义生成器**
```js
// 生成器函数声明
function* generatorFn() {}
// 生成器函数表达式
let generatorFn = function* () {}
// 作为对象字面量方法的生成器函数
let foo = {
 * generatorFn() {}
}
// 作为类实例方法的生成器函数
class Foo {
 * generatorFn() {}
}
// 作为类静态方法的生成器函数
class Bar {
 static * generatorFn() {}
} 
```
* 调用生成器函数会产生一个生成器对象。
* 生成器对象一开始处于暂停执行（suspended）的状态。
* 与迭代器相似，生成器对象也实现了 Iterator 接口，因此具有 next()方法。调用这个方法会让生成器开始或恢复执行。
```js
function* generatorFn() {
  console.log('start')
  return 'foo'; 
}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
//生成器函数只会在初次调用 next()方法后开始执行
console.log(g.next); // f next() { [native code] }
console.log(generatorObject.next()); //start
// { done: true, value: foo } 
```
#### ②通过 yield 中断执行
**yield 关键字可以让生成器停止和开始执行**，也是生成器最有用的地方。生成器函数在遇到 yield关键字之前会正常执行。遇到这个关键字后，执行会停止，函数作用域的状态会被保留。停止执行的生成器函数只能通过在生成器对象上调用 next()方法来恢复执行：
```js
function* generatorFn() {
 yield 'foo';
 yield 'bar';
 return 'baz';
}
let generatorObject = generatorFn();
console.log(generatorObject.next()); 
// { done: false, value: 'foo' }
console.log(generatorObject.next()); 
// { done: false, value: 'bar' }
console.log(generatorObject.next());
 // { done: true, value: 'baz' }
```
* **yield 关键字只能在生成器函数内部使用，用在其他地方会抛出错误**。
* 类似函数的 return 关键字，yield 关键字**必须直接位于生成器函数定义中，出现在嵌套的非生成器函数中会抛出语法错误：**
```js
// 有效
function* validGeneratorFn() {
 yield;
}
// 无效
function* invalidGeneratorFnA() {
 function a() {
 yield;
 }
} 
```
1. 生成器对象作为可迭代对象
在生成器对象上显式调用 next()方法的用处并不大。**把生成器对象当成可迭代对象**，使用起来会更方便：
```js
function* generatorFn() {
 yield 1;
 yield 2;
 yield 3;
}
for (const x of generatorFn()) {
 console.log(x);
}
// 1
// 2
// 3
```

```js
function* nTimes(n) {
 while(n--) {
 yield;
 }
} 
for (let _ of nTimes(3)) {
 console.log('foo');
}
// foo
// foo
// foo
```
2. 使用 yield 实现输入和输出
除了可以作为函数的中间返回语句使用，yield 关键字还可以作为函数的中间参数使用。上一次让生成器函数暂停的 yield 关键字会接收到传给 next()方法的第一个值。这里有个地方不太好理解——
**第一次调用 next()传入的值不会被使用，因为这一次调用是为了开始执行生成器函数：**
```js
function* generatorFn(initial) {
 console.log(initial);
 console.log(yield);
 console.log(yield);
}
let generatorObject = generatorFn('foo');

generatorObject.next('bar'); // foo//开始执行
generatorObject.next('baz'); // baz
generatorObject.next('qux'); // qux


//yield 关键字可以同时用于输入和输出，如下例所示：
function* generatorFn() {
 return yield 'foo';
}
let generatorObject = generatorFn();
console.log(generatorObject.next()); // { done: false, value: 'foo' }
console.log(generatorObject.next('bar')); // { done: true, value: 'bar' } 
```
3. 产生可迭代对象(yield *)
```js
// 等价的 generatorFn：
// function* generatorFn() {
// for (const x of [1, 2, 3]) {
// yield x;
// }
// }
function* generatorFn() {
 yield* [1, 2, 3];
}
let generatorObject = generatorFn();
for (const x of generatorFn()) {
 console.log(x);
}
// 1
// 2
// 3 
```
4. 使用 yield*实现递归算法
#### ③ 生成器作为默认迭代器（上面在算法中应用※）
生成器对象实现了 Iterable 接口，而且生成器函数和默认迭代器被调用之后都产生迭代器，生成器格外适合作为默认迭代器
```js
class Foo {
 constructor() {
 this.values = [1, 2, 3];
 } 
* [Symbol.iterator]() {
 yield* this.values;
 }
}
const f = new Foo();
for (const x of f) {
 console.log(x);
}
// 1
// 2
// 3
```
#### ④提前终止生成器
 一个实现 Iterator 接口的对象一定有 next()方法，还有一个可选的 return()方法用于提前终止迭代器。生成器对象除了有这两个方法，还有第三个方法：throw()
 1. return()
 * 与迭代器不同，所有生成器对象都有 return()方法，只要通过它进入关闭状态，就无法恢复了。
* **后续调用 next()会显示 done: true 状态**，而提供的任何返回值都不会被存储或传播。
2. throw()
* throw()方法会在暂停的时候将一个提供的错误注入到生成器对象中。**如果错误未被处理，生成器就会关闭**
```js
function* generatorFn() {
 for (const x of [1, 2, 3]) {
 yield x;
 }
}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
try {
 g.throw('foo');
} catch (e) {
 console.log(e); // foo
}
console.log(g); // generatorFn {<closed>}
```
* 假如生成器函数内部处理了这个错误，那么生成器就不会关闭，而且还可以恢复执行。错误处理会跳过对应的 yield，因此在这个例子中会跳过一个值。比如：
```js
function* generatorFn() {
 for (const x of [1, 2, 3]) {
 try {
 yield x;
 } catch(e) {}
 }
} 
const g = generatorFn();
console.log(g.next()); // { done: false, value: 1}
g.throw('foo');
console.log(g.next()); // { done: false, value: 3}
```

* 在这个例子中，生成器在 try/catch 块中的 yield 关键字处暂停执行。
* 在暂停期间，throw()方法向生成器对象内部注入了一个错误：字符串"foo"。这个错误会被 yield 关键字抛出。
* 因为错误是在生成器的 try/catch 块中抛出的，所以仍然在生成器内部被捕获。

## No.8对象、类与面向对象编程






