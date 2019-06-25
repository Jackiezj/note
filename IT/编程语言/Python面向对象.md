## 一、面向对象编程

- 面向对象编程(Object Oriented Programming-OOP)
- 面向过程：根据业务逻辑从上到下写代码
- 面向对象：将数据与函数绑定到一起，进行封装，这样能够更快速的开发程序，减少了重复代码的重写过程面向过程编程最易被初学者接受，其往往用一长段代码来实现指定功能，开发过程的思路是将数据与函数按照执行的逻辑顺序组织在一起，数据与函数分开考虑。

### 1. 类和对象

#### 1) 类

> 具有相同属性和行为事物的统称

- **类的构成**

  - 类的名称: 类名
  - 类的属性: 一组数据
  - 类的方法: 允许对进行操作的方法 (行为)

- **定义类**

  ```python
  class 类名:
      方法列表
  ```

  demo:

  ```python
  class Hero(object):  # 新式类定义形式
      def info(self):
          print("英雄各有见，何必问出处。")
  ```

  说明：

  - object 是Python 里所有类的最顶级父类；
  - 类名 的命名规则按照"大驼峰命名法"；
  - info 是一个实例方法，第一个参数一般是self，表示实例对象本身，当然了可以将self换为其它的名字，其作用是一个变量 这个变量指向了实例对象

#### 2) 对象

> 某一个具体事物的存在

- **创建对象**

  > python中，可以根据已经定义的类去创建出一个或多个对象。

  创建对象的格式为:

  ```python
    对象名1 = 类名()
    对象名2 = 类名()
    对象名3 = 类名()
  ```

  demo:

  ```python
  class Hero(object):  # 新式类定义形式
  	"""定义一个类"""
  	def info(self):
  		print(self) 
  
    taidamier = Hero()  # Hero这个类 实例化了一个对象  taidamier(泰达米尔)
    taidamier.info()  # 对象调用实例方法info()，. 表示选择属性或者方法
    
    print(taidamier)  # 打印对象，默认打印对象内存地址，等同于info里的print(self)
    print(id(taidamier))  # id(taidamier) 则是内存地址的十进制形式表示
  ```

  > info 是一个实例方法，类对象可以调用实例方法，实例方法第一个参数一定是self，当对象调用实例方法时，Python会自动将对象本身的引用做为参数，传递到实例方法的第一个参数self里

- **添加和获取对象的属性**

  ```python
  class Hero(object):
  	pass
  
  # 实例化了一个英雄对象 泰达米尔
  taidamier = Hero()
  
  # 给对象添加属性，以及对应的属性值
  taidamier.name = "泰达米尔"  # 姓名
  taidamier.hp = 2600  # 生命值
  taidamier.atk = 450  # 攻击力
  taidamier.armor = 200  # 护甲值
  
  # 通过.成员选择运算符，获取对象的属性值
  print("英雄 %s 的生命值 :%d" % (taidamier.name, taidamier.hp))
  print("英雄 %s 的攻击力 :%d" % (taidamier.name, taidamier.atk))
  print("英雄 %s 的护甲值 :%d" % (taidamier.name, taidamier.armor))
  ```

- **在方法内通过self获取对象属性**

  ```python
  class Hero(object):
      """定义了一个英雄类，可以移动和攻击"""
      def info(self):
          """在类的实例方法中，通过self获取该对象的属性"""
          print("英雄 %s 的生命值 :%d" % (self.name, self.hp))
          print("英雄 %s 的攻击力 :%d" % (self.name, self.atk))
          print("英雄 %s 的护甲值 :%d" % (self.name, self.armor))
  ```

### 2. 魔法方法

#### 1) `__init__()`方法

> 对象属性初始化 或 赋值 操作

```python
class Hero(object):
    # __init__()就是一个魔法方法，通常用来做属性初始化 或 赋值 操作。
    # 如果类面没有写__init__方法，Python会自动创建，但是不执行任何操作，
    # 如果为了能够在完成自己想要的功能，可以自己定义__init__方法，
    # 所以一个类里无论自己是否编写__init__方法 一定有__init__方法。

    def __init__(self):
        """ 方法，用来做变量初始化 或 赋值 操作，在类实例化对象的时候，会被自动调用"""
        self.name = "泰达米尔" # 姓名
        self.hp = 2600 # 生命值
        self.atk = 450  # 攻击力
        self.armor = 200  # 护甲值
```

说明：

- `__init__()`方法，在创建一个对象时默认被调用，不需要手动调用
- `__init__(self)`中的self参数，不需要开发者传递，python解释器会自动把当前的对象引用传递过去。

**有参数的`__init()`方法**

```python
class Hero(object):
    """定义了一个英雄类，可以移动和攻击"""

    def __init__(self, name, skill, hp, atk, armor):
        """ __init__() 方法，用来做变量初始化 或 赋值 操作"""
        # 英雄名
        self.name = name
        # 技能
        self.skill = skill
        # 生命值：
        self.hp = hp
        # 攻击力
        self.atk = atk
        # 护甲值
        self.armor = armor


# 实例化英雄对象时，参数会传递到对象的__init__()方法里
taidamier = Hero("泰达米尔", "旋风斩", 2600, 450, 200)
gailun = Hero("盖伦", "大宝剑", 4200, 260, 400)
```

#### 2) `__str__()`方法

> 该方法需要 return 一个数据，当在类的外部 print(对象) 则打印这个数据

```python
class Hero(object):
    def __init__(self, name, skill, hp, atk, armor):
        """ __init__() 方法，用来做变量初始化 或 赋值 操作"""
        # 英雄名
        self.name = name  # 实例变量
        # 技能
        self.skill = skill
        # 生命值：
        self.hp = hp   # 实例变量
        # 攻击力
        self.atk = atk
        # 护甲值
        self.armor = armor

        
    def __str__(self):
        return "英雄 <%s> 数据： 生命值 %d, 攻击力 %d, 护甲值 %d" % (self.name, self.hp, self.atk, self.armor)


taidamier = Hero("泰达米尔", "旋风斩", 2600, 450, 200)
gailun = Hero("盖伦", "大宝剑", 4200, 260, 400)

# 如果没有__str__ 则默认打印 对象在内存的地址。
# 当类的实例化对象 拥有 __str__ 方法后，那么打印对象则打印 __str__ 的返回值。
print(taidamier)
print(gailun)
```

#### 3) `__del__()`方法

> 创建对象后，python解释器默认调用`__init__()`方法；
>
> 当删除对象时，python解释器也会默认调用一个方法，这个方法为`__del__()`方法

```python
class Hero(object):
    # 当对象被删除时，会自动被调用
    def __del__(self):
        print("__del__方法被调用")
        print("%s 被 GM 干掉了..." % self.name)

taidamier = Hero()
# 删除对象,调用__del__方法
del(taidamier)
```

- 当有变量保存了一个对象的引用时，此对象的引用计数就会加1；
- 当使用del() 删除变量指向的对象时，则会减少对象的引用计数。如果对象的引用计数不为1，那么会让这个对象的引用计数减1，当对象的引用计数为0的时候，则对象才会被真正删除（内存被回收）。

继承介绍以及单继承

#### 4) `__new__`方法

- `__new__和__init__`的作用
  - `__new__`至少要有一个**参数cls**，代表要实例化的类，此参数在实例化时由Python解释器自动提供
  - `__new__`必须**要有返回值**，返回实例化出来的实例，这点在自己实现`__new__`时要特别注意，可以return父类`__new__`出来的实例，或者直接是object的`__new__`出来的实例
  - `__init__`有一个参数self，就是这个`__new__`返回的实例，`__init__`在`__new__`的基础上可以完成一些其它初始化的动作，`__init__`不需要返回值
  - 我们可以将类比作制造商，`__new__`方法就是前期的原材料购买环节，`__init__`方法就是在有原材料的基础上，加工，初始化商品环节

### 3. 继承

- 在程序中，继承描述的是多个类之间的所属关系。
- 如果一个类A里面的属性和方法可以复用，则可以通过继承的方式，传递到类B里。
- 那么类A就是基类，也叫做父类；类B就是派生类，也叫做子类。

```python
# 父类
class A(object):
    def __init__(self):
        self.num = 10

    def print_num(self):
        print(self.num + 10)
# 子类
class B(A):
    pass


b = B()
print(b.num) 
b.print_num()
```

#### 1) 单继承

> 子类只继承一个父类

```python
# 定义一个Master类
class Master(object):
    def __init__(self):
        self.kongfu = "古法煎饼果子配方" 

    def make_cake(self):
        print("按照 <%s> 制作了一份煎饼果子..." % self.kongfu)


class Prentice(Master): 
    """子类可以继承父类所有的属性和方法，哪怕子类没有自己的属性和方法，
    也可以使用父类的属性和方法。"""
    pass                
```

说明：

- 虽然子类没有定义`__init__`方法初始化属性，也没有定义实例方法，但是父类有。所以只要创建子类的对象，就默认执行了那个继承过来的`__init__`方法

#### 2) 多继承

> 子类继承多个父类

```python
class Master(object):
    def __init__(self):
        self.kongfu = "古法煎饼果子配方"  # 实例变量，属性

    def make_cake(self):                    # 实例方法，方法
        print("[古法] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)

    def dayandai(self):
        print("师傅的大烟袋..")

class School(object):
    def __init__(self):
        self.kongfu = "现代煎饼果子配方"

    def make_cake(self):
        print("[现代] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)

    def xiaoyandai(self):
        print("学校的小烟袋..")

class Prentice(Master, School):  # 多继承，继承了多个父类（Master在前）
    pass

# 子类的魔法属性__mro__决定了属性和方法的查找顺序
print(Prentice.__mro__)
```

说明：

- 多继承可以继承多个父类，也继承了所有父类的属性和方法
- 注意：如果多个父类中有同名的 属性和方法，则默认使用第一个父类的属性和方法（根据类的魔法属性**mro**的顺序来查找）
- 多个父类中，不重名的属性和方法，不会有任何影响。

#### 3) 子类重写父类的同名属性和方法

```python
class Master(object):
    def __init__(self):
        self.kongfu = "古法煎饼果子配方" 

    def make_cake(self): 
        print("[古法] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)


class School(object):
    def __init__(self):
        self.kongfu = "现代煎饼果子配方"

    def make_cake(self):
        print("[现代] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)


class Prentice(School, Master):  # 多继承，继承了多个父类
    def __init__(self):
        self.kongfu = "猫氏煎饼果子配方"

    def make_cake(self):
        print("[猫氏] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)


# 如果子类和父类的方法名和属性名相同，则默认使用子类的
# 叫 子类重写父类的同名方法和属性
damao = Prentice()
print(damao.kongfu) # 子类和父类有同名属性，则默认使用子类的
damao.make_cake() # 子类和父类有同名方法，则默认使用子类的

# 子类的魔法属性__mro__决定了属性和方法的查找顺序
print(Prentice.__mro__)
```

#### 4) 子类调用父类同名属性和方法

```python
class A(object):
    def __init__(self):
        self.attr = "a"

    def fun(self):
        print("A")


class B(object):
    def __init__(self):
        self.attr = "b"

    def fun(self):
        print("B")


class C(A, B):
    def __init__(self):
        super().__init__()  # 同时初始化父类的属性
        self.attr = "c"

    def fun(self):
        B.fun(self)  # 调用父类的方法
        print("C")


c = C()
print(c.attr)
c.fun()
```

核心点：

> 无论何时何地，self都表示是子类的对象。在调用父类方法时，通过传递self参数，来控制方法和属性的访问修改。

#### 5) super()的使用

```python
class A(object):
    def __init__(self):
        self.attra = "a"

    def fun(self):
        print("A")


class B(A):
    def __init__(self):
        super().__init__()
        self.attrb = "b"

    def fun(self):
        print("B")


class C(B):
    def __init__(self):
        super().__init__()
        self.attrc = "c"

    def fun(self):
        B.fun(self)
        print("C")


c = C()

print(c.attra)
print(c.attrb)
print(c.attrc)
c.fun()
# 结果为 a b c B C
# 如果继承关系改为 A --> object  B --> object  C --> A, B
# 则C的__init__方法中super().__init__()只能初始化到attrb，不能初始化到attra
```



知识点：

> 多层继承中：使用super() 可以逐一调用所有的父类方法，并且只执行一次。调用顺序遵循 **mro** 类属性的顺序。
>
> 多继承中：默认只执行第一个父类的同名方法

### 4. 私有

#### 1) 封装的意义：

1. 将属性和方法放到一起做为一个整体，然后通过实例化对象来处理；
2. 隐藏内部实现细节，只需要和对象及其属性和方法交互就可以了；
3. 对类的属性和方法增加 访问权限控制。

#### 2) 私有权限

> 在属性名和方法名 前面 加上两个下划线 __

> 1. 类的私有属性 和 私有方法，都不能通过对象直接访问，但是可以在本类内部访问；
> 2. 类的私有属性 和 私有方法，都不会被子类继承，子类也无法访问；
> 3. 私有属性 和 私有方法 往往用来处理类的内部事情，不通过对象处理，起到安全作用。

- Python中没有像C++中 public 和 private 这些关键字来区别公有属性和私有属性。

- Python是以属性命名方式来区分，如果在属性和方法名前面加了2个下划线'__'，则表明该属性和方法是私有权限，否则为公有权限。

- **修改私有属性的值**

- 如果需要修改一个对象的属性值，通常有2种方法

  > 1. 对象名.属性名 = 数据 ----> 直接修改
  > 2. 对象名.方法名() ----> 间接修改

- 私有属性不能直接访问，所以无法通过第一种方式修改，一般的通过第二种方式修改私有属性的值：定义一个可以调用的公有方法，在这个公有方法内访问修改。

```python
class Master(object):
    def __init__(self):
        self.kongfu = "古法煎饼果子配方" 
    def make_cake(self):          
        print("[古法] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)

class School(object):
    def __init__(self):
        self.kongfu = "现代煎饼果子配方"

    def make_cake(self):
        print("[现代] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)

class Prentice(School, Master):
    def __init__(self):
        self.kongfu = "猫氏煎饼果子配方"
        # 私有属性，可以在类内部通过self调用，但不能通过对象访问
        self.__money = 10000  


    # 现代软件开发中，通常会定义get_xxx()方法和set_xxx()方法来获取和修改私有属性值。

    # 返回私有属性的值
    def get_money(self):
        return self.__money

    # 接收参数，修改私有属性的值
    def set_money(self, num):
        self.__money = num


    def make_cake(self):
        self.__init__()
        print("[猫氏] 按照 <%s> 制作了一份煎饼果子..." % self.kongfu)

    def make_old_cake(self):
        Master.__init__(self) 
        Master.make_cake(self)

    def make_new_cake(self):
        School.__init__(self) 
        School.make_cake(self)

class PrenticePrentice(Prentice):
    pass


damao = Prentice()
# 对象不能访问私有权限的属性和方法
# print(damao.__money)
# damao.__print_info()

# 可以通过访问公有方法set_money()来修改私有属性的值
damao.set_money(100)

# 可以通过访问公有方法get_money()来获取私有属性的值
print(damao.get_money())
```

### 5. 多态

#### 1) 多态的理解

> 所谓多态：定义时的类型和运行时的类型不一样，此时就成为多态 
>
> 多态的概念是应用于Java和C#这一类强类型语言中，而Python崇尚“鸭子类型”。

> 鸭子类型：虽然我想要一只"鸭子"，但是你给了我一只鸟。 但是只要这只鸟走路像鸭子，叫起来像鸭子，游泳也像鸭子，我就认为这是鸭子。

Python的多态，就是弱化类型，重点在于对象参数是否有指定的属性和方法，如果有就认定合适，而不关心对象的类型是否正确。

- Python伪代码实现Java或C#的多态

```python
class F1(object):
    def show(self):
        print('F1.show')

class S1(F1):
    def show(self):
        print('S1.show')

class S2(F1):
    def show(self):
        print('S2.show')

# 由于在Java或C#中定义函数参数时，必须指定参数的类型
# 为了让Func函数既可以执行S1对象的show方法，又可以执行S2对象的show方法，
# 所以在def Func的形参中obj的类型是 S1和S2的父类即F1

# 而实际传入的参数是：S1对象和S2对象

def Func(F1 obj): 
    """Func函数需要接收一个F1类型或者F1子类的类型"""

    print(obj.show())

s1_obj = S1()
Func(s1_obj) # 在Func函数中传入S1类的对象 s1_obj，执行 S1 的show方法，结果：S1.show

s2_obj = S2()
Func(s2_obj) # 在Func函数中传入Ss类的对象 ss_obj，执行 Ss 的show方法，结果：S2.show
```

> 通俗点理解：定义obj这个变量是说的类型是：F1的类型，但是在真正调用Func函数时给其传递的不一定是F1类的实例对象，有可能是其子类的实例对象， 这种情况就是所谓的多态

- Python “鸭子类型”

```python
class F1(object):
    def show(self):
        print('F1.show')

class S1(F1):
    def show(self):
        print('S1.show')

class S2(F1):
    def show(self):
        print('S2.show')

def Func(obj):  
    # python是弱类型，即无论传递过来的是什么，obj变量都能够指向它，这也就没有所谓的多态了（弱化了这个概念）
    print(obj.show())

s1_obj = S1()
Func(s1_obj) 

s2_obj = S2()
Func(s2_obj)
```

### 6. 类属性和实例属性

#### 1) 类属性

> 就是`类对象`所拥有的属性，它被所有`类对象`的`实例对象`所共有
>
> 在内存中只存在一个副本
>
> 对于公有的类属性，在类外可以通过`类对象`和`实例对象`访问

```python
class People(object):
    name = 'Tom'  # 公有的类属性
    __age = 12  # 私有的类属性

p = People()

print(p.name)  # 正确
print(People.name)  # 正确
print(p.__age)  # 错误，不能在类外通过实例对象访问私有的类属性
print(People.__age) # 错误，不能在类外通过类对象访问私有的类属性
```

#### 2) 实例属性

> (对象属性)

```python
class People(object):
    address = '山东'  # 类属性
    def __init__(self):
        self.name = 'xiaowang'  # 实例属性
        self.age = 20  # 实例属性

p = People()
p.age = 12  # 实例属性
print(p.address)  # 正确
print(p.name)  # 正确
print(p.age)  # 正确

print(People.address)  # 正确
print(People.name)  # 错误
print(People.age)  # 错误
```

#### 3) 通过实例(对象)去修改类属性

```python
class People(object):
    country = 'china' #类属性


print(People.country)
p = People()
print(p.country)
p.country = 'japan' 
print(p.country)  # 实例属性会屏蔽掉同名的类属性
print(People.country)
del p.country  # 删除实例属性
print(p.country)
People.country = "English"  # 类外修改类属性
```

#### 4) 总结

- 需要在类外修改`类属性`，必须通过`类对象`去引用然后进行修改。
- 如果通过实例对象去引用，会产生一个同名的`实例属性`，这种方式修改的是`实例属性`，不会影响到`类属性`，
- 并且之后如果通过实例对象去引用该名称的属性，实例属性会强制屏蔽掉类属性，即引用的是`实例属性`，除非删除了该`实例属性`。

### 7. 静态方法和类方法

#### 1) 类方法

> 用修饰器`@classmethod`来标识其为类方法
>
> 第一个参数必须是类对象，一般以`cls`作为第一个参数
>
> 能够通过实例对象和类对象去访问。

```python
class People(object):
    # 私有类属性
    __country = 'china'

    #类方法，用classmethod来进行修饰
    @classmethod
    def get_country(cls):
        return cls.__country

p = People()
print(p.get_country())    #可以用过实例对象引用
print(People.get_country())    #可以通过类对象引用
```

类方法还有一个用途就是可以对类属性进行修改：

```python
class People(object):
    # 私有类属性
    __country = 'china'

    #类方法，用classmethod来进行修饰
    @classmethod
    def get_country(cls):
        return cls.__country

    @classmethod
    def set_country(cls,country):
        cls.__country = country


p = People()
print(p.get_country())   #可以用过实例对象访问
print(People.get_country())    #可以通过类访问

p.set_country('japan')   

print(p.get_country())
print(People.get_country())
```

结果显示在用类方法对类属性修改之后，通过类对象和实例对象访问都发生了改变

#### 2) 静态方法

> 通过修饰器`@staticmethod`来进行修饰
>
> 静态方法不需要多定义参数，可以通过对象和类来访问。

```python
class People(object):
    country = 'china'

    @staticmethod
    #静态方法
    def get_country():
        return People.country


p = People()
# 通过对象访问静态方法
p.get_contry()

# 通过类访问静态方法
print(People.get_country())
```

#### 3) 总结

1. 类方法的第一个参数是类对象cls，通过cls引用的必定是类对象的属性和方法；
2. 实例方法第一个参数是实例对象self，通过self引用的可能是类属性、也有可能是实例属性，但实例属性优先级更高。
3. 静态方法中不需要额外定义参数，因此在静态方法中引用类属性的话，必须通过类实例对象来引用

### 8. 单例模式

> 确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，单例模式是一种对象创建型模式。

- **创建单例-保证只有1个对象**

  ```python
  # 实例化一个单例
  class Singleton(object):
      __instance = None
  
      def __new__(cls, age, name):
          #如果类属性__instance的值为None，
          #那么就创建一个对象，并且赋值为这个对象的引用，保证下次调用这个方法时
          #能够知道之前已经创建过对象了，这样就保证了只有1个对象
          if not cls.__instance:
              cls.__instance = object.__new__(cls)
          return cls.__instance
  
  a = Singleton(18, "dongGe")
  b = Singleton(8, "dongGe")
  
  print(id(a))
  print(id(b))
  
  a.age = 19 #给a指向的对象添加一个属性
  print(b.age)#获取b指向的对象的age属性
  ```

- **创建单例时，只执行1次init方法**

  ```python
  # 实例化一个单例
  class Singleton(object):
      __instance = None
      __is_first = True
  
      def __new__(cls, age, name):
          if not cls.__instance:
              cls.__instance = object.__new__(cls)
          return cls.__instance
  
      def __init__(self, age, name):
          if self. __is_first:
              self.age = age
              self.name = name
              Singleton. __is_first = False
  
      a = Singleton(18, "习大大")
      b = Singleton(28, "习大大")
  
      print(id(a))
      print(id(b))
  
      print(a.age)  # 18
      print(b.age)  # 18
  
      a.age = 19
      print(b.age)  # 19
  ```
