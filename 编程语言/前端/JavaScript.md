# JavaScript简介

JavaScript是运行在浏览器上的脚本语言。简称JS。

JavaScript是网景公司（NetScape）的 布兰登艾奇（JavaScript之父）开发的，最初叫做LiveScript。

LiveScript的出现让浏览器更加的生动了，不再是单纯的静态页面了。页面更具有交互性。

在历史的某个阶段，SUN公司和网景公司他们之间有合作关系，SUN公司把LiveScript的名字修改为JavaScript。

JavaScript这个名字中虽然带有“Java”但是和Java没有任何关系，只是语法上优点类似。他们运行的位置不同，

Java运行在JVM当中，JavaScript运行在浏览器的内存当中。

JavaScript程序不需要我们程序员手动编译，编写完源代码之后，浏览器直接打开解释执行。

JavaScript的“目标程序”以普通文本形式保存，这种语言都叫做“脚本语言”。

Java的目标程序已.class形式存在，不能使用文本编辑器打开，不是脚本语言。

网景公司1998年被美国在线收购。

网景公司最著名的就是领航者浏览器：Navigator浏览器。

LiveScript的出现，最初的时候是为Navigator浏览器量身定制一门语言，不支持其他浏览器。

当Navigator浏览器使用非常广泛的时候，微软害怕了，于是微软在最短的时间内组建了一个团队，

开始研发只支持IE浏览器的脚本语言，叫做JScript。

JavaScript和JScript并存的年代，程序员是很痛苦的，因为程序员要写两套程序。

在这种情况下，有一个非营利性组织站出来了，叫做ECMA组织（欧洲计算机协会）

ECMA根据JavaScript制定了ECMA-262号标准，叫做ECMA-Script。

现代的javascript和jscript都实现了ECMA-Script规范。（javascript和jscript统一了。）

以后大家会学习一个叫做JSP的技术，JSP和JS有啥区别？

* JSP : JavaServer Pages（隶属于Java语言的，运行在JVM当中）
* JS : JavaScript（运行在浏览器上。）

2、在HTML中怎么嵌入JavaScript代码？

 三种方式。

### HTML中嵌入JS代码的第一种方式

```
<!doctype html>
<html>
	<head>
		<title>HTML中嵌入JS代码的第一种方式</title>
	</head>
	<body>

		<!--
			1、要实现的功能：
				用户点击以下按钮，弹出消息框。

			2、JS是一门事件驱动型的编程语言，依靠事件去驱动，然后执行对应的程序。
			在JS中有很多事件，其中有一个事件叫做：鼠标单击，单词：click。并且任何
			事件都会对应一个事件句柄叫做：onclick。【注意：事件和事件句柄的区别是：
			事件句柄是在事件单词前添加一个on。】，而事件句柄是以HTML标签的属性存在
			的。

			3、onclick="js代码"，执行原理是什么？
				页面打开的时候，js代码并不会执行，只是把这段JS代码注册到按钮的click事件上了。
				等这个按钮发生click事件之后，注册在onclick后面的js代码会被浏览器自动调用。

			4、怎么使用JS代码弹出消息框？
				在JS中有一个内置的对象叫做window，全部小写，可以直接拿来使用，window代表的是浏览器对象。
				window对象有一个函数叫做:alert，用法是：window.alert("消息");这样就可以弹窗了。

			5、JS中的字符串可以使用双引号，也可以使用单引号。

			6、JS中的一条语句结束之后可以使用分号“;”，也可以不用。
		-->
		<input type="button" value="hello" onclick="window.alert('hello js')"/>

		<input type="button" value="hello" onclick='window.alert("hello jscode")'/>

		<input type="button" value="hello" onclick="window.alert('hello zhangsan')
													window.alert('hello lis')
													window.alert('hello wangwu')"/>

		<!-- window. 可以省略。-->
		<input type="button" value="hello" onclick="alert('hello zhangsan')
													alert('hello lis')
													alert('hello wangwu')"/>

		<input type="button" value="hello" onclick="alert('hello zhangsan');
													alert('hello lis');
													alert('hello wangwu');"/>

	</body>
</html>
```
### HTML中嵌入JS代码的第二种方式

```

<!--
	javascript的脚本块在一个页面当中可以出现多次。没有要求。
	javascript的脚本块出现位置也没有要求，随意。
-->
<script type="text/javascript">
    // alert有阻塞当前页面加载的作用。（阻挡，直到用户点击确定按钮。）
    window.alert("first.......");
</script>

<!doctype html>
<html>
	<head>
		<title>HTML中嵌入JS代码的第二种方式</title>

		<!--样式块-->
		<style type="text/css">
			/*
				css代码
			*/
		</style>

		<script type="text/javascript">
			window.alert("head............");
		</script>

	</head>
	<body>

		<input type="button" value="我是一个按钮对象1" />

		<!--第二种方式：脚本块的方式-->
		<script type="text/javascript">

			/*
				暴露在脚本块当中的程序，在页面打开的时候执行，
				并且遵守自上而下的顺序依次逐行执行。（这个代
				码的执行不需要事件）
			*/
			window.alert("Hello World!"); // alert函数会阻塞整个HTML页面的加载。

			// JS代码的注释，这是单行注释。
			/*
				JS代码的多行注释。和java一样。
			*/
			window.alert("Hello JavaScript!");

		</script>

		<input type="button" value="我是一个按钮对象" />

	</body>
</html>

<script type="text/javascript">
window.alert("last.......");
</script>

<!--
/**
*
* javadoc注释，这里的注释信息会被javadoc.exe工具解析提取生成帮助文档。
*/
-->
```
### HTML中嵌入JS代码的第三种方式

```
<!doctype html>
<html>
	<head>
		<title>HTML中嵌入JS代码的第三种方式：引入外部独立的js文件。</title>
	</head>
	<body>

		<!--在需要的位置引入js脚本文件-->
		<!--引入外部独立的js文件的时候，js文件中的代码会遵循自上而下的顺序依次逐行执行。-->
		<!--
		<script type="text/javascript" src="js/1.js"></script>
		-->

		<!--同一个js文件可以被引入多次。但实际开发中这种需求很少。-->
		<!--
		<script type="text/javascript" src="js/1.js"></script>
		-->

		<!--这种方式不行，结束的script标签必须有。-->
		<!--
		<script type="text/javascript" src="js/1.js" />
		-->
		<!--
		<script type="text/javascript" src="js/1.js"></script>
		-->

		<script type="text/javascript" src="js/1.js">
			// 这里写的代码不会执行。
			// window.alert("Test");
		</script>

		<script type="text/javascript">
			alert("hello jack!");
		</script>
	</body>
</html>
```
### JS中的变量

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>关于JS中的变量</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				回顾java中的变量：
					1、java中怎么定义/声明变量？
						数据类型 变量名;
						例如：
							int i;
							double d;
							boolean flag;
					2、java中的变量怎么赋值？
						使用“=”运算符进行赋值运算。（"="运算符右边先执行，将右边执行的结果赋值给左边的变量。）
						变量名 = 值;
						例如：
							i = 10;
							d = 3.14;
							flag = false;
					3、java语言是一种强类型语言，强类型怎么理解？
						java语言存在编译阶段，假设有代码：int i;
						那么在Java中有一个特点是：java程序编译阶段就已经确定了
						i变量的数据类型，该i变量的数据类型在编译阶段是int类型，
						那么这个变量到最终内存释放，一直都是int类型，不可能变成
						其他类型。
							int i = 10;
							double d = i;
							这行代码是说声明一个新的变量d，double类型，把i变量中保存的值传给d。
							i还是int类型。

							i = "abc"; 这行代码编译的时候会报错，因为i变量的数据类型是int类型，不能将字符串赋给i。

							java中要求变量声明的时候是什么类型，以后永远都是这种类型，不可变。编译期强行固定变量的数据类型。
							称为强类型语言。

							public void sum(int a, int b){}
							sum(?,?);

				javascript当中的变量？
					怎么声明变量？
						var 变量名;
					怎么给变量赋值？
						变量名 = 值;
					javascript是一种弱类型语言，没有编译阶段，一个变量可以随意赋值，赋什么类型的值都行。
						var i = 100;
						i = false;
						i = "abc";
						i = new Object();
						i = 3.14;

					重点：javascript是一种弱类型编程语言。

			*/
		   // 在JS当中,当一个变量没有手动赋值的时候,系统默认赋值undefined
		   var i;
		   // undefined 在JS中是一个具体存在值.
		   alert("i = " + i); // i = undefined

		   alert(undefined);
		   var k = undefined;
		   alert("k = " + k);

		   // 一个变量没有声明/定义,直接访问?
		   // alert(age); //语法错误：age is not defined (变量age不存在。不能这样写。)

		   var a, b, c = 200;
		   alert("a = " + a);
		   alert("b = " + b);
		   alert("c = " + c);

		   a = false;
		   alert(a);

		   a = "abc";
		   alert(a);

		   a = 1.2;
		   alert(a);

		</script>
	</body>
</html>

```
### JS函数

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS函数初步</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				1、JS中的函数：
					等同于java语言中的方法，函数也是一段可以被重复利用的代码片段。
					函数一般都是可以完成某个特定功能的。

				2、回顾java中的方法？
					[修饰符列表] 返回值类型 方法名(形式参数列表){
						方法体;
					}
					例如：
					public static boolean login(String username,String password){
						...
						return true;
					}
					boolean loginSuccess = login("admin","123");

				3、JS中的变量是一种弱类型的，那么函数应该怎么定义呢？
					语法格式：
						第一种方式：
							function 函数名(形式参数列表){
								函数体;
							}
						第二种方式：
							函数名 = function(形式参数列表){
								函数体;
							}

						JS中的函数不需要指定返回值类型，返回什么类型都行。
			*/
		   function sum(a, b){
			   // a和b都是局部变量,他们都是形参(a和b都是变量名，变量名随意。)
			   alert(a + b);
		   }

		   // 函数必须调用才能执行的.
		   //sum(10, 20);

		   // 定义函数sayHello
		   sayHello = function(username){
			   alert("hello " + username);
		   }

		   // 调用函数
		   //sayHello("zhangsan");

		</script>

		<input type="button" value="hello" onclick="sayHello('jack');" />
		<input type="button" value="计算10和20的求和" onclick="sum(10, 20);" />

	</body>
</html>
```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS函数初步</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				java中的方法有重载机制，JS中的函数能重载吗？
					JS当中的函数在调用的时候，参数的类型没有限制，并且参数的个数也没有限制，JS就是这么随意。（弱类型）

				重载的含义：
					方法名或者函数名一样，形参不同（个数、类型、顺序）
			*/
		   function sum(a, b){
			   return a + b;
		   }

		   // 调用函数sum
		   var retValue = sum(1, 2);
		   alert(retValue);

		   var retValue2 = sum("jack"); // jack赋值给a变量,b变量没有赋值系统默认赋值undefined
		   alert(retValue2); // jackundefined

		   var retValue3 = sum();
		   alert(retValue3); // NaN (NaN是一个具体存在的值，该值表示不是数字。Not a Number)

		   var retValue4 = sum(1, 2, 3);
		   alert("结果=" + retValue4); // 结果=3

		   function test1(username){
			   alert("test1");
		   }

		   /*
		   在JS当中，函数的名字不能重名，当函数重名的时候，后声明的函数会将之前声明的同名函数覆盖。
		   */
		   function test1(){
			   alert("test1 test1");
		   }

		   test1("lisi"); // 这个调用的是第二个test1()函数.

		</script>
	</body>
</html>

```
### 局部变量和全局变量

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS的局部变量和全局变量</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				全局变量：
					在函数体之外声明的变量属于全局变量，全局变量的生命周期是：
						浏览器打开时声明，浏览器关闭时销毁，尽量少用。因为全局变量会一直在浏览器的内存当中，耗费内存空间。
						能使用局部变量尽量使用局部变量。
				局部变量：
					在函数体当中声明的变量，包括一个函数的形参都属于局部变量，
					局部变量的生命周期是：函数开始执行时局部变量的内存空间开辟，函数执行结束之后，局部变量的内存空间释放。
					局部变量生命周期较短。
			*/

		   // 全局变量
		   var i = 100;

		   function accessI(){
			   // 访问的是全局变量
			   alert("i = " + i);
		   }

		   accessI();

		   // 全局变量
		   var username = "jack";
		   function accessUsername(){
			   // 局部变量
			   var username = "lisi";
			   // 就近原则:访问局部变量
			   alert("username = " + username);
		   }
		   // 调用函数
		   accessUsername();
		   // 访问全局变量
		   alert("username = " + username);

		   function accessAge(){
			   var age = 20;
			   alert("年龄 = " + age);
		   }

		   accessAge();

		   // 报错(语法不对)
		   // alert("age = " + age);

		   // 以下语法是很奇怪的.
		   function myfun(){
			   // 当一个变量声明的时候没有使用var关键字,那么不管这个变量是在哪里声明的,都是全局变量.
			   myname = "dujubin";
		   }

		   // 访问函数
		   myfun();

		   alert("myname = " + myname); // myname = dujubin

		</script>
	</body>
</html>

```
### 数据类型

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS中的数据类型</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				1、虽然JS中的变量在声明的时候不需要指定数据类型，但是在赋值，每一个数据还是有类型的，所以
				这里也需要学习一下JS包括哪些数据类型？
					JS中数据类型有：原始类型、引用类型。
					原始类型：Undefined、Number、String、Boolean、Null
					引用类型：Object以及Object的子类

				2、ES规范(ECMAScript规范)，在ES6之后，又基于以上的6种类型之外添加了一种新的类型：Symbol

				3、JS中有一个运算符叫做typeof，这个运算符可以在程序的运行阶段动态的获取变量的数据类型。
					typeof运算符的语法格式：
						typeof 变量名
					typeof运算符的运算结果是以下6个字符串之一：注意字符串都是全部小写。
						"undefined"
						"number"
						"string"
						"boolean"
						"object"
						"function"

				4、在JS当中比较字符串是否相等使用“==”完成。没有equals。
			*/

		   /*
		   // 求和,要求a变量和b变量将来的数据类型必须是数字,不能是其他类型
		   // 因为以下定义的这个sum函数是为了完成两个数字的求和.
		   function sum(a, b){
			   if(typeof a == "number" && typeof b == "number"){
				   return a + b;
			   }
			   alert(a + "," + b + "必须都为数字！");
		   }

		   // 别人去调用以上你写的sum函数.
		   var retValue = sum(false, "abc");
		   alert(retValue); // undefined

		   var retValue2 = sum(1, 2);
		   alert(retValue2); // 3
		   */

		  var i;
		  alert(typeof i); // "undefined"

		  var k = 10;
		  alert(typeof k); // "number"

		  var f = "abc";
		  alert(typeof f); // "string"

		  var d = null;
		  alert(typeof d); // "object"  null属于Null类型,但是typeof运算符的结果是"object"

		  var flag = false;
		  alert(typeof flag); // "boolean"

		  var obj = new Object();
		  alert(typeof obj); // "object"

		  // sayHello是一个函数.
		  function sayHello(){

		  }
		  alert(typeof sayHello); // "function"

		</script>
	</body>
</html>

```
### Undefined类型

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Undefined类型</title>
	</head>
	<body>

		<script type="text/javascript">
			/*
				Undefined类型只有一个值，这个值就是 undefined
				当一个变量没有手动赋值，系统默认赋值undefined
				或者也可以给一个变量手动赋值undefined。
			*/
		   var i; // undefined
		   var k = undefined; // undefined

		   alert(i == k); // true

		   var y = "undefined"; // "undefined"
		   alert(y == k); // false

		</script>

	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Number类型</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				1、Number类型包括哪些值？
					-1 0 1 2 2.3 3.14 100 .... NaN Infinity
					整数、小数、正数、负数、不是数字、无穷大都属于Number类型。
				2、isNaN() : 结果是true表示不是一个数字，结果是false表示是一个数字。
				3、parseInt()函数
				4、parseFloat()函数
				5、Math.ceil() 函数（Math是数学类，数学类当中有一个函数叫做ceil()，作用是向上取整。）
			*/
		   var v1 = 1;
		   var v2 = 3.14;
		   var v3 = -100;
		   var v4 = NaN;
		   var v5 = Infinity;

		   // "number"
		   alert(typeof v1);
		   alert(typeof v2);
		   alert(typeof v3);
		   alert(typeof v4);
		   alert(typeof v5);

		   // 关于NaN (表示Not a Number，不是一个数字，但属于Number类型)
		   // 什么情况下结果是一个NaN呢？
		   // 运算结果本来应该是一个数字,最后算完不是一个数字的时候,结果是NaN.
		   var a = 100;
		   var b = "中国人";
		   alert(a / b); // 除号显然最后结果应该是一个数字,但是运算的过程中导致最后不是一个数字,那么最后的结果是NaN

		   var e = "abc";
		   var f = 10;
		   alert(e + f); // "abc10"

		   // Infinity (当除数为0的时候，结果为无穷大)
		   alert(10 / 0);

		   // 思考:在JS中10 / 3 = ?
		   alert(10 / 3); // 3.3333333333333335

		   // 关于isNaN函数？
		   // 用法：isNaN(数据) ,结果是true表示不是一个数字, 结果是false表示是一个数字.
		   // isNaN : is Not a Number
		   function sum(a, b){
			   if(isNaN(a) || isNaN(b)){
				   alert("参与运算的必须是数字！");
				   return;
			   }
			   return a + b;
		   }
		   sum(100, "abc");
		   alert(sum(100, 200));

		   // parseInt():可以将字符串自动转换成数字,并且取整数位.
		   alert(parseInt("3.9999")); // 3
		   alert(parseInt(3.9999)); // 3

		   // parseFloat():可以将字符串自动转换成数字.
		   alert(parseFloat("3.14") + 1); // 4.14
		   alert(parseFloat("3.2") + 1); // 4.2

		   // Math.ceil()
		   alert(Math.ceil("2.1")); // 3

		</script>
	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Boolean类型</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
			1、 JS中的布尔类型永远都只有两个值：true和false （这一点和java相同。）
			2、在Boolean类型中有一个函数叫做：Boolean()。
				语法格式：
					Boolean(数据)
				Boolean()函数的作用是将非布尔类型转换成布尔类型，if中不写时会自动调用。
			*/
		   // var username = "lucy";
		   var username = "";

		   /*
		   if(Boolean(username)){
			   alert("欢迎你" + username);
		   }else{
			   alert("用户名不能为空！");
		   }
		   */

		  /*
		   if(username){
			   alert("欢迎你" + username);
		   }else{
			   alert("用户名不能为空！");
		   }
		   */

		  // 规律:“有"就转换成true,"没有"就转换成false.
		  alert(Boolean(1)); // true
		  alert(Boolean(0)); // false
		  alert(Boolean("")); // false
		  alert(Boolean("abc")); // true
		  alert(Boolean(null)); // false
		  alert(Boolean(NaN)); // false
		  alert(Boolean(undefined)); // false
		  alert(Boolean(Infinity)); // true

		   /*
		   while(10 / 3){
			   alert("hehe");
		   }
		   */

		   for(var i = 0; i < 10; i++){
			   alert("i = " + i);
		   }

		   // Null类型只有一个值,null
			alert(typeof null); // "object"

		</script>
	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>String类型</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				String类型：
					1、在JS当中字符串可以使用单引号，也可以使用双引号。
						var s1 = 'abcdef';
						var s2 = "test";
					2、在JS当中，怎么创建字符串对象呢？
						两种方式：
							第一种：var s = "abc";
							第二种（使用JS内置的支持类String）： var s2 = new String("abc");
						需要注意的是：String是一个内置的类，可以直接用，String的父类是Object。
					3、无论小string还是大String，他们的属性和函数都是通用的。
					4、关于String类型的常用属性和函数？
						常用属性：
							length 获取字符串长度
						常用函数：
							indexOf			获取指定字符串在当前字符串中第一次出现处的索引
							lastIndexOf		获取指定字符串在当前字符串中最后一次出现处的索引
							replace			替换
							substr			截取子字符串
							substring		截取子字符串
							toLowerCase		转换小写
							toUpperCase		转换大写
							split			拆分字符串
			*/
		   // 小string(属于原始类型String)
		   var x = "king";
		   alert(typeof x); // "string"

		   // 大String(属于Object类型)
		   var y = new String("abc");
		   alert(typeof y); // "object"

		   // 获取字符串的长度
		   alert(x.length); // 4
		   alert(y.length); // 3

		   alert("http://www.baidu.com".indexOf("http")); // 0
		   alert("http://www.baidu.com".indexOf("https")); // -1

		   // 判断一个字符串中是否包含某个子字符串？
		   alert("http://www.baidu.com".indexOf("https") >= 0 ? "包含" : "不包含"); // 不包含

		   // replace (注意：只替换了第一个)
		   alert("name=value%name=value%name=value".replace("%","&")); // name=value&name=value%name=value

		   // 继续调用replace方法,就会替换第“二”个.
		   // 想全部替换需要使用正则表达式.
		   alert("name=value%name=value%name=value".replace("%","&").replace("%", "&")); // name=value&name=value&name=value

		   // 考点:经常问 substr和substring的区别？
		   // substr(startIndex, length)
		   alert("abcdefxyz".substr(2,4)); //cdef
		   // substring(startIndex, endIndex) 注意:不包含endIndex
		   alert("abcdefxyz".substring(2,4)); //cd

		</script>
	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Object类型</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				Object类型：
					1、Object类型是所有类型的超类，自定义的任何类型，默认继承Object。
					2、Object类包括哪些属性？
						prototype属性（常用的，主要是这个）：作用是给类动态的扩展属性和函数。
						constructor属性
					3、Object类包括哪些函数？
						toString()
						valueOf()
						toLocaleString()
					4、在JS当中定义的类默认继承Object，会继承Object类中所有的属性以及函数。
					换句话说，自己定义的类中也有prototype属性。

					5、在JS当中怎么定义类？怎么new对象？
						定义类的语法：
							第一种方式：
								function 类名(形参){

								}
							第二种方式：
								类名 = function(形参){

								}
						创建对象的语法：
							new 构造方法名(实参); // 构造方法名和类名一致。
			*/
		   function sayHello(){

		   }

		   // 把sayHello当做一个普通的函数来调用.
		   sayHello();

		   // 这种方式就表示把sayHello当做一个类来创建对象.
		   var obj = new sayHello(); // obj是一个引用,保存内存地址指向堆中的对象.

		   // 定义一个学生类
		   function Student(){
			   alert("Student.....");
		   }

		   // 当做普通函数调用
		   Student();

		   // 当做类来创建对象
		   var stu = new Student();
		   alert(stu); // [object Object]

		   // JS中的类的定义,同时又是一个构造函数的定义
		   // 在JS中类的定义和构造函数的定义是放在一起来完成的.
		   function User(a, b, c){ // a b c是形参,属于局部变量.
			   // 声明属性 (this表示当前对象)
			   // User类中有三个属性:sno/sname/sage
			   this.sno = a;
			   this.sname = b;
			   this.sage = c;
		   }

		   // 创建对象
		   var u1 = new User(111, "zhangsan", 30);
		   // 访问对象的属性
		   alert(u1.sno);
		   alert(u1.sname);
		   alert(u1.sage);

		   var u2 = new User(222, "jackson", 55);
		   alert(u2.sno);
		   alert(u2.sname);
		   alert(u2.sage);

		   // 访问一个对象的属性,还可以使用这种语法
		   alert(u2["sno"]);
		   alert(u2["sname"]);
		   alert(u2["sage"]);

		   // 定义类的另一种语法
		   /*
		   Emp = function(a, b){
			   this.ename = a;
			   this.sal = b;
		   }
		   */

		  Emp = function(ename,sal){
			  // 属性
			  this.ename = ename;
			  this.sal = sal;
		  }

		  var e1 = new Emp("SMITH", 800);
		  alert(e1["ename"] + "," + e1.sal);

		   Product = function(pno,pname,price){
			   // 属性
			   this.pno = pno;
			   this.pname = pname;
			   this.price = price;
			   // 函数
			   this.getPrice = function(){
				   return this.price;
			   }
		   }

		   var xigua = new Product(111, "西瓜", 4.0);
		   var pri = xigua.getPrice();
		   alert(pri); // 4.0

		   // 可以通过prototype这个属性来给类动态扩展属性以及函数
		   Product.prototype.getPname = function(){
			   return this.pname;
		   }

		   // 调用后期扩展的getPname()函数
		   var pname = xigua.getPname();
		   alert(pname)

			// 给String扩展一个函数
		   String.prototype.suiyi = function(){
			   alert("这是给String类型扩展的一个函数，叫做suiyi");
		   }

		   "abc".suiyi();

		</script>
	</body>
</html>
<!--
	java语言怎么定义类，怎么创建对象？（强类型）
		public class User{
			private String username;
			private String password;
			public User(){

			}
			public User(String username,String password){
				this.username = username;
				this.password = password;
			}
		}
		User user = new User();
		User user = new User("lisi","123");

	JS语言怎么定义类，怎么创建对象？（弱类型）
		User = function(username,password){
			this.username = username;
			this.password = password;
		}
		var u = new User();
		var u = new User("zhangsan");
		var u = new User("zhangsan","123");
-->
```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>null NaN undefined这三个值有什么区别</title>
	</head>
	<body>
		<script type="text/javascript">
			// == 是等同运算符
			alert(1 == true); // true
			alert(1 === true); // false

			// null NaN undefined 数据类型不一致.
			alert(typeof null); // "object"
			alert(typeof NaN); // "number"
			alert(typeof undefined); // "undefined"

			// null和undefined可以等同.
			alert(null == NaN); // false
			alert(null == undefined); // true
			alert(undefined == NaN); // false

			// 在JS当中有两个比较特殊的运算符
			// ==(等同运算符：只判断值是否相等)
			// ===(全等运算符：既判断值是否相等，又判断数据类型是否相等)
			alert(null === NaN); // false
			alert(null === undefined); // false
			alert(undefined === NaN); // false
		</script>
	</body>
</html>

```
### 常用事件

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS的常用事件</title>
	</head>
	<body>
		<script type="text/javascript">
			/*
				JS中的事件：

					blur失去焦点
					focus获得焦点

					click鼠标单击
					dblclick鼠标双击

					keydown键盘按下
					keyup键盘弹起

					mousedown鼠标按下
					mouseover鼠标经过
					mousemove鼠标移动
					mouseout鼠标离开
					mouseup鼠标弹起

					reset表单重置
					submit表单提交

					change下拉列表选中项改变，或文本框内容改变
					select文本被选定
					load页面加载完毕（整个HTML页面中所有的元素全部加载完毕之后发生。）

				任何一个事件都会对应一个事件句柄，事件句柄是在事件前添加on。
				onXXX这个事件句柄出现在一个标签的属性位置上。（事件句柄以属性的形式存在。）
			*/
		   // 对于当前程序来说,sayHello函数被称为回调函数(callback函数)
		   // 回调函数的特点:自己把这个函数代码写出来了,但是这个函数不是自己负责调用,由其他程序负责调用该函数.
		   function sayHello(){
			   alert("hello js!");
		   }
		</script>

		<!--注册事件的第一种方式，直接在标签中使用事件句柄-->
		<!--以下代码的含义是：将sayHello函数注册到按钮上，等待click事件发生之后，该函数被浏览器调用。我们称这个函数为回调函数。-->
		<input type="button" value="hello" onclick="sayHello()"/>

		<input type="button" value="hello2" id="mybtn" />
		<input type="button" value="hello3" id="mybtn1" />
		<input type="button" value="hello4" id="mybtn2" />
		<script type="text/javascript">
			function doSome(){
				alert("do some!");
			}
			/*
				第二种注册事件的方式，是使用纯JS代码完成事件的注册。
			*/
		   // 第一步:先获取这个按钮对象(document是全部小写，内置对象，可以直接用，document就代表整个HTML页面)
		   var btnObj = document.getElementById("mybtn");
		   // 第二步:给按钮对象的onclick属性赋值
		   btnObj.onclick = doSome; // 注意:千万别加小括号. btnObj.onclick = doSome();这是错误的写法.
									// 这行代码的含义是,将回调函数doSome注册到click事件上.

		   var mybtn1 = document.getElementById("mybtn1");
		   mybtn1.onclick = function(){ // 这个函数没有名字,叫做匿名函数,这个匿名函数也是一个回调函数.
			   alert("test.........."); // 这个函数在页面打开的时候只是注册上,不会被调用,在click事件发生之后才会调用.
		   }

		   document.getElementById("mybtn2").onclick = function(){
			   alert("test22222222.........");
		   }
		</script>

	</body>
</html>

<!--
	java中也有回调函数机制：
		public class MyClass{

			public static void main(String[] args){
				// 主动调用run()方法，站在这个角度看run()方法叫做正向调用。
				run();
			}

			// 站在run方法的编写者角度来看这个方法，把run方法叫做回调函数。
			public static void run(){
				System.out.println("run...");
			}
		}
-->

```
### JS代码执行顺序

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS代码的执行顺序</title>
	</head>
	<!-- load事件什么时候发生？页面全部元素加载完毕之后才会发生。-->
	<body onload="ready()">

		<script type="text/javascript">

			/*
			// 第一步:根据id获取节点对象
			var btn = document.getElementById("btn"); // 返回null(因为代码执行到此处的时候id="btn"的元素还没有加载到内存)

			// 第二步:给节点对象绑定事件
			btn.onclick = function(){
				alert("hello js");
			}
			*/

		   function ready(){
			   var btn = document.getElementById("btn");
			   btn.onclick = function(){
			   	alert("hello js");
			   }
		   }

		</script>

		<input type="button" value="hello" id="btn" />

	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS代码的执行顺序</title>
	</head>
	<body>

		<script type="text/javascript">
			// 页面加载的过程中,将a函数注册给了load事件
			// 页面加载完毕之后,load事件发生了,此时执行回调函数a
			// 回调函数a执行的过程中,把b函数注册给了id="btn"的click事件
			// 当id="btn"的节点发生click事件之后,b函数被调用并执行.
			window.onload = function(){ // 这个回调函数叫做a
				document.getElementById("btn").onclick = function(){ // 这个回调函数叫做b
					alert("hello js..........");
				}
			}

		</script>

		<input type="button" value="hello" id="btn" />

	</body>
</html>

```
### 设置节点的属性

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>JS代码设置节点的属性</title>
	</head>
	<body>

		<script type="text/javascript">
			window.onload = function(){
				document.getElementById("btn").onclick = function(){
					var mytext = document.getElementById("mytext");
					// 一个节点对象中只要有的属性都可以"."
					mytext.type = "checkbox";
				}
			}
		</script>

		<input type="text" id="mytext"/>

		<input type="button" value="将文本框修改为复选框" id="btn"/>

	</body>
</html>

```
### 捕捉回车键

