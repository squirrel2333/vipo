列表可修改，**元组不可修改**。这就意味着元组类型的变量一旦定义，其中的元素不能再添加或删除，而且元素的值也不能修改。如果试图修改元组中的元素，将引发`TypeError`错误，导致程序崩溃。

元组也可以封装多个、不同类型的元素

## 简介

元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在小括号 () 里，元素之间用逗号隔开。

```python
#!/usr/bin/python3

tuple = ('abcd', 786 , 2.23, 'runoob', 70.2)
tinytuple = (123, 'runoob')

print (tuple)             # 输出完整元组
print (tuple[0])          # 输出元组的第一个元素
print (tuple[1:3])        # 输出从第二个元素开始到第三个元素
print (tuple[2:])         # 输出从第三个元素开始的所有元素
print (tinytuple * 2)     # 输出两次元组
print (tuple + tinytuple) # 连接元组
```

```python
>>> tup = (1, 2, 3, 4, 5, 6)
>>> print(tup[0])
1
>>> print(tup[1:5])
(2, 3, 4, 5)
>>> tup[0] = 11  # 修改元组元素的操作是非法的
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>>
```
* 虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。

```python
tup1 = ()    # 空元组
tup2 = (20,) # 一个元素，需要在元素后添加逗号
```
string、list 和 tuple 都属于 sequence（序列）。

**注意：**

* 1、与字符串一样，元组的元素不能修改。
* 2、元组也可以被索引和切片，方法一样。
* 3、注意构造包含 0 或 1 个元素的元组的特殊语法规则。
* 4、元组也可以使用+操作符进行拼接。

`**()**`**表示空元组，但是如果元组中只有一个元素，需要加上一个逗号，否则**`**()**`**就不是代表元组的字面量语法，而是改变运算优先级的圆括号，所以**`**('hello', )**`**和**`**(100, )**`**才是一元组，而**`**('hello')**`**和**`**(100)**`**只是字符串和整数。**

## 定义

```python
t1 = (1, "hello", True)
t2 = ()
t3 = tuple()
# 定义单个元素
t4 = ("hello", )
t5 = ((1,2,3), (4,5,6))
```
## 打包解包

当我们把多个用逗号分隔的值赋给一个变量时，多个值会打包成一个元组类型；当我们把一个元组赋值给多个变量时，元组会解包成多个值然后分别赋给对应的变量，如下面的代码所示。

```python
# 打包操作
a = 1, 10, 100
print(type(a))  # <class 'tuple'>
print(a)        # (1, 10, 100)
# 解包操作
i, j, k = a
print(i, j, k)  # 1 10 100
```
在解包时，如果解包出来的元素个数和变量个数不对应，会引发`ValueError`异常，错误信息为：`too many values to unpack`（解包的值太多）或`not enough values to unpack`（解包的值不足）。

```python
a = 1, 10, 100, 1000
# i, j, k = a             # ValueError: too many values to unpack (expected 3)
# i, j, k, l, m, n = a    # ValueError: not enough values to unpack (expected 6, got 4)
```
## 操作

```python
num = t5[0][1]

# 根据索引查找
t1.index(1)

nums = t1.count(1)

length = len(t1)

max(t1)
min(t1)
```
## 遍历

```python
index = 0

while index < len(t5):
    t5[index]
    index += 1

for ele in t5:
    print(ele)
```
* 元组中的元素不可重新赋值，但如果元素为例如list，tuple(list1,list2...)那tuple1[1][0]可以修改
