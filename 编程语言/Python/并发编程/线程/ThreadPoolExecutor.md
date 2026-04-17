## 线程池基本概念

### 什么是线程池

线程池是一种多线程处理形式，它预先创建一组线程并放入池中，当有任务需要执行时，从池中取出空闲线程来执行任务，任务完成后线程返回池中等待下一次任务。

### 为什么使用线程池

* **减少线程创建销毁的开销**：线程的创建和销毁需要消耗系统资源
* **控制并发数量**：避免无限制创建线程导致系统资源耗尽
* **提高响应速度**：任务到达时可以直接使用现有线程，无需等待线程创建
* **统一管理**：提供统一的线程分配、调度和监控机制

## Python 中的线程池实现

### concurrent.futures 模块

Python 3.2+ 引入了 `concurrent.futures`模块，提供了高级的线程池接口。

```python
from concurrent.futures import ThreadPoolExecutor
import time
import threading

# 基本任务函数
def task(name, duration):
    print(f"{name} 开始执行，线程ID: {threading.get_ident()}")
    time.sleep(duration)
    return f"{name} 完成，耗时 {duration} 秒"
# 创建固定大小的线程池
with ThreadPoolExecutor(max_workers=4) as executor:
    # 提交任务
    future = executor.submit(task, "任务1", 2)
    # 获取结果
    result = future.result()
    print(result)
```
## 线程池的核心方法

### submit() 方法

提交单个任务，返回 Future 对象

```python
def use_submit():
    with ThreadPoolExecutor(max_workers=2) as executor:
        # 提交多个任务
        future1 = executor.submit(task, "任务A", 3)
        future2 = executor.submit(task, "任务B", 1)
        future3 = executor.submit(task, "任务C", 2)

        # 按完成顺序获取结果
        for future in [future1, future2, future3]:
            print(future.result())
```
### map() 方法

批量提交任务，保持输入输出顺序

```python
def use_map():
    # 任务参数
    names = ["任务1", "任务2", "任务3", "任务4"]
    durations = [1, 2, 1, 3]

    with ThreadPoolExecutor(max_workers=3) as executor:
        # 使用 map 批量处理
        results = executor.map(task, names, durations)

        # 结果保持原始顺序
        for result in results:
            print(result)
```
### as\_completed() 方法

按完成顺序处理结果

```python
def use_as_completed():
    with ThreadPoolExecutor(max_workers=2) as executor:
        # 提交任务
        futures = [
            executor.submit(task, "快速任务", 1),
            executor.submit(task, "慢速任务", 3),
            executor.submit(task, "中速任务", 2)
        ]

        # 按完成顺序处理
        for future in concurrent.futures.as_completed(futures):
            print(f"完成: {future.result()}")
```
## 异常处理和超时控制

### 异常处理

```python
def task_with_error(name):
    if name == "错误任务":
        raise ValueError("故意错误")
    return f"{name} 成功"

def handle_exceptions():
    with ThreadPoolExecutor() as executor:
        futures = [
            executor.submit(task_with_error, "正常任务"),
            executor.submit(task_with_error, "错误任务")
        ]

        for future in concurrent.futures.as_completed(futures):
            try:
                result = future.result()
                print(f"成功: {result}")
            except Exception as e:
                print(f"任务失败: {e}")
```
### 超时控制

```python
def handle_timeout():
    def long_task():
        time.sleep(5)
        return "完成"

    with ThreadPoolExecutor() as executor:
        future = executor.submit(long_task)

        try:
            result = future.result(timeout=2)  # 2秒超时
            print(result)
        except concurrent.futures.TimeoutError:
            print("任务超时")
            future.cancel()  # 取消任务
```
## 回调函数和状态检查

### 回调函数

```python
def use_callbacks():
    def callback(future):
        try:
            result = future.result()
            print(f"回调收到: {result}")
        except Exception as e:
            print(f"回调错误: {e}")

    with ThreadPoolExecutor() as executor:
        future = executor.submit(task, "回调任务", 2).add_done_callback(callback)
        future.result()  # 等待完成
```
### 状态检查

```python
def check_status():
    with ThreadPoolExecutor() as executor:
        future = executor.submit(task, "状态任务", 2)

        print(f"是否完成: {future.done()}")
        print(f"是否取消: {future.cancelled()}")

        time.sleep(3)
        print(f"是否完成: {future.done()}")
```
## 线程池配置和优化

### 线程池大小选择

```python
import os

def choose_pool_size():
    cpu_count = os.cpu_count() or 1

    # I/O 密集型任务：较大线程池
    io_workers = min(32, cpu_count * 5)

    # CPU 密集型任务：较小线程池（建议用进程池）
    cpu_workers = cpu_count

    print(f"CPU核心: {cpu_count}")
    print(f"I/O推荐: {io_workers}")
    print(f"CPU推荐: {cpu_workers}")
```
### 自定义线程池配置

```python
def custom_thread_pool():
    executor = ThreadPoolExecutor(
        max_workers=4,
        thread_name_prefix='custom_pool',
        initializer=lambda: print("线程初始化"),
        initargs=()
    )

    try:
        future = executor.submit(task, "自定义任务", 1)
        print(future.result())
    finally:
        executor.shutdown(wait=True)  # 等待所有任务完成
```
