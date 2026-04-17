# CSS概述

CSS(Cascading Style Sheet):层叠样式表语言。CSS的作用是修饰HTML页面，设置HTML页面中的某些元素的样式，让HTML页面更好看。CSS好比是HTML的化妆品一样。HTML还是主体，CSS依赖HTML。CSS的存在就是修饰HTML，所以新建的文件还是xx.html文件。

# CSS样式

样式表的优先级: 行内样式 > 内部样式 = 外部样式 (后写的覆盖先写的)

## 第一种方式

在标签内部使用style属性来设置元素的CSS样式，这种方式称为内联定义方式。

语法格式：

`<标签 style="样式名:样式值;样式名:样式值;样式名:样式值;..."></标签>`

```html
	<body>
		<!--
			width 宽度样式
			height 高度样式
			background-color 背景色样式
			display 布局样式（none表示隐藏，block表示显示）
		-->
		<div style="width : 300px; height : 300px; background-color : #CCFFFF; display : block;
		border-color : red;border-width : 1px;border-style : solid;"></div>

		<br><br>

		<!--
			样式还能这样写：
				border : 1px solid black;
		-->
		<div style="width : 300px; height : 300px; background-color : #CCFFFF; display : block;
		border : 1px solid black;"></div>

	</body>
```
## 第二种方式

在head标签中使用style块，这种方式被称为样式块方式。

```
  	<head>
      <style type="text/css">
      	选择器 {
        样式名 : 样式值;
        样式名 : 样式值;
        .....
      	}
      	选择器 {
        样式名 : 样式值;
        样式名 : 样式值;
        .....
      	}
      </style>
  	</head>
```

```
	<head>
		<title>HTML中引入CSS样式的第二种方式：样式块</title>

		<style type="text/css">
			/*
				这是CSS的注释。
			*/

			/*
				id选择器
				语法格式：
					#id{
						样式名 : 样式值;
						样式名 : 样式值;
						样式名 : 样式值;
						....
					}
			*/
			#usernameErrorMsg {
				color : red;
				font-size : 12px;
			}

			/*
				标签选择器
				语法格式：
					标签名 {
						样式名 : 样式值;
						样式名 : 样式值;
						样式名 : 样式值;
						....
					}
				标签选择器作用的范围比id选择器广。
			*/
			div {
				background-color : black;
				border : 1px solid red;
				width : 100px;
				height : 100px;
			}

			/*
				类选择器
				语法格式：
					.类名{
						样式名 : 样式值;
						样式名 : 样式值;
						样式名 : 样式值;
						....
					}
			*/
			.student {
				border : 1px solid red;
				width : 400px;
				height : 30px;
			}

		</style>
	</head>
	<body>

		<!--
			设置样式字体大小12px，颜色为红色！
		-->
		<span id="usernameErrorMsg">对不起，用户名不能为空！</span>

		<div></div>
		<div></div>
		<div></div>

		<!--class相同的标签可以认为是同一类标签。-->
		<br><br><br>
		<input type="text" class="student"/>

		<br><br><br>

		<select class="student">
			<option>专科</option>
			<option>本科</option>
		</select>

	</body>
```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>列表样式</title>

		<style type="text/css">
			ul{
				/*list-style-type: none;*/
				/*list-style-type: circle ;*/
				list-style-type: square ;
			}
		</style>
	</head>
	<body>
		<ul>
			<li>中国
				<ul>
					<li>北京</li>
					<li>上海</li>
					<li>重庆</li>
				</ul>
			</li>
			<li>美国</li>
			<li>俄国</li>
		</ul>
	</body>
</html>

```

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>CSS样式的绝对定位</title>
		<style type="text/css">
			#div1{
				background-color: red;
				border: 1px black solid;
				width: 300px;
				height: 300px;
				position : absolute; /*绝对定位*/
				left: 100px;
				top: 100px;
			}
		</style>
	</head>
	<body>

		<div id="div1"></div>

	</body>
</html>

```
## 第三种方式

链入外部样式表文件，这种方式最常用。（将样式写到一个独立的xxx.css文件当中，在需要的网页上直接引入css文件，样式就引入了）

语法格式：`<link type="text/css" rel="stylesheet" href="css文件的路径" />`

这种方式易维护，维护成本较低。xxx.css文件

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>在HTML中使用CSS样式的第三种方式：引入外部独立的css文件</title>

		<!--引入css-->
		<link rel="stylesheet" type="text/css" href="css/1.css" />

	</head>
	<body>

		<a href="http://www.baidu.com">百度</a>

		<span id="baiduSpan">点击我链接到百度哦！</span>
	</body>
</html>

```
# 语法

## 规范

```
h1 {
  /* 字体颜色 */
  color: bule;
  font-size: 40px;
}
```
## 风格

```
h1 {
  /* 字体颜色 */
  color: blue;
  font-size: 40px;
}
```

```
h1{color:blue;font-size:40px;}
```
