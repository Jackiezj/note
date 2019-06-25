# python基础

## 一. python简介

### 1.1 执行python的方式

- IDE执行 :  Pycharm
- 终端执行:  `python **.py` / `python3 **.py`
- 终端交互:  `python3 + enter`

### 1.2 python注释

- 单行注释

  ```python
  # 我是注释，可以在里写一些功能说明之类的哦
  ```

- 多行注释

  ```python
  '''
      我是多行注释，
      可以写很多很多行的功能说明
  '''
  ```

- python2中的中文支持

  > 在开头加上

  ```python
  # -*- coding:utf-8 -*-
  ```

## 二. 变量及数据类型

### 2.1 变量

- 变量定义

  ```python
  num1 = 100 # num1就是一个变量
  ```

  > 变量类型由python解释器自动判断

- 变量的类型获得

  > 使用type()函数

  ```python
  num1 = 100
  print(type(num1))
  ```

### 2.2 变量的数据类型

- 变量的类型

  | 变量类型 | 说明       | 备注     |
  | -------- | ---------- | -------- |
  | int      | 有符号整型 | 数字型   |
  | long     | 长整型     | 数字型   |
  | float    | 浮点型     | 数字型   |
  | complex  | 复数型     | 数字型   |
  | bool     | 布尔型     | 数字型   |
  | str      | 字符串     | 非数字型 |
  | list     | 列表       | 非数字型 |
  | tupel    | 元组       | 非数字型 |
  | dict     | 字典       | 非数字型 |

### 2.3 数据类型转换

- 示例

  ```python
  int('123')
  float('1.2')
  str(123)
  list((1,))
  tuple([1])
  ```

### 2.4 变量是否可变

- 不可变类型: 数字, 字符串, 元组
- 可变类型: 列表, 字典, 集合

## 三. 输入与输出

### 3.1 输出

- 输出及格式化输出

  ```python
  # 普通输出
  print("hello world")
  
  # 格式化输出整型
  num1 = 1
  print("num1=%d" % num1)  # 1
  print("num1=%06d" % num1)  # 000001
  
  # 格式化输出浮点型
  num2 = 2.2
  print("num2=%f" % num2)  # 2.2
  print("num2=%.3f" % num2)  # 2.200
  
  # 格式化输出字符串
  str1 = 'hello'
  print("str=%s" % str1)
  
  # 格式化输出%
  print("%%")
  ```

### 3.2 输入

- 输入

  ```python
  password = input("请输入内容")
  print(password)  # 打印输出的内容
  ```

## 四. 运算符

### 4.1 算术运算符

- 示例

  | 运算符  | 描述     |
  | ------- | -------- |
  | + - * / | 加减乘除 |
  | //      | 取整     |
  | %       | 取余     |
  | **      | 指数     |

### 4.2 赋值运算符

- `=, +=, -=, *=, /=, //=, %=, **=`

### 4.3 比较运算符

- `==, !=, >, <, >=, <=`

- `0 < 9 < A < Z < a < z`

### 4.4 逻辑运算符

- `and, or, not`

## 五. 条件与循环

### 5.1 `if`语句

- demo

  ```python
  if age >= 18:
      print("我已经成年了")
  elif age <= 3:
      print("是婴儿")
  else:
  	print("未成年")
  ```

- 简写

  ```python
  print("YES") if age >= 18 else print("NO")
  ```

### 5.2 循环语句

- while循环

  ```python
  i = 0
  while i < 5:
      print("i=%d" % i)
      i+=1
  ```

- for循环

  ```python
  name = 'zhangsan'
  for x in name:
      print(x)
      
  for i in range(5):
      print(i)
  ```

  > for循环, 循环对象是一个可迭代对象 :  str, list, tuple, dict, 列表生成式, 推导式

- break, continue

  - break

    结束循环体

  - continue

    结束本次循环（只结束这一次）

## 六. 高级变量类型

### 6.1 字符串(str)

- 定义

  ```python
  str1 = "hello"
  str2 = 'hello'
  str3 = '''定义一个可换行的
  		  字符串
  	   '''
  # """"""用于函数类的注释
  ```

- 方法

  > 方法中所有的区间均为 [start, end) 左闭右开
  >
  > 字符串为不可变类型, 对其操作后开辟新的内存空间, 不改变原来str的值

  - 切片 --> 得到新str

    > `str[start:end:len]`
    >
    > 区间为 [start, end) 左闭右开

    ```python
    name = 'zhangsan'
    name[0:3]  # 'zha'
    name[2:]  # 'angsan'
    name[:2]  # 'zh'
    name[1:-1]  # 'hangsa'
    name[::-1]  # 'nasgnahz'
    ```

  - 查找替换

    `find` --> `find(sub, start=None, end=None)` 查找:   返回值: sub对应索引或-1 `rfind`从右边开始查找

    `replace` --> `replace(old, new, count=None)`  替换:   返回值: 新**引用**对象

    `strip, lsrip, rstrip`  --> `lstrip(), lstrip()` 删除两端空白, 左右空白

    ```python
    name = 'zhangsan'
    name.find('an')
    name.find('an', 0, 4)
    name.replace('an', a, 2)  # zhagsa
    name = "  zhangsan  "
    name.lstrip().rstrip()  # "zhangsan"
    ```

  - 统计次数

    `count` --> `count(sub, start=None, end=None)`

    ```python
    name.count('an', 'a')
    ```

  - 分割

    `split`  --> `split(sep=None, maxsplit=-1)` 默认按`" "`全部分割

    ```python
    name = 'zhang san he'
    name2 = name.split()  # name2: ['zhang', 'san', 'he']
    name3 = name.split(" ",2)  # name3: ['zhang', 'san he']
    ```

  - 大小写转换

    `capitalize` --> `capitalize()` 小写-->大写(全部)

    `lower` --> `lower()` 大写 --> 小写(全部)

    `title` --> `title()` 标题模式 

    ```python
    name.capitalize()
    name.lower()
    name.title()
    ```

### 6.2 列表

- 列表定义

  ```python
  num_list = [1, 2, 3, 4]
  ```

- CRUD

  - 添加元素

    `append` --> `append(p_object)` 添加一个元素, 可以是任何对象(引用)

    `extend` --> `extend(iterable)`

    ```python
    num_list.append(5)
    num_list.extend([6, 7])
    ```

  - 修改元素

    下标修改元素

    ```python
    num_list[0] = 100
    ```

  - 查找元素

    `in`, `not in` --> 返回bool

    `index`, `count` 

    ```python
    if 1 in num_list:
        print(1)
    num_list.count(1)
    num_list.index(1)
    ```

  - 删除元素

    `del` --> `del object`  eg :  `del list[0]`

    `pop` --> `pop()`   删除最后一个元素

    `remove` --> `remove(value)`  删除指定元素

    ```python
    del num_list[0]
    num_list.pop()
    num_list.remove(7)
    ```

  - 排序

    `sort` --> `sort(key=None, reverse=False)`

    ```python
    num_list.sort(reverse=True)  # 倒序排列
    ```

    > 对于`[{'a': 1}, {'a': 2}]`, 按a键的值排序时, 可指定key为a键的值`lambda x: x['a']`

  - 去重实例

    > 核心 :  元素第一次index与迭代次数不相同则删除
    >
    > `enumerate(iterable)`可将一个iterable转换为一个*索引序列*

    ```python
    def clear_repeat(num_list1):
        num_list2 = []
        for i, num in enumerate(num_list1):
            if num_list1.index(num) == i:
                num_list2.append(num)
        return num_list2
    ```

    方法二

    ```python
    def clear_repeat(old_list):
        return list(set(old_list))
    ```

    > 问题 : 如果是数字会自动排序

### 6.3 元组

- 定义

  ```python
  tuple1 = (1,)
  tuple2 = (1, 2)
  ```

- 方法

  > 元组为不可变数据类型, 只有查找方法

  `下标获取元素`

  `count, index` --> 与列表相同

  `列表和元组转换`

  ```python
  tuple([1, 2])
  list((1, 2))
  ```

### 6.4 字典

- 定义

  ```python
  info = {'name': 'zhangsan', 'id': 100}
  ```

- CRUD

  > 对字典的操作就是根据Key操作Value

  - 增加

    ```python
    info['age'] = 18
    ```

  - 修改

    ```python
    info['age'] = 20
    ```

  - 查询

    `info[kdy]`  不存在报错

    `info.get(key, default=None)`  不存在,返回默认值

    ```python
    info['age']  # 不存在报错
    info.get('age', 22)
    ```

  - 删除

    `pop`

    `del`

    `clear` 清空整个字典

    ```python
    info.pop('age')  # 返回对应值
    del info['name']
    info.clear()
    ```

  - 其它操作

    ```python
    len(info)  # 返回字典长度
    # 用于遍历
    info.keys()  # 返回字典key的列表
    info.values()  # 返回字典value的列表
    info.items()  # 返回(K, V)元组的列表
    ```

### 6.5 公共方法

- `+, *, in, not in`

  > +, * 不适合于字典

  ```python
  [1, 2] + [3, 4]  # 合并 [1, 2, 3, 4]
  [1, 2] * 2  # 复制 [1, 2, 1, 2]
  3 in (1, 2, 3)  # True
  'name' not in {'name': 1, 'age': 1}  # False
  ```

- python内置函数

  ```python
  len(item)
  max(item)
  min(item)
  del(item)
  ```

- 切片

  请看str切片部分

## 七. 函数

### 7.1 函数定义和调用

- 定义和调用

  ```python
  def fun():  # 定义
      """函数文档说明"""
      pass
  fun()  # 调用
  ```

### 7.2 函数的参数与返回值

- 函数参返类型
  - 有参无返

    ```python
    def fun(a, b):
        print(a + b)
    ```

  - 无参有返

    ```python
    num1 = 1
    def fun():
        return num1
    ```

  - 无参无返

    ```python
    def fun():
        pass
    ```

  - 有参有返

    ```python
    def fun(a, b):
        return a + b
    ```

- 函数参数

  - python中参数全部为引用传递,

    - 函数内对可变类型数据修改引用指向的数据
    - 函数内对不可变类型数据修改对原参数无影响
    - 本质 :  是否开辟新内存空间

  - 缺省参数

    ```python
    def fun(name, age=None):
        pass
    
    fun('zhangsan')
    fun('zhangsan', '18')
    fun('zhangsan', age='18')
    fun(name='zhangsan', age='18')
    ```

    > 注意缺省参数**必须写在后面**

  - 不定长参数

    ```python
    def fun(name, *args, **kwargs):
        print(args, kwargs)
        
    fun('zhangsan', 12, 'id', 'age': 18)  # (12, 'id', {'age': 18}) {}
    fun('zhangsan', (1, 'id'), {'age': 18})  # ((1, 'id'), {'age': 18}) {}
    fun('zhangsan', *(1, 'id'), **{'age': 18})  # (1, 'id') {'age': 18}
    fun('zhangsan', 1, *(1, 'id'), **{'age': 18})  # (1, 1, 'id') {'age': 18}
    ```

    > 第一二种方式 :  *args对所有参数自动装箱
    >
    > 如果同时传递元组和字典用第三种方式

### 7.3 函数返回值

- return返回函数返回值
- return函数结束,不再向下执行
- return返回多个返回值时,自动装箱为元组
- 接收元组可自动拆箱, 如: name, age = fun()

### 7.4 局部变量和全局变量

- 局部变量

  定义在函数内部, 外部不能访问

- 全局变量

  定义在函数外

- 修改全局变量的值

  > 局部变量和全局变量重名时, 优先使用局部变量

  ```python
  global a, b
  ```

### 7.5 递归函数

- 递归原理: 函数反复调用自身

- 实例:  n阶乘 n!

  ```python
  def fun(n):
      if n != 1:
      	result = n * fun(n-1)
      else:
          return 1
      return result
  ```

### 7.6 匿名函数

- 定义

  ```python
  lambda [arg1 [,arg2,.....argn]]:expression
  ```

- 使用

  字典排序

  ```python
  stus = [
      {"name": "zhangsan", "age": 18}, 
      {"name": "lisi", "age": 19}, 
      {"name": "wangwu", "age": 17}
  ]
  stus.sort(key = lambda x: x['name'])
  ```

## 八. 文件操作

### 8.1 文件的打开与关闭

- 示例

  ```python
  with open('xxx.txt', 'w') as f:
      pass
  ```

  > 打开模式有: `w, r, a, wb, rb, ab`
  >
  > b表示二进制方式
  >
  > 模式后加`+` 表示读写都可,如`rb+`, 一般不用

### 8.2 文件的读写

- 读

  ```python
  with open('xxx.txt', 'r') as f:
      f.read(n)  # n表示读取长度, 不写表示全部读取
      f.readline()  # 一次读取一行
      f.readlines()  # 读取全部, 返回行为元素的列表
  ```

- 写

  ```python
  with open('xxx.txt', 'w') as f:
      f.write('hello')
  ```

### 8.3 操作文件系统

- 示例

  ```python
  import os
  
  # 重命名
  os.rename('xxx.txt', 'yyy.txt')
  # 删除文件
  os.remove('xxx.txt')
  # 创建文件夹
  os.mkdir('demo')
  # 获取当前目录
  os.getcwd()
  # 改变默认目录
  os.chdir('../')
  # 获取目录列表
  os.listdr('./')
  # 删除文件夹
  os.rmdir('demo')
  # 判断是否是文件夹
  os.path.isdir(path)
  os.path.isfile(path)
  ```

## 九. 异常

### 9.1 异常捕获

- 捕获异常

  ```python
  try:
      f = open('123.txt', 'r')
  except IOErrot as e
  	print(e)
  ```

- 捕获多个异常

  ```python
  try:
      f = open('123.txt', 'r')
      a = 1 / 0
  except (IOError, Exception) as e
  	print(e)
  ```

- else 与 finally

  ```python
  try:
      f = open('123.txt', 'r')
      a = 1 / 0
  except (IOError, Exception) as e
  	print(e)
  else:
      print('没有捕获到异常被执行')
  finally:
      f.close()
      print('有没有异常都要执行')
  ```

### 9.2 异常的传递

​	如果没有处理异常, 则异常会抛给调用者

### 9.3 抛出自定义异常

- 自定义异常是`Exception`的子类

- 用raise语句可抛出异常

  ```python
  class ShortInputException(Exception):
      '''自定义的异常类'''
      def __init__(self, length, atleast):
          super().__init__()
          self.length = length
          self.atleast = atleast
  
  def main():
      try:
          s = input('请输入 --> ')
          if len(s) < 3:
              # raise引发一个你定义的异常
              raise ShortInputException(len(s), 3)
      except ShortInputException as result:#x这个变量被绑定到了错误的实例
          print('ShortInputException: 输入的长度是 %d,长度至少应是 %d'% (result.length, result.atleast))
      else:
          print('没有异常发生.')
  main()
  ```

## 十. 模块与包

### 10.1 导入模块

- 导入方式

  ```python
  import math  # 导入模块名
  from math import sqrt  # 导入模块中的函数或变量
  from re import *  # 导入模块中所有功能
  from time import sleep as sp  # 给导入的内容起别名
  ```

- `__all__`

  ```python
  __all__ = ['test', 'Test']  # 变量中的元素，不会被from xxx import *时导入
  ```

### 10.2 包

- 包将有联系的模块组织在一起，即放到同一个文件夹下，并且在这个文件夹创建一个名字为`__init__.py` 文件，那么这个文件夹就称之为`包`
- 有效避免模块名称冲突问题，让应用组织结构更加清晰

- `__init__.py`
  - `__init__.py`为空 ： 仅仅是把这个包导入，不会导入包中的模块
  - `__all__`  ： 控制着 from 包名 import *时导入的模块
  - 可以在这个文件中编写语句，当导入时，这些语句就会被执行

## 十一. 其它

### 1.GIL(全局解释器锁)

面试题：

- 问题：描述Python GIL的概念， 以及它对python多线程的影响？编写一个多线程抓取网页的程序，并阐明多线程抓取程序是否可比单线程性能有提升，并解释原因

答案：

- Python语言和GIL没有半毛钱关系。仅仅是由于历史原因在Cpython虚拟机(解释器)，难以移除GIL。
- GIL：全局解释器锁。每个线程在执行的过程都需要先获取GIL，保证同一时刻只有一个线程可以执行代码。
- 线程释放GIL锁的情况： 在IO操作等可能会引起阻塞的system call之前,可以暂时释放GIL,但在执行完毕后,必须重新获取GIL Python 3.x使用计时器（执行时间达到阈值后，当前线程释放GIL）或Python 2.x，tickets计数达到100
- Python使用多进程是可以利用多核的CPU资源的。
- 多线程爬取比单线程性能有提升，因为遇到IO阻塞会自动释放GIL锁

面试题：

- 有了GIL为什么还要有互斥锁

答案：

- 互斥锁保证一行代码执行完成切换线程，GIL可能会在一行代码未执行完成就切换线程
- 如：a = a + 1 互斥锁a = a+1执行完成后切换线程，GIL可能只执行a+1就切换了，从而导致数据不准确

### 2.深拷贝和浅拷贝

> 赋值不会copy, 只是指向同一引用, id相同

- 浅拷贝

  > 浅拷贝是对于一个对象的顶层拷贝 --> 拷贝引用,不拷贝内容

- 深拷贝

  > 深拷贝是对于一个对象所有层次的拷贝(递归)

- copy.copy()和copy.deepcopy()

  > copy.copy对于可变类型，会进行浅拷贝
  >
  > copy.copy对于不可变类型，不会拷贝，仅仅是指向其引用

```python
import copy
list0 = [1]
list1 = [list0, 2]
list2 = copy.copy(list1)  # copy  id(list1)!=id(list2) 但 id(list1[0]) == id(list2[0])
list3 = copy.deepcopy(list1)  # deepcopy  id(list1)!=id(list2) 且 id(list1[0]) != id(list2[0])

list = [1, 2]
copy_list = copy.copy(list)
list2 = list
# 此处 id(list)==id(list2)!=id(copy_list), 若list中还有list,则内部list的三个id值相同
list_id, list2_id, copy_list_id = id(list), id(list2), id(copy_list)

num = 1
num2 = num
copy_num = copy.copy(num)
# 三个id值相同
num_id, num2_id, copy_num_id = id(num), id(num2), id(copy_num)

```

- 应用

```python
import copy
def test(temp):
    temp.append(44)
nums = [1, 2, 3]
test.(copy.deepcopy(nums))  # 应用deepcopy能够在不改变nums值的条件下使用nums
```

### 3.私有化

- `xx`: 公有
- `_xx`: 私有化属性和方法，from somemodule import * 禁止导入,类对象和子类可以访问
- `__xx`: 私有化属性和方法，无法在外部直接访问
- `__xx__`: 公有，魔法方法或属性，不要自己发明

> 私有属性方法的获得：`_Class__object`  一般不要使用
>
> 如：Student类中的`__name`属性  可以通过`_Student__name`获得

### 4.import注意点

- import搜索路径

```Python
import sys
sys.path  # 显示要导入模块的查找路径
```

- 程序执行时添加新的模块路径

```Python
sys.path.append("/home/itcast/xxx")
sys.path.insert(0, "/home/itcast/xxx")  # 可以确保先搜索这个路径
```

- 重新导入模块

> 执行过程过模块变化了，可以重新导入

```python
from imp import reload
reload(reload_test)  # 重新导入reload_test模块
```

### 5.多继承

1) super().方法 不一定就是父类方法，而是会按照 类.__mro__ 属性中的顺序，执行下一个被继承类的方法

- 总结：
  - 单继承：用super().method
  - 多继承：用super(类, self).method

2) 继承不是复制，类属性只是对父类属性的引用

### 6.property

> 用来操作私有属性，在方法内部可以设置修改权限

```Python
class Person(objetct):
    def __init__(self, age):
        self.__age = age

    @property  # get方法
    def age(self):
        print("有权限")
        return self.__age

    @age.setter  # set方法
    def age(self, new_age):
        self.__age = new_age


xiaoming = Person(18)
print(xiaoming.age)  # 获取__age属性
xiaoming.age = 50  # 设置__age为50
```

### 7.range  生成式/推导式  生成器

```python
# range使用
for i in range(1, 10, 2):  # range(m, n, 步长)
    print(i)  # 1 3 5 7 9
# 列表生成式
list1 = [i**2 for i in range(0, 100) if i % 2 ==0 ]
# 列表生成器
list1 = (i**2 for i in range(0, 100) if i % 2 ==0 )
# 生成器
def fib(num):
    temp = yield num  # 使用了yield关键字的函数不再是函数，而是生成器
G = fib(3)  # G为一个生成器
next(G)
G.send(None)  # 与等价next(G)
temp = G.send("python")	 # temp接收下次c.send("python")的python
```

### 8.迭代器

> `isinstance(对象, Iterable)`  :  判断是否为可迭代对象

- **迭代器本质**
  ​	一个具备了__iter__方法的对象，就是一个可迭代对象
  ​	一个实现了__iter__方法和__next__方法的对象，就是迭代器

- for...in...循环的本质

  ```python
  for item in Iterable  # Iterable可迭代对象
  	pass
  # 此处的item等价于:
  i = iter(Iterable)
  next(i)
  ```

  > iter(对象)返回该对象的迭代器
  > ​	next(迭代器)返回迭代器的下一条数据



### 9. 闭包

- 概念:

  函数内定义函数，且内函数用到外边函数的变量，将内函数及用到的变量称之为闭包

  简单说: 就是函数中的函数

- eg

  ```python
  def line_conf(a, b):
      def line(x):
          return a*x + b
      return line
  # 调用
  line_conf(1, 1)(5)
  ```

### 10. 装饰器

- 定义
  ​	@函数名 是python的一种语法糖
- 实例

```python
def out(fun):
	print("out")
    def inner():
        return fun()
    return inner

@out
def main():
    print("main")
		@out <==> out(main)

# 被装饰的函数有不定长参数
def timefun(func):
    def wrapped_func(*args, **kwargs):
        print("%s called at %s"%(func.__name__, ctime()))
        return func(*args, **kwargs)
    return wrapped_func
# 装饰器带参数
def timefun_arg(pre="hello"):
    def timefun(func):
        def wrapped_func():
            print("%s called at %s %s" % (func.__name__, ctime(), pre))
            return func()
        return wrapped_func
    return timefun
foo()==timefun_arg("itcast")(foo)()
```



















