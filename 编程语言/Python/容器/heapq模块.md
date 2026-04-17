模块实现的是小根堆

插入删除数据时间复杂度O(logn)

获得最大最小元素时间复杂度O(1)

# 创建堆

```python
import heapq

# 创建空堆
heap = []

# 从现有列表创建堆（原地转换）
data = [3, 1, 4, 1, 5, 9, 2, 6]
heapq.heapify(data)  # O(n)时间复杂度
print(data)  # 输出: [1, 1, 2, 3, 5, 9, 4, 6]
```
# 添加元素

```python
heap = []
heapq.heappush(heap, 5)  # 添加元素
heapq.heappush(heap, 2)
heapq.heappush(heap, 10)
heapq.heappush(heap, 1)

print(heap)  # 输出: [1, 2, 10, 5]
```
# 弹出最小元素

```python
min_val = heapq.heappop(heap)
print(min_val)  # 输出: 1
print(heap)     # 输出: [2, 5, 10]

# 只查看不弹出
print(heap[0])
```
# 组合操作

```python
# 先添加后弹出（比分别调用高效）
val = heapq.heappushpop(heap, 3)
print(val)  # 输出: 2
print(heap) # 输出: [3, 5, 10]

# 先弹出后添加（堆必须非空）
val = heapq.heapreplace(heap, 0)
print(val)  # 输出: 3
print(heap) # 输出: [0, 5, 10]
```
# 查找极值

```python
data = [3, 1, 4, 1, 5, 9, 2, 6]

# 查找最小的3个元素
smallest = heapq.nsmallest(3, data)
print(smallest)  # 输出: [1, 1, 2]

# 查找最大的3个元素
largest = heapq.nlargest(3, data)
print(largest)   # 输出: [9, 6, 5]
```
# 合并有序序列

```python
a = [1, 3, 5, 7]
b = [2, 4, 6, 8]
c = [0, 9, 10]

merged = heapq.merge(a, b, c)
print(list(merged))
```
# 自定义对象排序

```python
"""
从列表中找出最大的或最小的N个元素
堆结构(大根堆/小根堆)
"""
import heapq

list1 = [34, 25, 12, 99, 87, 63, 58, 78, 88, 92]
list2 = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
print(heapq.nlargest(3, list1))
print(heapq.nsmallest(3, list1))
print(heapq.nlargest(2, list2, key=lambda x: x['price']))
print(heapq.nlargest(2, list2, key=lambda x: x['shares']))
```
