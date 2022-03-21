#JavaScript高级程序设计（第4版）学习笔记
(引用部分多为CV或代码，有删改调整)
(善良的黄字绿字部分是"饼饼"标注过的吗？%%%)
(※暂时加强记忆)
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
## No1. 什么是 JavaScript
###I.历史(略)
###II.JavaScript 实现
####①完整的 JavaScript 实现
 1. 核心（ECMAScript）
 2. 文档对象模型（DOM）
 3. 浏览器对象模型（BOM）

**Web 浏览器只是 ECMAScript 实现可能存在的一种宿主环境（host environment）**
宿主环境提供ECMAScript 的基准实现和与环境自身交互必需的扩展。
扩展（比如 DOM）使用 ECMAScript 核心类型和语法，提供特定于环境的额外功能。
**其他宿主环境还有服务器端 JavaScript 平台 Node.js**
####②文档对象模型（DOM，Document Object Model）※
DOM是一个应用编程接口（API），**提供与网页内容交互的方法和接口**。用于在 HTML 中使用扩展的 XML。DOM 将整个页面抽象为一组分层节点。HTML 或 XML 页面的每个组成部分都是一种**节点(node)**，包含不同的数据
*PS:经常用DOM树描述各分层节点的联系*
>注意 DOM 并非只能通过 JavaScript 访问，而且确实被其他很多语言实现了。不过对于浏览器来说，DOM 就是用ECMAScript 实现的，如今已经成为 JavaScript 语言的一大组成部分。
####③浏览器对象模型（BOM） 
**提供与浏览器交互的方法和接口**,用于支持访问和操作浏览器的窗口。使用 BOM，开发者可以操控浏览器显示页面之外的部分

## No2. HTML 中的 JavaScript
###I.\<script>元素
####①.\<script>元素有下列 7 个(尚在使用)属性。
> async：可选。**表示应该立即开始下载脚本，但不能阻止其他页面动作，比如下载资源或等待其他脚本加载**。**只对外部脚本文件有效**。
 charset：可选。使用 src 属性指定的代码字符集。这个属性很少使用，因为大多数浏览器不在乎它的值。
 crossorigin：可选。配置相关请求的CORS（跨源资源共享）设置。默认不使用CORS。crossorigin="anonymous"配置文件请求不必设置凭据标志。crossorigin="use-credentials"设置凭据标志，意味着出站请求会包含凭据。
 defer：可选。**表示脚本可以延迟到文档完全被解析和显示之后再执行**。**只对外部脚本文件有效**。在 IE7 及更早的版本中，对行内脚本也可以指定这个属性。
 integrity：可选。允许比对接收到的资源和指定的加密签名以验证子资源完整性（SRI，Subresource Integrity）。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于**确保内容分发网络（CDN，Content Delivery Network）不会提供恶意内容**。
 src：可选。**!!表示包含要执行的代码的外部文件**。
 type：表示代码块中**脚本语言的内容类型**（也称 MIME 类型）。按照惯例，这个值始终都是**"text/javascript"**，尽管"text/javascript"和"text/ecmascript"都已经废弃了。JavaScript 文件的 MIME 类型通常是"application/x-javascript"，不过给type 属性这个值有可能导致脚本被忽略。在非 IE 的浏览器中有效的其他值还有"application/javascript"和"application/ecmascript"。如果这个值是 module，则代码会被当成 ES6 模块，而且只有这时候代码中才能出现 import 和 export 关键字。
####②.行内嵌入与引入外部文件

**在\<script>元素中的代码被计算完成之前，页面的其余内容不会被加载，也不会被显示**\<script>...\</script> 
在使用行内 JavaScript 代码时，要注意代码中不能出现字符串</script>  实在想用,考虑转义字符“\”
要包含外部文件中的 JavaScript，就必须使用 src 属性。这个属性的值是一个 URL，指向包含JavaScript 代码的文件:
>\<script src="example.js"></script>

>浏览器不会检查所包含 JavaScript 文件的扩展名
(PS:不一定非得是js，可ts)

>浏览器在解析外部（网站）资源时，会向 src 属性指定的路径发送一个 GET 请求，请求不受浏览器同源策略限制，但返回并被执行的 JavaScript 则受限制。来自外部域的代码会被当成加载它的页面的一部分来加载和解释.
PS:**!!须要确保该链接的可靠性**

>浏览器都会按照\<script>在页面中出现的顺序依次解释它们，前提是它们没有使用 defer 和 async 属性

####③\<script>标签位置
(PS:犹记得实习期间刚学时放head里还是放body里,坑的教训)
1.head里的坏处:把所有 JavaScript文件都放在<head>里，也就意味着必须把所有 **JavaScript 代码都下载、解析和解释完成后，才能开始渲染页面**（**页面在浏览器解析到\<body>的起始标签时开始渲染**）。对于需要很多 JavaScript 的页面，这会**导致页面渲染的明显延迟，在此期间浏览器窗口完全空白**。
2.现代 Web 应用程序通常将所有 JavaScript 引用放在\<body>
元素中的页面内容**后面**

####④推迟执行脚本(defer)※
脚本会被延迟到整个页面都解析完毕后再运行。在\<script>元素上
设置 defer 属性
>\<script defer src="example1.js">\</script>

即告诉浏览器**立即下载，但延迟执行**

只对外部脚本文件才有效，最好只包含一个这样的脚本
####⑤ 异步执行脚本(async)※
1.脚本不需要等待其他脚本，同时也不阻塞文档渲染，即异步加载
2.只适用于外部脚本，会告诉浏览器立即开始下载,与 defer 不同的是标记为 async 的脚本并不保证能按照它们出现的次序执行!
3.**异步脚本不应该在加载期间修改 DOM！**
4.**异步脚本保证会在页面的 load 事件前执行，但可能会在 DOMContentLoaded之前或之后**
####⑥动态加载脚本(document.createElement('script'))※
1.JavaScript 可以使用 DOM API，所以通过向 DOM 中动态添加 script 元素同样可以加载指定的脚本。只要创建一个 script 元素并将其添加到DOM
>let script = document.createElement('script');
script.src = 'my.js';
document.head.appendChild(script); 

*(PS:实习时搞的百度搜索用了这种动态加载方法，实时获取了真实搜索结果数据并存入一个json中)*
2.这种方式创建的\<script>元素是以异步方式加载的，相当于添加了 async 属性!
想让预加载器知道这些动态请求文件的存在，可以在文档头部显式声明它们：
>\<link rel="preload" href="my.js"> 

>(已退出舞台)XHTML 中的变化:
在 XHTML 中使用 JavaScript 必须指定 type 属性且值为text/javascript

>\<!-- >用黑科技! -->

###II.\<script>行内代码与外部文件(这里有解释放外部文件的好处)
在配置浏览器请求外部文件时，要重点考虑的一点是它们会占用多少带宽。在 SPDY/HTTP2 中，预请求的消耗已显著降低，以**轻量、独立** JavaScript 组件形式向客户端送达脚本更具优势。
>在初次请求时，如果浏览器支持 SPDY/HTTP2，就可以从同一个地方取得一批文件，并将它们逐个放到浏览器缓存中。
在第二个页面请求时，由于你已经把应用程序切割成了轻量可缓存的文件，第二个页面也依赖的某些组件此时已经存在于浏览器缓存中了。
###III.文档模式(doctype)(简略)
**1.混杂模式（quirks mode）**
让 IE 像 IE5 一样（支持一些非标准的特性）
**2.标准模式（standards mode）**
让 IE 具有兼容标准的行为
>上两种模式的主要区别只体现在通过 CSS 渲染的内容方面，但对JavaScript 也有一些关联影响，或称为副作用

**3.准标准模式（almost standards mode）:**
这种模式下的浏览器支持很多标准的特性，但是没有标准规定得那么严格。主要区别在于如何对待图片元素周围的空白（在表格中使用图片时最明显）。
(PS:这块尚未自己实验区分对比)
###IV.\<noscript>元素(适配不认js的browser)(少见)
用于给不支持 JavaScript 的浏览器提供替代内容
\<noscript>元素可以包含任何可以出现在<body>中的 HTML 元素
>1.浏览器不支持脚本；
2.浏览器对脚本的支持被关闭。
任何一个条件被满足，包含在\<noscript>中的内容就会被渲染

## No3.JavaScript语言基础 

###I.语法
####①基础
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
>"use strict";//是一个预处理指令

6.语句结尾加;
有助于在**某些情况下提升性能**，因为解析器会尝试在合适的
位置补上分号以纠正语法错误!
###II.关键字与保留字
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

###III.变量
ECMAScript 变量是松散类型的，意思是变量可以用于保存**任何**类型的数据。每个变量只不过是一个用于保存任意值的命名占位符
(PS:不必像C艹里的int double....)
####①var声明
不初始化的情况下，变量会保存一个特殊值 undefined
1. var 声明作用域
var 操作符定义的变量会成为包含它的函数的局部变量，退出后销毁
>function test() {
 var message = "hi"; // 局部变量
 //message = "hi";//全局变量,下面就不会报错了(不推荐|严格模式下ReferenceError)
}
test();
console.log(message); // 出错！

定义多个变量
var a = "hi",
 b = false,
 c = 29; 
2. var 声明提升
变量会自动提升到函数作用域顶部!
>function foo() {
 console.log(a);
 var a = 26;
}
foo(); // undefined
其实解析时是:
>function foo() {
 **var a**//hoist
 console.log(a);
 a = 26;
}
foo(); // 26
####②let声明
1.对比
let 声明的范围是块作用域，不可重复声明，不提升
var 声明的范围是函数作用域，可重复声明
>var name;
let name; // SyntaxError
let age;
var age; // SyntaxError

2.TDZ
在 let 声明之前的执行瞬间被称为“暂时性死区”（temporal dead zone
3.全局声明
>var name = 'Matt';
console.log(window.name); // 'Matt'
let age = 26;
console.log(window.age); // undefined

4.条件声明（(let不可依赖)
**因为 let 的作用域是块，所以不可能检查前面是否已经使用 let 声明过同名变量，同时也就不可能在没有声明的情况下声明它**！
5. for 循环中的 let 声明
>for ([此处var/let有不同输出结果] i = 0; i < 5; ++i) {
 setTimeout(() => console.log(i), 0)
}
// let 0、1、2、3、4
// var 5、5、5、5、5 

PS：(var退出循环时,迭代变量保存的是**导致循环退出的值**：5。在之后执行超时逻辑时，**所有的 i 都是同一个变量**，因而输出的都是同一个最终值!)
(let则是**为每个迭代循环声明一个新的迭代变量**,console.log 输出的也就是循环执行过程中**每个迭代变量的值**)
###③const 声明
1.与 let 基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量
2.const 声明的**限制只适用于它指向的变量的引用**。换句话说，如果 const 变量引用的是一个对象，那么**修改这个对象内部的属性并不违反 const 的限制**。
>const person = {};
person.name = 'Matt'; // ok 

3.对 for-of 和 for-in 循环特别有意义
>for (const key in {a: 1, b: 2}) {
 console.log(key);
}
// a, b 

###④ 声明风格及最佳实践
1. 不使用 var
2. const 优先，let 次之
###IV.数据类型
6 种原始类型：Undefined、Null、Boolean、Number、String 和 Symbol
1 种复杂数据类型： Object
####①typeof 操作符
 "undefined"表示值未定义；
 "boolean"表示值为布尔值；
 "string"表示值为字符串；
 "number"表示值为数值；
 "object"表示值为对象（而不是函数）或 null；
 "function"表示值为函数；
 "symbol"表示值为符号。
>typeof null //"object"
null 被认为是一个对空对象的引用!!

to be strict,函数在 ECMAScript 中被认为是对象
####②Undefined 类型







###V.操作符
###VI.语句
###VII.函数


