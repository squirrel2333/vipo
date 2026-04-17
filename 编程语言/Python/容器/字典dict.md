不支持下标索引

## 定义

```python
dict = {"name": "hqw", "age":18 }

my_dict = {}
my_dict2 = dict()

# 查询
score = my_dict["wlh"]

# 嵌套字典
stu_score = {
    "hhh": {
        "chinese" : 44,
        "englist" : 80
    },
    "kkk" : {
        "chinese" : 44,
        "englist" : 80
    }
}

hhh_chinese = stu_score["hhh"]["chinese"]

# 推导式生成
prices2 = {key: value for key, value in prices.items() if value > 100}
```
## 操作

```python
# 新增/更新
my_dict["ooo"] = 88
my_dict.update({'country': 'USA', 'profession': 'Engineer'})

# 删除
del my_dict['ooo']

# 取出并删除
score = my_dict.pop("ooo")
a = {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}.pop(4) # 16

my_dict.clear()

# 字典的全部key
my_dict.keys()

# 字典全部的value
my_dict.values()

len(my_dict)

max(my_dict) # 最大的key
min(my_dict)
```
## 遍历

不支持while

无序

```python
for key, value in user_0.items():
    print("\nKey: " + key)
    print("Value: " + value)
```

```python

keys = dict.keys()
for key in keys:
    print(f"key: {key}")
    print(f"value: {dict[key]}")

for key in dict:
    print(f"key: {key}")
    print(f"value: {dict[key]}")

# 按顺序遍历字典中的所有键
for name in sorted(keys):
    print(name)
```

```python
# 不会去重
for value in dict.values():
    print(value)

for value in set(dict.values()):
    print(value)
```

```python
infos = {
    "a" : {
        "salary" : 3000,
        "level" : 1
    },
    "b" : {
        "salary" : 4000,
        "level" : 1
    },
    "c" : {
        "salary" : 5000,
        "level" : 2
    },
    "d" : {
        "salary" : 6000,
        "level" : 3
    },
    "e" : {
        "salary" : 7000,
        "level" : 4
    }
}

for name in infos:
    if infos[name]["level"] == 1:
        info = infos[name]
        info["level"] += 1
        info["salary"] += 1000
        infos[name] = info

print(infos)
```
