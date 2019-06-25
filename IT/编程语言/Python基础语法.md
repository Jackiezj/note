# Python基础语法

# 1. Python必知必会

- 运行python程序

  ```bash
  python xxx.py
  python3 xxx.py
  python3  # 打开交互
  PyCharm  # 集成开发
  ```

- 注释

  ```python
  # 单行注释
  '''
  	多行注释
  '''
  """
  	函数或类等的说明
  """
  ```



# 2. python常用函数

## 2.1 变量相关方法

- **输入输出`print`和`input`**

  ```python
  content = input('请输入内容')
  print(content)
  ```

- **判断变量`type`**

  ```python
  type(num)
  ```

- **可变类型数据 内置函数**

  > str, list, tuple, dict

  ```python
  len(item)  # 返回长度
  max(item)  # 返回最大值
  min(item)  # 返回最小值
  del(item)  # 删除元素
  ```

- **字符串常用方法**

  ```python
  # 切片 (左闭右开)支持负数索引,支持步长
  name = 'zhang san'
  name[0:3]  # 'zha'
  name[2:]  # 'ang san'
  name[:2]  # 'zh'
  name[1:-1]  # 'hang sa'
  name[::-1]  # 'nas gnahz'
  
  
  # 查找
  index = name.find('an')  # 查找'an'第一次出现的索引值,没有返回-1
  index = name.find('an', 0, 4)  # 同上,查找范围为[0,4]
  
  # 替换
  new_str = name.replace('an', 'a')  # 替换:将字符串中所有'an'替换为'a'
  new_str = name.replace('an', 'a', 2)  # 替换2次
  
  # 去除前后空白
  name.strip()  # 去除两端空白
  name.lstrip().rstrip()  # 去除左右空白
  
  # 统计次数
  name.count('an')
  name.count('an', 0, 4)  # 限定统计范围
  
  # 分割(返回list)
  name_list = name.split()  # 默认按" "全部分割 ['zhang', 'san']
  name3 = name.split("a", 1)  # 分割指定次数 ['zh', 'ng san']
  
  # 大小写转换
  name.lower()
  name.upper()
  name.title()  # 首字母大写
  name.capitalize()  # 每个单词首字母大写
  ```

- **列表常用方法**

  ```python
  num_list = [1, 2, 3, 4]
  
  # 增加
  num_list.append(5)
  num_list.extend([6, 7])
  # 修改
  num_list[0] = 100
  # 查询
  1 in num_list  # 返回bool
  1 not in num_list
  num_list.count(1)
  num_list.index(1)
  # 删除
  del num_list[0]  # 删除指定索引元素
  num_list.pop()  # 删除最后一个元素
  num_list.remove(7)  # 删除第一个值为7的元素
  
  # 排序
  num_list.sort(reverse=True)  # 倒序排列
  
  # 去重
  def clear_repeat(num_list1):
      num_list2 = []
      for i, num in enumerate(num_list1):
          if num_list1.index(num) == i:
              num_list2.append(num)
      return num_list2
  # 去重方法二
  list(set(num_list))  # 如果为数字列表会自动排序
  ```

- **字典常用方法**

  ```python
  info = {'name': 'zhangsan', 'id': 100}
  
  # 增加
  info['age'] = 18
  # 修改
  info['age'] = 20
  # 查询
  info['age']  # 不存在报错
  info.get('age', 22)  # 不存在,返回默认值
  # 删除
  info.pop('age')  # 返回对应值
  del info['name']
  info.clear()
  
  # 用于遍历
  info.keys()  # 返回字典key的列表
  info.values()  # 返回字典value的列表
  info.items()  # 返回(K, V)元组的列表
  ```

- 可变类型数据 公共方法 `+, *, in, not in`

  > +, * 不适合于字典

  ```python
  [1, 2] + [3, 4]  # 合并 [1, 2, 3, 4]
  [1, 2] * 2  # 复制 [1, 2, 1, 2]
  3 in (1, 2, 3)  # True
  'name' not in {'name': 1, 'age': 1}  # False
  ```

  





# 3. 变量与数据类型

- **定义变量**

  ```python
  num1 = 100  # python对变量类型自动解析
  type(num1)  # type函数返回类型类型
  
  # int
  num = 100
  # float
  num = 1.1
  # bool
  name = True
  # str
  name = 'name'
  name = '''
  	定义可换行的字符串
  '''
  # list
  name = [1, 2]
  # tuple
  name = (1,)
  name = (1, 2)
  # dict
  name = {1, 2}
  ```

- **强制转换变量类型**

  ```python
  int('123')
  float('12.3')
  str(123)
  list((1,))
  tuple([1])
  ```

# 4. 运算符

- **算术运算符**

  ```python
  +, -, *, /, //(取整), %(取余), **(指数)
  ```

- **赋值运算符**

  ```python
  =, +=, -=, *=, /=, //=, %=, **=
  ```

- **逻辑运算符**

  ```python
  and, or, not
  ```

- **比较运算符**

  ```python
  ==, !=, >, <, >=, <=
  # 0 < 9 < A < Z < a < z
  ```

# 5. 条件与循环

- **条件**

  ```python
  if age >= 18:
      print("我已经成年了")
  elif age <= 3:
      print("是婴儿")
  else:
  	print("未成年")
  
  # 简写
  print("YES") if age >= 18 else print("NO")
  ```

- **循环**

  ```python
  # while循环
  i = 0
  while i < 5:
      print("i=%d" % i)
      i+=1
      
  # for循环
  # 循环对象是一个可迭代对象 :  str, list, tuple, dict, 列表生成式, 推导式
  name = 'zhangsan'
  for x in name:
      print(x)
      
  for i, x in enumerate(name):
      print(i, x)
      
  for i in range(5):
      print(i)
  ```

# 6. 函数

- **定义**

  ```python
  def fun(name, age=18, *args, **kwargs):
      """
      函数文档说明
      name 正常参数,必传
      age 缺省参数,不传会使用默认值,使用*args与**kwargs必传
      *args **kwargs 可变参数
      *args 自动将非命名参数装箱为元组
      **kwargs 自动将命名参数装箱为字典
      """
      print(name, age, args, kwargs)
      return (name, age)  # 同时返回多个值时用元组
  ```

- **调用**

  ```python
  # 赋值正常参数,缺省参数
  un('zs')  # 输出: zs
  fun('zs', 28)  # 输出: zs 28
  
  # *args, **kwargs 自动装箱 非命名参数装箱为元组,命名参数装箱为字典
  fun('zs', 28, 'lw')  # 输出: zs 28 ('lw',)
  fun('zs', age=28, 'lw', 'll', 'qq'=111, 'wx'=222)  
  	# 输出: zs 28 ('lw', 'll') {'qq':111, 'wx':222}
      
  # *args, **kwargs 自动拆箱再装箱 传入tuple,list,dict时,让*args与**kwargs自动拆箱再装箱
  fun(1, 1, *(2,), **{'qq':111})  # 1 1 (2,) {'qq': 111}
  	# 区别 fun(1, 1, (2,), {'qq':111}), 此时不会自动拆箱,结果为 1 1 ((2,), {'qq': 111}) {}
      
  # 返回值为元组时,接收返回值自动拆箱
  name, age = fun('zs')
  ```

- **局部变量与全局变量**

  > 局部变量: 定义在函数中,外部不能访问
  >
  > 全局变量: 定义在函数外
  >
  > 两者冲突时优先使用局部变量

  ```python
  # 函数内使用全局变量
  global name
  ```

- **递归函数**

  >  递归原理: 函数反复调用自身

  ```python
  # n! 递归示例
  def fun(n):
      if n == 1:
          return 1
      return n*fun(n-1)
  ```

# 7. IO操作

- **文件读写**

  > 文件打开模式: w, r, a, wb, rb, ab  (a表示追加,b表示二进制形式)

  ```python
  # 写
  with open('xxx.txt', 'w') as f:
      f.write('hello')
  # 追加
  with open('xxx.txt', 'a') as f:
      f.write('world')
  # 读
  # 可以多次读,f对象会记录读取位置,下次读取从记录处继续读取
  with open('xxx.txt', 'r') as f:
      f.read(n)  # n:读取长度, 默认全部读取
      f.readline()  # 一次读取一行
      f.readlines()  # 返回行元素的列表
  ```

- **操作文件系统**

  ```python
  import os
  
  """目录相关"""
  # 改变目录
  os.chdir('../')
  # 当前目录
  os.getcwd()
  # 当前目录列表
  os.listdr('./')
  
  """文件夹相关"""
  # 新建文件夹
  os.mkdir('demo')
  # 修改文件夹(重命名)
  os.rename('demo', 'demo1')
  # 删除文件夹
  os.rmdir('demo1')
  
  """文件相关"""
  # 新建文件
  open('xxx.txt','w')
  # 修改文件名(重命名)
  os.rename('xxx.txt', 'yyy.txt')
  # 删除文件
  os.remove('yyy.txt')
  
  """判断是否是文件夹"""
  os.path.isdir('./xxx.txt')
  os.path.isfile('./yyy')
  ```

# 8. 异常

- **异常的捕获与处理**

  ```python
  # 捕获异常
  try:
      f = open('123.txt', 'r')
  except IOErrot as e
  	print(e)
      
  # 一次捕获多个异常
  try:
      f = open('123.txt', 'r')
      a = 1 / 0
  except (IOError, Exception) as e
  	print(e)
      
  # 没有异常时的处理
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

- **异常的传递性**

  > 如果没有处理异常, 则异常会挪给调用者

- **主动抛出异常**

  ```python
  raise IOError
  ```

- **抛出自定义异常**

  ```python
  class ShortInputException(Exception):
      '''自定义的异常类'''
      def __init__(self, length):
          super().__init__()
          self.length = length
  
  def main():
      try:
          raise ShortInputException(3)
      except ShortInputException as result: # result这个变量被绑定到了错误的实例
          print('ShortInputException', result.length)
  
  if __name__ == '__main__':
      main()
  ```

# 9. 



























































