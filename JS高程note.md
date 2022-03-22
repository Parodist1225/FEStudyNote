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
 Java
 PHP
 Python
 Ruby
 Golang
 HTML
 CSS 
****
2022.3.21
## No1. 什么是 JavaScript
### I.历史(略)
### II.JavaScript 实现
#### ①完整的 JavaScript 实现
 1. 核心（ECMAScript）
 2. 文档对象模型（DOM）
 3. 浏览器对象模型（BOM）

**Web 浏览器只是 ECMAScript 实现可能存在的一种宿主环境（host environment）**
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
```
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
```
function test() {
 var message = "hi"; // 局部变量
 //message = "hi";//全局变量,下面就不会报错了
 (不推荐|严格模式下ReferenceError)
}
test();
console.log(message); // 出错！
```
定义多个变量
`var a = "hi",
 b = false,
 c = 29;` 
2. var 声明提升
变量会自动提升到函数作用域顶部!
```
function foo() {
 console.log(a);
 var a = 26;
}
foo(); // undefined
```
其实解析时是:
```
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
`var name;
let name; // SyntaxError
let age;
var age; // SyntaxError
`
2.TDZ
在 let 声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone
3.全局声明
```
var name = 'Matt';
console.log(window.name); // 'Matt'
let age = 26;
console.log(window.age); // undefined
```
4.条件声明（(let不可依赖)
**因为 let 的作用域是块，所以不可能检查前面是否已经使用 let 声明过同名变量，同时也就不可能在没有声明的情况下声明它**！
5. for 循环中的 let 声明
```
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
`const person = {};
person.name = 'Matt'; // ok 
`
(PS:参考py中的元组中存列表，列表可修改添加元素，列表本身的指向不可更改)
3.对 for-of 和 for-in 循环特别有意义
`for (const key in {a: 1, b: 2}) {
 console.log(key);
}
// a, b 
`
### ④ 声明风格及最佳实践
1. 不使用 var
2. const 优先，let 次之
### IV.数据类型
6 种原始类型：Undefined、Null、Boolean、Number、String 和 Symbol
1 种复杂数据类型： Object
#### ①typeof 操作符
 "undefined"表示值未定义；
 "boolean"表示值为布尔值；
 "string"表示值为字符串；
 "number"表示值为数值；
 "object"表示值为对象（而不是函数）或 null；
 "function"表示值为函数；
 "symbol"表示值为符号。
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
```
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
`let result = Number.MAX_VALUE + Number.MAX_VALUE;
console.log(isFinite(result)); // false `
##### 3. NaN
Not a Number!!!!任何**涉及 NaN 的操作始终返回 NaN**
`console.log(0/0); // NaN
console.log(-0/+0); // NaN `
NaN 不等于包括 NaN 在内的任何值
**`console.log(NaN == NaN); // false`**
isNaN方法
```
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false，10 是数值
console.log(isNaN("10")); // false，可转换为数值 10
console.log(isNaN("blue")); // true，不可转换为数值
console.log(isNaN(true)); // false，可转换为数值 1
```

##### 4.数值转换
Number()
```
let num1 = Number("Hello world!"); // NaN
let num2 = Number(""); // 0
let num3 = Number("000011"); // 11
(八进制忽略0，十六进制返回对应十进制数)
let num4 = Number(null); // 0
let num5 = Number(undefined); // NaN
```
parseInt()
字符串最前面的空格会被忽略，从第一个非空格字符开始转换。如果第一个字符不是数值字符、加号或减号，parseInt()立即返回 NaN
```
let num1 = parseInt("1234blue"); // 1234
let num2 = parseInt(""); // NaN (此处与Number不同！) 
let num3 = parseInt("0xA"); // 10，解释为十六进制整数
let num4 = parseInt(22.5); // 22
let num5 = parseInt("70"); // 70，解释为十进制值
//可以加入第二个参数表示进制
let num1 = parseInt("A", 16); // 10，解释为十六进制整数
```
parseFloat()
从位置 0 开始检测每个字符。同样，它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。
这意味着**第一次出现的小数点是有效的，但第二次出现的小数点就无效了，此时字符串的剩余字符都会被忽略**
十六进制数值始终会返回 0。因为parseFloat()只解析十进制值
```
let num1 = parseFloat("1234blue"); // 1234，按整数解析
let num2 = parseFloat("0xA"); // 0
let num3 = parseFloat("22.5"); // 22.5
let num4 = parseFloat("22.34.5"); // 22.34
let num5 = parseFloat("0908.5"); // 908.5 
```
##### 5.String 类型
表示零或多个 16 位 Unicode 字符序列
可由双引号（"）、单引号（'）或反引号（`）标示
###### (1) 字符字面量
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
###### (2)字符串的特点
字符串是不可变的（immutable），一旦创建，它们的值就不能变了。
要修改某个变量中的字符串值，必须先**销毁**原始的字符串，然后将包含新值的另一个字符串保存到该变量
###### (3) 转换为字符串
toString()方法:返回当前值的字符串等价物
```
let age = 11;
let ageAsString = age.toString(); // 字符串"11"
```
(Ps:可以在()加入数字x表示转化成x进制数)
数的字符串表示，比如：
```
let num = 10;
console.log(num.toString(2)); // "1010"
```
String()方法
(Ps:null 和 undefined 没有 toString()方法,考虑用String()
**不确定一个值是不是 null 或 undefined，可以使用 String()转型函数**)
`console.log(num.String())"null" `
###### (4)模板字面量
ECMAScript 6 新增了使用模板字面量定义字符串的能力
模板字面量**保留换行字符，可以跨行定义字符串**：
(PS:此处指定义模板时规定的换行，空格也全部保留)
```
let myMultiLineString = 'first line\nsecond line';
let myMultiLineTemplateLiteral = `first line
second line`;
console.log(myMultiLineString);
console.log(myMultiLineTemplateLiteral);
(二者输出均为.二者可===)
// first line
// second line"
```
###### (5)字符串插值  ※（6）※为啥丢了？
模板字面量不是字符串，而是一种特殊的 JavaScript 句法表达式，
只不过求值后得到的是字符串
字符串插值通过在${}中使用一个 JavaScript 表达式实现：
```
let value = 5;
let exponent = 'second';
let Temp ='${ value } to the ${ exponent } power is ${ value * value }'; 
 console.log(Temp); // 5 to the second power is 25 
 ```
 (Ps:参考python字符串中,""前加f的情形下用{}包括变量名来取其值)
(所有插入的值都会使用 toString()强制转型为字符串)
>在插值表达式中可以调用函数和方法：
 function capitalize(word) {
 return `${ word[0].toUpperCase() }${ word.slice(1) }`;
}//**此处slice暂时存疑(切片？)**
console.log(`${ capitalize('hello') }, ${ capitalize('world') }!`); // Hello, World!

###### (6)模板字面量标签函数  ※
标签函数（tag function）:可以**自定义插值行为**。标签函数会**接收** 被插值记号分隔后的模板function name(strings,**表达式参数**);
eg:
 ```
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
(PS:**剩余操作符
函数的参数以 … 为前缀，则该参数是剩余参数（rest parameter）。
剩余参数表示参数个数不确定的参数列表。
在函数被调用时,该形参会成为一个数组,数组中的元素都是传递给该函数的多出来的实参的值。**)
```
let a = 6;
let b = 9;
function simpleTag(strings, ...expressions) {
 console.log(strings);
 for(const expression of expressions) {
 console.log(expression);
 }
 return 'foobar';
}
let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
// ["", " + ", " = ", ""]
// 6
// 9
// 15
console.log(taggedResult); // "foobar" 
```
**有 n 个插值的模板字面量，传给标签函数的表达式参数的个数始终是 n**，**而传给标签函数的第一个参数所包含的字符串个数则始终是 n+1**。
下面代码把这些字符串和对表达式求值的结果**拼接**为返回的字符串：
(PS:下面代码中n=3,strings[0]包含4个字符串个数)
```
let a = 6;
let b = 9;
function zipTag(strings, ...expressions) {
 return strings[0] +
 expressions.map((e, i) => `${e}${strings[i + 1]}`)
 .join('');
}
let taggedResult = zipTag`${ a } + ${ b } = ${ a + b }`;
console.log(taggedResult); // "6 + 9 = 15" 
```
(PS：此处码住
**map() 方法创建一个新数组，其结果是该数组中的每个元素是调用一次提供的函数后的返回值。**)
(.join('')方法 用''分割数组各元素)

###### (7) 原始字符串
String.raw 标签函数：直接获取原始的模板字面量内容
`console.log(`\u00A9`); // ©
console.log(String.raw`\u00A9`); // \u00A9`

##### 6. Symbol 类型
ES6新增，**符号是原始值，且符号实例是唯一、不可变的**。(PS:这意味着两个Symbol做==比较为false)
符号的用途是确保对象属性使用唯一标识符，不会发生属性冲突的危险
###### (1). 符号的基本用法
符号需要使用 Symbol()函数初始化
`let sym = Symbol();`
>调用 Symbol()函数时，也可以传入一个字符串参数作为对符号的描述（description），将来可以通过这个字符串来调试代码。但是，这个字符串参数与符号定义或标识完全无关
`let sym = Symbol('meiyong');`

**Symbol()函数不能与 new 关键字一起作为构造函数使用**
避免创建符号包装对象(object)
(PS:用 Boolean、String 或 Number创建new,均为object 见下面7.Object 类型)
###### (2). 使用全局符号注册表
如果运行时的不同部分**需要共享和重用符号实例**，那么可以用一个字符串作为键，在全局符号注册表中创建并重用符号:
使用 Symbol.for()方法(*传入的参数都会被转化为字符串*)
`let fooGlobalSymbol = Symbol.for('foo');`
(PS:后面可再次创一个指向'foo'的Symbol,二者===)
使用Symbol.keyFor()来查询全局注册表
```
// 创建全局符号
let s = Symbol.for('foo');
console.log(Symbol.keyFor(s)); // foo
// 创建普通符号
let s2 = Symbol('bar');
console.log(Symbol.keyFor(s2)); // undefined 
```
###### (3). 使用符号作为属性
```
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

###### (4).常用内置符号 
ES6引入了常用内置符号（well-known symbol），用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为。这些内置符号都以 Symbol 工厂函数字符串属性的形式存在。所有内置符号属性都是不可写、不可枚举、不可配置的。
应用eg:🤔
for-of 循环会在相关对象上使用 Symbol.iterator 属性，那么就可以通过在自定义对象上重新定义Symbol.iterator 的值，来改变 for-of 在迭代该对象时的行为
>在提到 ECMAScript 规范时，经常会引用符号在规范中的名称，前缀为@@。比如，@@iterator 指的就是 Symbol.iterator。

###### (5). Symbol.asyncIterator  ※M
这个符号表示实现异步迭代器 API 的函数
mark,看完迭代器再说www
###### (6). Symbol.hasInstance
该方法决定一个构造器对象是否认可一个对象是它的实例
(*咋感觉有点像亲子鉴定*)
instanceof 操作符可以用来**确定一个对象实例的原型链上是否有原型**
```
function Foo() {}
let f = new Foo();
console.log(f instanceof Foo); // true
```
ES6中:
```
function Foo() {}
let f = new Foo();
console.log(Foo[Symbol.hasInstance](f)); // true
```
>这个属性定义在 Function 的原型上，因此默认在所有函数和类上都可以调用..
instanceof操作符会在原型链上寻找这个属性定义，就跟在原型链上寻找其他属性一样,
可以在继承的类上通过**静态方法**重新定义这个函数：
```
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
###### (7). Symbol.isConcatSpreadable   M
一个布尔值，如果是 true，则意味着对象应该用 Array.prototype.concat()打平其数组元素!
mark,学习concat
###### (8). Symbol.iterator M 迭代器
###### (9). Symbol.match   ※
用**正则表达式**去匹配字符串。由 String.prototype.match()方法使用
```
console.log('foobar'.match(/bar/));
// ["bar", index: 3, input: "foobar", groups: undefined]
```
给这个方法传入非正则表达式值会导致该值被转换为 RegExp 对象!!
如果让方法直接使用参数，则可以重新定义 Symbol.match 函数以取代默认对正则表达式求值的行为，从而让match()方法使用非正则表达式实例。
Symbol.match 函数接收一个参数(下面②接的参数str)，就是(target)调用 match()方法的字符串实例。：
```
①class FooMatcher {
 static [Symbol.match](target) {
 return target.includes('foo');
 }
}
console.log('foobar'.match(FooMatcher)); // true
console.log('barbaz'.match(FooMatcher)); // false
②class StringMatcher {
 constructor(str) {
 this.str = str;
 }
 [Symbol.match](target) {
 return target.includes(this.str);
 }
}
console.log('foobar'.match(new StringMatcher('foo'))); // true
console.log('barbaz'.match(new StringMatcher('qux'))); // false 
```
###### (10). Symbol.replace  
```
// 'foobarbaz' 
console.log('foobarbaz'.replace(/bar/, 'qux'));
// 'fooquxbaz' 
```
给这个方法传入非正则表达式值会导致该值被转换为RegExp对象!!
同上(9)可以重新定义 Symbol.replace 函数以取代默认对正则表达式求值的行为
###### (11). Symbol.search  
该方法返回字符串中匹配正则表达式的索引
(PS:有点像数组考虑0时的位置)。
```
console.log('foobar'.search(/bar/));
// 3 
```
###### (12). Symbol.species  ※
一个函数值，该函数作为创建派生对象的构造函数.
在**内置类型**中最常用，用于对内置类型实例方法的返回值暴露实例化派生对象的方法。用 Symbol.species 定义静态的获取器（getter）方法，可以覆盖新创建实例的原型定义：
```
class Bar extends Array {}
class Baz extends Array {
 static get [Symbol.species]() {
 return Array;
 }
}
let bar = new Bar();
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true
bar = bar.concat('bar');
console.log(bar instanceof Array); // true
console.log(bar instanceof Bar); // true
let baz = new Baz();
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); // true
baz = baz.concat('baz');
console.log(baz instanceof Array); // true
console.log(baz instanceof Baz); // false 
```
###### (13). Symbol.split
在匹配正则表达式的索引位置拆分字符串。由 String.prototype.split()方法使用
```
console.log('foobarbaz'.split(/bar/));
// ['foo', 'baz']
```
###### (14). Symbol.toPrimitive @
将对象转换为相应的原始值。由 ToPrimitive 抽象操作使用
根据提供给这个函数的参数（string、number 或 default），可以控制返回的原始值：
```

```
###### (15). Symbol.toStringTag @
该字符串用于创建对象的默认字符串描述
通过 toString()方法获取对象标识时，会检索由 Symbol.toStringTag 指定的实例标识符，默认为"Object"。内置类型已经指定了这个值，但自定义类实例还需要明确定义：
```

```
###### (16). Symbol.unscopables 
该对象所有的以及继承的属性，都会从关联对象的 with 环境绑定中排除
设置这个符号并让其映射对应属性的键值为 true，就可以
阻止该属性出现在 with 环境绑定中，如下例所示：
```
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

##### 7.Object 类型
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
####①一元操作符


### VI.语句
### VII.函数


