# Shell

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell。

Shell 编程跟 JavaScript、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux 的 Shell 种类众多，常见的有：

* Bourne Shell（/usr/bin/sh或/bin/sh）
* Bourne Again Shell（/bin/bash）
* C Shell（/usr/bin/csh）
* K Shell（/usr/bin/ksh）
* Shell for Root（/sbin/sh）

Bash，也就是 Bourne Again Shell，由于易用和免费，Bash 在日常工作中被广泛使用。同时，Bash 也是大多数Linux 系统默认的Shell。在一般情况下，人们并不区分 Bourne Shell 和 Bourne Again Shell，所以，像 **#!/bin/sh**，它同样也可以改为 **#!/bin/bash**。**#!** 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

## 运行Shell脚本

### 作为可执行程序

```
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh  #执行脚本
```
注意，一定要写成 **./test.sh**，而不是 **test.sh**，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

### 作为解释器参数

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：

`/bin/sh test.sh`

`/bin/php test.php`

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

# Shell变量

`your\_name="runoob.com"`

1. 定义变量不需要$
2. 变量名和等号之间不能有空格
3. 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
4. 不能使用标点和bash关键字
5. 还可以使用语句给变量赋值例如`for file in `ls /etc` 将/etc下的目录的文件名循环出来`
6. 使用变量要加$，花括号{}是可选的
7. `readonly myUrl`将变量定义为只读不可改
8. `unset myName`将变量删除

# 字符串

## 单引号

* 字符原样输出，字符串中的变量无效
* 单引号中不能出现单独的单引号，即使转义也不行，但可以成对出现，作为拼接使用

## 双引号

* 可以出现变量
* 可以出现转义字符

## 获取字符串长度

```
string="abcd"
echo ${#string} # 输出4
```
## 提取子字符串

```
string="runoob is a great site"
echo ${string:1:4} # 输出unoo
```
## 查找子字符串

```
string="runoob is a great site"
echo `expr index "$string" io` # 输出4 查找i或o的位置，哪个先出现就计算哪个
```
# 数组

只支持一维数组，下标从零开始

## 定义数组

```
array_name(value0 value1 value2)
array_name2(
value0
value1
value2
)
array[0]=value0
array[1]=value1
array[2]=value2
```
## 读取数组

```
valuen=${array_name[n]}

# 获取数组中的所有元素
valueAll=${array_name[@]}

# 获取数组元素个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}

# 单个元素长度
lengthn=${#array_name[n]}
```
# 注释

`#`

```
:<<EOF
多行
注释
EOF
```
EOF可以替换成任意字符

# 传参

```
echo "文件名: $0"
echo "参数1：$1"
echo "参数2: $2"
echo "传递到脚本的个数：$#"
echo "字符串显示所有传参：$*"
echo "脚本运行的当前进程ID: $$"
echo "后台运行的最后一个进程ID: $!"
echo "与$*相同，但是使用时加引号: $@"
echo "显示Shell使用的当前选项，与set命令功能相同: $-"
echo "显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误: $?"
```
# 运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

expr 是一款表达式计算工具，使用它能完成表达式的求值操作。例如，两个数相加(**注意使用的是反引号** **`** **而不是单引号** **'**)：

* 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
* 完整的表达式要被 **` `** 包含

```
#!/bin/bash

echo ============算术运算符=============
a=10
b=20

val=`expr $a + $b`
echo $val
val=`expr $a - $b`
echo $val
val=`expr $a \* $b` #必须加\
echo $val
val=`expr $b / $a`
echo $val
val=`expr $b % $a`
echo $val
if [ $a == $b ]
then
	echo "a=b"
else
	echo "a!=b"
fi

echo ==============关系运算符===========
if [ $a -eq $b ]
then
	echo "a等于b"
fi
if [ $a -ne $b ]
then
	echo "a不等于b"
fi
if [ $a -gt $b ]
then
	echo "a大于b"
fi
if [ $a -lt $b ]
then
	echo "a小于b"
fi
if [ $a -ge $b ]
then
	echo "a大于等于b"
fi
if [ $a -le $b ]
then
	echo "a小于等于b"
fi

echo ============布尔运算符==========
if [ $a != $b ]
then
	echo "a不等于b"
fi
if [ $a -lt 15 -a $b -gt 15 ]
then
	echo "a小于15 且 b大于15"
fi
if [ $a -lt 10 -o $b -gt 10 ]
then
	echo "a小于10 或 b大于10"
fi

echo ============逻辑运算符===========
if [[ $a -lt 100 && $b -gt 100 ]] # 这里需要两个括号
then
	echo "a小于100且b大于100"
fi
if [[ $a -lt 100 || $b -gt 100 ]]
then
	echo "a小于100或b大于100"
fi

echo ===========字符串运算=============
a="abcd"
b="efgh"

if [ $a = $b ]
then
	echo "a等于b"
else
	echo "a不等于b"
fi
if [ $a != $b ]
then
	echo "a不等于b"
fi
if [ -z $a ]
then
	echo "字符串长度为0"
fi
if [ -n $a ]
then
	echo "字符串长度不为0"
fi
if [ $a ]
then
	echo "字符串不为空"
fi

echo =========文件测试运算符=========
file="C:/Users/ehuasag/Desktop/2.sh"
if [ -r $file ]
then
	echo "可读"
fi
if [ -w $file ]
then
	echo "可写"
fi
if [ -x $file ]
then
	echo "可执行"
fi
if [ -f $file ]
then
	echo "是普通文件"
fi
if [ -d $file ]
then
	echo "是文件夹"
fi
if [ -s $file ]
then
	echo "文件不为空"
fi
if [ -e $file ]
then
	echo "文件存在"
fi
```
# echo

## 普通字符串

`echo "It is a test"`

`echo It is a test`

## 转义字符串

`echo "\"It is a test\""`

## 显示换行

`echo -e "OK! \n" # -e开启转义`

`echo "Test"`

结果：

OK！

Test

## 显示不换行

`echo -e "OK! \c" # \c 不换行`

`echo "Test"`

结果：

OK！Test

## 字符原样输出

`echo '$name\"'` 单引号不转义也不取变量

## 显示命令执行结果

`echo `date`` 返回当下日期

# test命令

用于检测某个条件是否成立，可以进行数值、字符、文件测试

## 数值测试

`-eq等于 -ne不等于 -gt大于 -ge大于等于 -lt小于 -le小于等于`

```
if test $[num1] -eq $[num2]
then
	echo "相等"
fi
```
`[]`中执行基本的算数运算

```
a=5
b=6
result=$[a+b] # 两边不能有空格
echo "$result"
```
## 字符串测试

`=等于 !=不等于 -z字符串长度为零 -n字符串长度不为0`

```
if test $num1 = $num2
then
	echo "字符串相同"
fi
```
## 文件测试

```
cd /bin
if test -e ./bash
then
	echo "文件存在"
else
	echo "文件不存在"
fi
```
# 流程控制

## if...else...fi

```
if condition
then
	command1
  command2
  ...
elif condition2
then
	commandn
else
	command
fi
```
## for循环

```
for var in item1 item2 ... itemN
do
	command1
  command2
done
```
`for var in item1 item2; do command1; command2; done;`

# 函数

```
#!/bin/bash

echo =========== 函数定义 =============

#也可以定义为 function fun()
demoFunc(){
	# return省略则返回最后一条命令
	echo "shell function"
}
demoFunc

echo ============ 函数返回值 =============

funWithReturn(){
	echo "first number:"
	read num1
	echo "second number:"
	read num2
	echo "num1 + num2 ="
	return $(($num1+$num2))
}
# 返回值在调用该函数后立即通过?来获取，并保存，且?只对上一条指令负责。否则将得到0
funWithReturn
echo "$?"

echo ============ 函数参数 ===============

funWithParam(){
	echo "first param: $1"
	echo "second param: $2"
	echo "tenth param: ${10}" #超过9必须带{}
	echo "total number of params are: $#"
	echo "output all params as a string: $*"
}
funWithParam 1 2 3 4 5 6 7 8 9 10

```
# 输入/输出重定向

|  |  |
| --- | --- |
| 命令 | 说明 |
| command > file | 将输出重定向到 file。 |
| command < file | 将输入重定向到 file。 |
| command >> file | 将输出以追加的方式重定向到 file。 |
| n > file | 将文件描述符为 n 的文件重定向到 file。 |
| n >> file | 将文件描述符为 n 的文件以追加的方式重定向到 file。 |
| n >& m | 将输出文件 m 和 n 合并。 |
| n <& m | 将输入文件 m 和 n 合并。 |
| << tag | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。 |

*需要注意的是文件描述符 0 通常是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。*

## 输出重定向

```
[root@VM-4-6-centos shell]# who > users

[root@VM-4-6-centos shell]# cat users
root     pts/0        2022-10-11 20:15 (101.86.63.106)
root     pts/1        2022-10-11 20:15 (101.86.63.106)

[root@VM-4-6-centos shell]# echo "www.baidu.com" >> users
[root@VM-4-6-centos shell]# cat users
root     pts/0        2022-10-11 20:15 (101.86.63.106)
root     pts/1        2022-10-11 20:15 (101.86.63.106)
www.baidu.com

```
## 输入重定向

```
[root@VM-4-6-centos shell]# wc -l < users
3

command1 <infile > outfile # 从infile读取内容输出到outfile中
```
## 深入

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。

标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。

标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

默认情况下，command > file 将 stdout 重定向到 file，command < file 将stdin 重定向到 file。

`$ command 2>file #如果希望 stderr 重定向到 file，可以这样写`

# 文件包含

Shell 也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。

```
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
```

```
#!/bin/bash

url="www.baidu.com"
```

```
#!/bin/bash

#使用 . 号来引用test1.sh 文件
. ./test1.sh

# 或者使用以下包含文件代码
# source ./test1.sh

echo "baidu：$url"
```

```
[root@VM-4-6-centos shell]# chmod +x test2.sh
[root@VM-4-6-centos shell]# ./test2.sh
baidu: www.baidu.com
```
