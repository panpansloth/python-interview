### 单例模式
```python
def singleton(cls):
    instance = None

    def inner(*args, **kwargs):
        nonlocal instance
        if not instance:
            instance = cls(*args, **kwargs)
        return instance

    return inner
```
### 装饰器
    装饰器:本质上是一个Python函数,可以让其他函数在不需要做任何代码变动的前提下增加额外功能,装饰器的返回值也是一个函数对象.
    装饰器作用:增加额外功能,如打印日志,权限校验,性能测试,事务处理等.
    装饰器语法糖:@decorator
```python
from functools import wraps
from time import time


def record_time(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time()
        result = func(*args, **kwargs)
        print(f'{func.__name__}执行时间: {time() - start}s')
        return result

    return wrapper
```
### 迭代器和生成器
    迭代器:实现了__iter__和__next__方法的对象.
    生成器:是一种特殊的迭代器,可以通过yield关键字实现.
```python
# 生成器实现斐波那契数列
def fib(num):
    a, b = 0, 1
    for _ in range(num):
        a, b = b, a + b
        yield a


# 迭代器实现斐波那契数列
class Fib:
    def __init__(self, num):
        self.num = num
        self.a, self.b = 0, 1
        self.idx = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.idx < self.num:
            self.a, self.b = self.b, self.a + self.b
            self.idx += 1
            return self.a
        raise StopIteration()
```
### 内存管理
Python内存管理的三个关键点: 引用计数|标记清理|分代回收

    引用计数:指对象被引用的次数,每当引用一次,对象的引用计数就会增加1,当对象的引用计数为0时,对象就会被回收.

        引用计数增加的情况:
        1.对象被创建,例如a=23
        2.对象被引用,例如b=a
        3.将其放入一个容器中,例如list1=[a,a]
        4.作为参数传递给函数,例如func(a)
        
        引用计数减少的情况:
        1.用del语句删除对象引用,例如del a
        2.对象引用被重新赋值,例如a=24
        3.对象离开其作用域,例如func函数执行完毕时,func函数中的局部变量(全局变量不会)将被销毁
        4.容器被销毁(del list1)或者容器删除该对象

    标记-清理:解决容器类型可能产生的循环引用问题
        标记阶段:遍历所有对象,将所有活动对象打上标记
        清理阶段:清理所有没有标记的对象

    分代回收:在循环引用的情况下,标记-清理算法效率低下,因此Python提供了分代回收机制.
        Python将所有对象分为三代,分别是年轻代(0代),中年代(1代),老年代(2代)
        基本思想:对象存在的时间越长,是垃圾的可能性就越小,尽量不对这样的对象进行垃圾回收.
### __init__和__new__方法
    __new__方法:创建对象时调用,返回当前对象的一个实例,必须返回当前类的一个实例,否则对象创建不成功.
    __init__方法:创建完对象后调用,对当前对象的实例的一些初始化操作,无返回值.
```python
class A:
    def __new__(cls, *args, **kwargs):
        print('调用__new__方法')
        return super().__new__(cls)

    def __init__(self):
        print('调用__init__方法')
```
### *arg和**kwargs
    *arg(可变参数):将参数打包成tuple给函数体调用
    **kwargs(关键字参数):将参数打包成dict给函数体调用
### 列表去重后保持原来的顺序
```python
def del_repeat_element(l):
    """
    删除列表中重复元素的函数(去重后元素相对位置保持不变)
    :param l: 列表
    :return: 去重后的列表
    """
    seen = set()
    for item in l:
        if item not in seen:
            yield item
            seen.add(item)
```
### 列表中每个数字出现的次数
```python
def count_num(items):
    hashtable = {}
    for item in items:
        if isinstance(item, (int, float)):
            hashtable[item] = hashtable.get(item, 0) + 1
    return hashtable
```
### 读取大文件
```python
def read_chunked(file, chunk_size=1024):
    with open(file, encoding='utf-8') as f:
        for chunk in iter(lambda: f.read(chunk_size), ''):
            yield chunk


if __name__ == '__main__':
    for chunk in read_chunked('iterview.md'):
        print(chunk)
```

