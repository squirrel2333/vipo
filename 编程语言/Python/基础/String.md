字符串就是一系列字符。在Python中，用引号括起的都是字符串，其中的引号可以是单引号，

也可以是双引号，如下所示：

`"This is a string."`

`'This is also a string.'`

这种灵活性让你能够在字符串中包含引号和撇号：

```python
'I told my friend, "Python is my favorite language!"'
"The language 'Python' is named after Monty Python, not the snake."
"One of Python's strengths is its diverse and supportive community."
```
转义：`'doesn\'t'`

不希望转义(原始字符串)：`r'okk\t'`原始字符串不能以奇数个\结尾

字符串过长可以用括号扩起来

a = ("123456"
 "789")

```python
my_str = "itheima and itcast"

# 通过索引获取字符
value = my_str[2]
value2 = my_str[-16]

# 获取字符串起始位置的索引
value3 = my_str.index("and")

# 字符串替换 得到新的字符串
new_my_str = my_str.replace("it", "ti")

# 字符串分割 得到新的多个字符串的列表
new_my_str.split(" ")

# 去除前后空格 和换行符
print(new_my_str.strip())
# 去前后指定字符串 去除的是首尾的t和i，按照单个字符 heima and ticas
print(new_my_str.strip("it"))

# 统计字符串出现次数
new_my_str.count("it")

# 字符串长度
len(new_my_str)

max(ne_my_str)
min(str)
```
### 索引和切片

字符串的索引和切片操作跟列表、元组几乎没有区别，因为字符串也是一种有序序列，可以通过正向或反向的整数索引访问其中的元素。但是有一点需要注意，因为**字符串是不可变类型**，所以**不能通过索引运算修改字符串中的字符**。

```python
s = 'abc123456'
n = len(s)
print(s[0], s[-n])    # a a
print(s[n-1], s[-1])  # 6 6
print(s[2], s[-7])    # c c
print(s[5], s[-4])    # 3 3
print(s[2:5])         # c12
print(s[-7:-4])       # c12
print(s[2:])          # c123456
print(s[:2])          # ab
print(s[::2])         # ac246
print(s[::-1])        # 654321cba
```
需要再次提醒大家注意的是，在进行索引运算时，如果索引越界，会引发`IndexError`异常，错误提示信息为：`string index out of range`（字符串索引超出范围）。

### 字符的遍历

如果希望遍历字符串中的每个字符，可以使用`for-in`循环，有如下所示的两种方式。

方式一：

```python
s = 'hello'
for i in range(len(s)):
    print(s[i])
```
方式二：

```python
s = 'hello'
for elem in s:
    print(elem)
```
### 字符串长度

```python
len(s)
```
### 是否包含字串

`if "abc" in a:`

### 查找

如果想在一个字符串中从前向后查找有没有另外一个字符串，可以使用字符串的`find`或`index`方法。在使用`find`和`index`方法时还可以通过方法的参数来指定查找的范围，也就是查找不必从索引为`0`的位置开始。

```python
s = 'hello, world!'
print(s.find('or'))      # 8
print(s.find('or', 9))   # -1
print(s.find('of'))      # -1
print(s.index('or'))     # 8
print(s.index('or', 9))  # ValueError: substring not found
```
**说明**：`find`方法找不到指定的字符串会返回`-1`，`index`方法找不到指定的字符串会引发`ValueError`错误。

`find`和`index`方法还有逆向查找（从后向前查找）的版本，分别是`rfind`和`rindex`，代码如下所示。

```python
s = 'hello world!'
print(s.find('o'))       # 4
print(s.rfind('o'))      # 7
print(s.rindex('o'))     # 7
# print(s.rindex('o', 8))  # ValueError: substring not found
```
### 性质判断

可以通过字符串的`startswith`、`endswith`来判断字符串是否以某个字符串开头和结尾；还可以用`is`开头的方法判断字符串的特征，这些方法都返回布尔值，代码如下所示。

`isupper`是否包含大写字母

```python
s1 = 'hello, world!'
print(s1.startswith('He'))   # False
print(s1.startswith('hel'))  # True
print(s1.endswith('!'))      # True
s2 = 'abc123456'
print(s2.isdigit())  # False
print(s2.isalpha())  # False
print(s2.isalnum())  # True

# 是否包含大写字母
print(s2.isupper())  # False
```
**说明**：上面的`isdigit`用来判断字符串是不是完全由数字构成的，`isalpha`用来判断字符串是不是完全由字母构成的，这里的字母指的是 Unicode 字符但不包含 Emoji 字符，`isalnum`用来判断字符串是不是由字母和数字构成的。

### 大小写处理

#### 字符串首字母大写

```python
s1 = 'hello, world!'
# 字符串首字母大写
print(s1.capitalize())  # Hello, world!
```
#### 首字母大写

```python
name = "ada lovelace"
print(name.title())
# Ada Lovelace
```
#### 大写转小写

```python
name = "Ada Lovelace"
print(name.lower())
```
#### 小写转大写

```python
name = "Ada LoveLace"
print(name.upper())
```
### 替换操作

如果希望用新的内容替换字符串中指定的内容，可以使用`replace`方法，代码如下所示。`replace`方法的第一个参数是被替换的内容，第二个参数是替换后的内容，还可以通过第三个参数指定替换的次数。

```python
s = 'hello, good world'
print(s.replace('o', '@'))     # hell@, g@@d w@rld
print(s.replace('o', '@', 1))  # hell@, good world
```
### 拆分与合并

可以使用字符串的`split`方法将一个字符串拆分为多个字符串（放在一个列表中），也可以使用字符串的`join`方法将列表中的多个字符串连接成一个字符串，代码如下所示。

```python
s = 'I love you'
words = s.split()
print(words)            # ['I', 'love', 'you']
print('~'.join(words))  # I~love~you
```
需要说明的是，`split`方法默认使用空格进行拆分，我们也可以指定其他的字符来拆分字符串，而且还可以指定最大拆分次数来控制拆分的效果，代码如下所示。

```python
s = 'I#love#you#so#much'
words = s.split('#')
print(words)  # ['I', 'love', 'you', 'so', 'much']
words = s.split('#', 2)
print(words)  # ['I', 'love', 'you#so#much']
```
### 修剪

```python
favorite_language = 'python '
favorite_language.rstrip()
# 'python'
favorite_language
# 'python '

# 去掉开头的空白
favorite_language.lstrip()

# 去掉两端的空白
favorite_language.strip()
```
### 拼接和重复

字符串可以用+运算符连接在一起，用\*运算符重复。

```python
print("h"*3 + "qw") # hhhqw
```
### 格式化

#### %

%表示占位，s表示类型转换为字符串放入字符串

```python
name = "hello"
message = "%s world!" % name
print(message) # hello world!

int1 = 1
int2 = 2
print("the num is: %s %s" % (int1, int2)) # the num is: 1 2
```
%d表示整型占位

%f表示浮点占位

```python
# 这种方式不关注类型
set_up_year = 2006
stock_price = 19.99

print(f"今年：{set_up_year}, 股票价格：{stock_price}") # 今年：2006, 股票价格：19.99
```
#### 精度控制

m.n控制宽度和精度

* m宽度，如果m小于数字宽度，不会生效
* .n小数点精度，会进行小数四舍五入

%5d表示宽度控制5位，如数字11，变成[空格][空格][空格]11用三个空位补足宽度

%5.2f 表示宽度控制为5， 精度设为2。11.345 设置 %7.2f 结果： [空格][空格]11.35

#### format

我们也可以用字符串的`format`方法来完成字符串的格式，代码如下所示。

```python
a = 321
b = 123
print('{0} * {1} = {2}'.format(a, b, a * b))
```
从 Python 3.6 开始，格式化字符串还有更为简洁的书写方式，就是在字符串前加上`f`来格式化字符串，在这种以`f`打头的字符串中，`{变量名}`是一个占位符，会被变量对应的值将其替换掉，代码如下所示。

```python
a = 321
b = 123
print(f'{a} * {b} = {a * b}')

print(f'周长: {perimeter:.2f}')
print(f'面积: {area:.2f}')
```
如果需要进一步控制格式化语法中变量值的形式，可以参照下面的表格来进行字符串格式化操作。

|  |  |  |  |
| --- | --- | --- | --- |
| **变量值** | **占位符** | **格式化结果** | **说明** |
| `3.1415926` | `{:.2f}` | `'3.14'` | 保留小数点后两位 |
| `3.1415926` | `{:+.2f}` | `'+3.14'` | 带符号保留小数点后两位 |
| `-1` | `{:+.2f}` | `'-1.00'` | 带符号保留小数点后两位 |
| `3.1415926` | `{:.0f}` | `'3'` | 不带小数 |
| `123` | `{:0>10d}` | `'0000000123'` | 左边补`0`，补够10位 |
| `123` | `{:x<10d}` | `'123xxxxxxx'` | 右边补`x` ，补够10位 |
| `123` | `{:>10d}` | `' 123'` | 左边补空格，补够10位 |
| `123` | `{:<10d}` | `'123 '` | 右边补空格，补够10位 |
| `123456789` | `{:,}` | `'123,456,789'` | 逗号分隔格式 |
| `0.123` | `{:.2%}` | `'12.30%'` | 百分比格式 |
| `123456789` | `{:.2e}` | `'1.23e+08'` | 科学计数法格式 |

### 编码解码

通过字符串的`encode`方法，我们可以按照某种编码方式将字符串编码为字节串，我们也可以使用字节串的`decode`方法，将字节串解码为字符串

```python
a = "你好"

st1 = a.encode('utf-8')  # b'\xe4\xbd\xa0\xe5\xa5\xbd'
st1 = st1.decode('utf-8')
print(st1)
```
#

