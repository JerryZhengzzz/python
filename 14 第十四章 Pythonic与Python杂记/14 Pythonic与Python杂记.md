14 Pythonic与Python杂记

目录

[toc]


---

以前章节主打基础知识，目的是让你具备不断学习、更新知识体系的能力。这要远远好于把所有知识点罗列出来。

本章节没有主线，只是关于上述主线章节的零碎升级、补充。
毕竟，好的产品，是打磨出来的，没有一蹴而就。这里的产品，不只是app、网站，还包括课程、项目。

>七月老师：
虽然是谋生手段，但兴趣爱好，却是具有更加强大的力量。这是件很幸福的事情。

# 一、用字典映射代替switch case 语句

## 1.1 其他语言的`switch case` 语句

关于条件分支语句，其他语言是用`switch case` 语句。例如，C#中：


```C#
switch(day)
{
    case 0 :
    dayname = "Sunday";
    break;

    case 1 :
    dayname = "Monday";
    break;

    case 2 :
    dayname = "Tuesday";
    break;
    ...
    default :
    dayname = "Unknown";
    break;
}

```

python中，官方建议可以用`if else`代替上述语句。
但是，老师认为不合适。
因为其他语言中也有`if else`，如果能代替，为什么还要多出个`switch case`语句呢？只有`if else`不就可以了吗?

因此，老师的建议是，用字典映射来代替其它语言中的`switch case`语句，更简洁、好用。

## 1.2 字典映射


```py
day = 0        #定义一个变量，目的是接受下标数值
switcher = {
    0 : 'Sunday',
    1 : 'Monday',
    2 : 'Tuesday',
}

dayname = switcher[day]      #使用下标，来访问字典
print(dayname)

-->
Sunday
```


当`day = 2`时，结果是什么？



```py
day = 2        #变量被重新赋值为2
switcher = {
    0 : 'Sunday',
    1 : 'Monday',
    2 : 'Tuesday',
}

dayname = switcher[day]      
print(dayname)

-->
Tuesday
```

当`day = 6`时，结果是什么？


```py
day = 6        #变量被重新赋值为6
switcher = {
    0 : 'Sunday',
    1 : 'Monday',
    2 : 'Tuesday',
}

dayname = switcher[day]      
print(dayname)

-->
error ❌     #因为字典switcher中，找不到key=6的元素
```


## 1.3 用内置`get`函数来访问字典

当变量的值，超出了字典中的key范围时，就必须换一种字典的访问方式。
即，内置的`get`方法，来访问字典。

```py
day = 6        #变量被重新赋值为6
switcher = {
    0 : 'Sunday',
    1 : 'Monday',
    2 : 'Tuesday',
}

dayname = switcher.get(day,'Unknown')    #内置的`get`函数
             # Unknown：指定当day对应的key不存在时，函数调用将返回的结果  
print(dayname)

-->
Unknown
```
上述利用内置函数`get`，并补上`Unknown`，本质上，就是在模拟其他语言中`switch case`语句里的default分支。


两种字典访问方式对比

||通过下标访问|用内置函数get访问|
|---|---|---|
|优点|简单、直接|有容错性|

## 1.4 函数编程融入字典映射

上述字典中的value，只是个字符串，顶多是个lambda表达式。
而，我们知道，其他语言`switch case`中的`case`下面，是可以写代码块、写很多行的。
那么，python中的字典又如何模拟呢？

其实，key对应的value不只是字符串，也可以是函数。这跟函数式编程思维中的将函数赋值给变量是一个道理。


```py
day = 0

def get_sunday():       #先定义，后调用
    return 'Sunuday'
def get_monday():
    return 'Monday'
def get_tuesday():
    return 'Tuesday'

switcher = {
    0 : get_sunday,     # key对应的value也可以是函数（名）
    1 : get_monday,
    2 : get_tuesday,
}

dayname = switcher.get(day,'Unknown')()  #因为从字典switcher中，得到的key所对应的value，是函数名。所以要加个小括号()方能调用执行   
print(dayname)

-->
Sunuday
```


```py
day = 6

def get_sunday():       #先定义，后调用
    return 'Sunuday'
def get_monday():
    return 'Monday'
def get_tuesday():
    return 'Tuesday'

switcher = {
    0 : get_sunday,     # key对应的value也可以是函数（名）
    1 : get_monday,
    2 : get_tuesday,
}

dayname = switcher.get(day,'Unknown')()   
print(dayname)

-->
error ❌    #在get函数中，默认值是'Unknown'，即字符串str型。不是函数名！因为Unknown()没意义
```
为了解决上述问题，应该对默认值的情况，重新定义一个函数。

因为，字典中的value值都用的是函数，所以建议默认值也要用函数。

```py
day = 6

def get_sunday():       
    return 'Sunuday'      #这里只是返回了字符串常量。真正写业务，是可以有很多的逻辑的
def get_monday():
    return 'Monday'
def get_tuesday():
    return 'Tuesday'
def get_default():      #重新定义一个返回默认值的函数
    return 'Unknown'

switcher = {
    0 : get_sunday,     
    1 : get_monday,
    2 : get_tuesday,
}

dayname = switcher.get(day,get_default)()    #这里的get_default，是默认情况下的函数名
print(dayname)

-->
Unknown
```

>七月老师：
其他语言中的switch case下，也是不建议写很多的业务逻辑的，应该是写个方法，case只是调用这个方法。
具体的业务逻辑应该写在方法内部，而不是直接写在case语句下面。



# 二、列表推导式

叫法很奇怪，其本质跟数学中的集合推导式是一样的。
具有python特色，很pythonic。因为简洁优雅，其他语言中没有。
用途：根据已有的列表，创建一个新的列表。

## 2.1 例题

将列表`[1,2,3,4,6,7,8,]`中的每个元素，都做平方，得到一个新的列表？

(1)写法1：
使用之前的`for`循环控制语句，进行遍历


```py
a = [1,2,3,4,6,7,8]
b = []         #定义一个空列表，以接受新的列表元素

for x in a:
    b.append(x*x)      #⭐使用内置函数append()，将新的列表元素添加到列表b中

print(b)

-->
[1, 4, 9, 16, 36, 49, 64]
```

(2)写法2：
只用高阶函数中的map函数，也能遍历


```py
a = [1,2,3,4,6,7,8]
r = map(lambda x:x*x,a)
print(list(r))

-->
[1, 4, 9, 16, 36, 49, 64]
```


(3)写法3：
使用列表推导式

```py
a = [1,2,3,4,6,7,8]
b = [x*x for x in a]     #结构：How怎么算  Who算谁     #里面是个for循环，但又不是真的for循环
print(b)

-->
[1, 4, 9, 16, 36, 49, 64]
```
上述的列表推导式，本质上其实就是对写法1的简化。

>小知识点：
平方运算，不止可以写为`x*x`，还可以`x**2`。后者的指数运算很方便，因为当100个x相乘时，就直接`x**100`。

>七月老师：
如果是对列表中的全部元素进行操作，那么其实用map函数更好用。因为其他语言中也都有，用习惯了。

## 2.2 推荐的使用场景

当对非全部元素进行操作，即有选择性的操作时，比较方便。

**例题**

将列表`[1,2,3,4,6,7,8,]`中的每个大于等于5的元素，都做平方，得到一个新的列表？

写法1：
使用高阶函数中的filter函数


```py
a = [1,2,3,4,6,7,8]
r = filter(lambda x:x*x if x>=5 else None,a)
print(list(r))

-->
[6, 7, 8]     # ?
```


写法2：
使用列表推导式
```py
a = [1,2,3,4,6,7,8]
b = [x*x for x in a if x >=5]     #只要加个条件筛选即可
print(b)

-->
[36, 49, 64]
```

## 2.3 常见误区

虽然叫列表推导式，但是，不只是能推到列表。
还可以推导集合set、元组tuple、字典dic。

**（1）“集合推导式”**
```py
a = {1,2,3,4,6,7,8}            #集合
b = [x*x for x in a if x >=5]    
     #因为这里的中括号[]，并不是列表推导式的固定字符。如果想得到集合，那么这里就应该是集合符合{}
print(b)

-->
[36, 49, 64]        #结果仍是列表
```
改为：

```py
a = {1,2,3,4,6,7,8}            #集合
b = {x*x for x in a if x >=5}     #列表推导式的符号改为集合符号{}
    
print(b)

-->
{64, 49, 36}       #结果是集合
```

**（2）“字典推导式”**

**例题1：**
将字典`{'喜小乐':18,'石敢当':20,'横小五':15}`中的key提取出来，单独制成一个列表？

```py
students = {
    '喜小乐':18,
    '石敢当':20,
    '横小五':15
}

b = [key for key,value in student]   #列表一个形参即可，但字典因为有键值对，必须两个形参
print(b)

-->
error ❌    #字典不能直接被for in循环遍历
```

通过调用字典的items()方法，来取出很多条目中的一个：

```py
students = {
    '喜小乐':18,
    '石敢当':20,
    '横小五':15
}

b = [key for key,value in student.items()]  
print(b)

-->
['喜小乐', '石敢当', '横小五']   
```

**例题2：**
将字典`{'喜小乐':18,'石敢当':20,'横小五':15}`中的key与value位置颠倒，制成一个新的字典？

```py
students = {
    '喜小乐':18,
    '石敢当':20,
    '横小五':15
}

b = {value:key for key,value in students.items()}   #这里的推导式符号是字典符号{}，因为得到的是一个字典
print(b)

-->
{18: '喜小乐', 20: '石敢当', 15: '横小五'}
```

**(3)“元组推导式”**

推导式符号，变为中括号时：

```py
students = {
    '喜小乐':18,
    '石敢当':20,
    '横小五':15
}

b = (key for key,value in students.items())     #推导式符号改为元组  #前面只能是key
print(b)

-->
<generator object <genexpr> at 0x0000022569729930>    #得到的不是元组，而是可遍历的对象generator
```

```py
students = {
    '喜小乐':18,
    '石敢当':20,
    '横小五':15
}

b = (key for key,value in students.items())     
for x in b:
    print(x)

-->
喜小乐
石敢当
横小五
```
之所以元组会出现这样一个情况：得到一个generator对象，是因为元组是不可变的，它很多行为和列表等一些典型的可变的是有区别的。





##Pythonic与Python杂记

###目录

- 用字典映射代替其他语言中switch case 语句
- 列表推导式
- **None的特殊性与误区**
- **自定义对象与bool的对应关系**
- 装饰器的副作用
- 编程能力
- 海象运算符
- 用f关键字做字符串拼接
- 装饰器：@dataclass数据类


---

# 三、None 的特殊性与误区

## 3.1 None与空字符串、空列表、0、False的关系

None与空字符串、空列表、0、False均不相同。两层含义：

- 类型不相同
- 取值不相同

验证

```py
a = ''
b = False
c = []

print(a == None)
print(b == None)
print(c == None)      #值是否相等，比较运算符

-->
False
False
False        #取值上，None与空字符串、空列表、0、False均不相同
```

```py
a = ''
b = False
c = []

print(a is None)
print(b is None)
print(c is None)       #身份是否相同，身份运算符
print(type(None))

-->
False
False
False        #类型上，None与空字符串、空列表、0、False均不相同
<class 'NoneType'>     #因为None的类型是 NoneType 
```



## 3.2 判空操作

判空的时候，以下两者不能混为一谈：

```py
a

if not a
if a is None
```

```py
def fun():
    return None    #函数定义时，经常会返回None

a = fun()

if not a:
    print('S')
else:
    print('F')

if a is None:
    print('S')
else:
    print('F')

-->
S
S       #上面的两个操作结果相同。
```

很多人误认为既然上面的两个操作结果相同，就以为两者是一个东西。其实，两者并不是一回事儿：

```py
a = []      #空列表

if not a:        # not a是逻辑运算符，第二章：and or not且或非 
    print('S')        # not a就是True。
else:
    print('F')

if a is None:
    print('S')
else:
    print('F')

-->
S        #由此可见，两者不是一回事
F       
```

很多同学的判空操作是`if a is None`，老师推荐用以下方式：


```py
if a：
if not a：

因为，当
a = None
a = ''
a = []
a = False
时候，都可以得到想要的结果
```

## 3.3 None与False的本质概念

|          | None       | False  |
| -------- | ---------- | ------ |
| 数据类型 | NoneType型 | bool型 |
| 本质概念 | 表示不存在 | 表示假 |

有时候，`if None`与 `if False`得到的结果可能是相同的，但是两者的本质概念是不同的。

同理：


```py
[]      #表示这个列表中没有任何元素
''      #表示这个字符串中没有任何元素

或：这个序列里面没有任何的字符
```



# 四、自定义对象与bool的对应关系

## 4.1 奇怪现象


```py
a = ''
print(a == None)

-->
False
```

`a` 与`None`的取值是不相同的，但`if a` 与 `if None`走的分支却是相同的。这是为什么？

因为，if 在逻辑判断时，python中的每个对象都与bool有对应关系。例如：

```py
''      # False
[]      # False
None    # False    #他俩的bool值是相同的
```

那么，自定义对象与bool有对应关系是什么呢？



## 4.2 自定义对象与`__len__`方法

以我们之前的student类为例：

```py
class Test():
    pass

test = Test()     #实例化一个对象

if test:
    print('S')

-->
S
```

上面是我们经常判空的一个逻辑误区：自定义对象，一定都是True。即：
如果test存在，那么`if test`成立，就能打印出S。
相反，如果test的取值是None时，那么`if test`就不成立，就不能打印出S。


**（1）那么有没有可能，test依然存在，不是None，但依然打印不出S呢？**
有。

```py
class Test():
    def __len__(self):    #在test类下面，定义一个内置的方法len，它是个实例方法
        return 0    #让test的长度永远是0


test = Test()    

if test:
    print('S')

-->
没有S被打印出来
```

如果加个else分支：


```py
class Test():
    def __len__(self):    #在test类下面，定义一个内置的方法len，它是个实例方法
        return 0    #让test的长度永远是0

test = Test()    

if test:
    print('S')
else:
    print('F')

-->
F
```

这就是判空操作时，习以为常的最大的误区。
自定义对象，做判空操作时，不一定就是True，就会进入if分支


解释如下：

```py
class Test():
    def __len__(self):   
        return 0    

test = Test()    

print(bool(None))
print(bool([]))
print(bool(test))

-->
False
False
False    # test的bool型是False，所以上面进入的是else分支
```

如果把len方法去掉：


```py
class Test():
    pass   

test = Test()    

print(bool(None))
print(bool([]))
print(bool(test)

-->
False
False
True
```

由此可见，自定义的对象，其bool类型与len方法有关系。


**（2）当然，除了上面的`__len__`方法，还与一个`__bool__`方法有关系。
下面，探讨下这两个方法，是如何决定和影响test对象的最终的bool取值的？**



```py
class Test():
    pass         #什么都没定义

print(bool(Test()))

-->
True
```

以上，让我们天然的以为：只要test对象存在，就一定是True。


```py
class Test():
    def __len__(self):      #定义了一个len方法
        return 0       #空值

print(bool(Test()))

-->
False
```


```py
class Test():
    def __len__(self):
        return 8     #非空值

print(bool(Test()))

-->
True
```

以上很好理解，因为返回的数字0被转型为False，数字8被转型为True。

但是，当返回的不是数字时：



```py
class Test():
    def __len__(self):
        return '8'     #字符串8

print(bool(Test()))

-->
error ❌    #内置函数len代表的是长度，所以只能是数字
```

```py
class Test():
    def __len__(self):
        return 1.0     #数字的浮点型1.0

print(bool(Test()))

-->
error ❌    #内置函数len代表的是长度，所以只能是数字，且只能是整型int
```


```py
class Test():
    def __len__(self):
        return True     #浮点型

print(bool(Test()))

-->
True
```

**小结：**
1.内置函数len代表的是长度，所以只能是数字，且只能是整型int
2.Test类的bool取值，以__len__的返回结果来决定。
因为，运行`bool()`实质上还是在调用对象下面__len__的方法。


- 有__len__的方法

```py
class Test():
    def __len__(self):
        return True    

print(len(Test()))      #全局的内置方法len(),其实质也是在调用对象下面的__len__的方法
print(bool(Test()))

-->
1   
True
```

- 没有__len__的方法

```py
class Test():
    pass       #当对象下面没有__len__的方法时

print(len(Test()))     

-->
error ❌    #内置方法len()就会出错
```

>七月老师：
>像`len()`这种全局的内置函数，并不神秘。本质还是要去调用对象下面的某个方法。只不过，调用更加方便。



## 4.3 自定义对象与`__bool__`方法

当两个方法都存在时，优先调用`__bool__`方法，最正宗。
没有时，再调用`__len__`方法。

```py
class Test():
    def __bool__(self):
        return False

    def __len__(self):     #上下两个方法背道而驰
        return True

print(bool(Test()))

-->
False     # 优先用bool方法
```


```py
class Test():
    def __bool__(self):
        return 0           #能返回整型0吗

    def __len__(self):     
        return True

print(bool(Test()))

-->
error ❌         #__bool__方法只能返回bool类型
```

虽然，很多时候，数字与bool能相互换算，但是类型不同，所以本质就不是一个东西。


```py
class Test():
    def __bool__(self):
        print('bool called')
        return False    
               
    def __len__(self): 
        print('len called')    
        return True

print(bool(Test()))

-->
bool called      #只运行了__bool__()方法
False
```

```py
class Test():
    # def __bool__(self):         #把__bool__()方法去掉
    #     print('bool called')
    #     return False        
               
    def __len__(self): 
        print('len called')    
        return True

print(bool(Test()))

-->
len called            #__len__将会被调用
True
```







##Pythonic与Python杂记

###目录

- 用字典映射代替其他语言中switch case 语句
- 列表推导式
- None的特殊性与误区
- 自定义对象与bool的对应关系
- **装饰器的副作用**
- **编程能力**
- **海象运算符**
- **用f关键字做字符串拼接**
- **装饰器：@dataclass数据类**


---

# 五、装饰器的副作用

## 5.1 副作用

无装饰器时：

```py
def f1():
    print(f1.__name__)

f1()

-->
f1         #f1()函数的名字，是f1
```

有装饰器时：
增加一个打印时间的功能

```py
import time

def decorator(func):     #老函数要作为参数传进装饰器中
    def wrapper():
        print(time.time())     #增加打印时间的逻辑
        func()          #调用老函数
                        #⭐上面新老逻辑的调用均是函数，必须有小括号()才能运行
    return wrapper

@decorator
def f1():      #在格式缩进上，老函数与装饰器是平级的
    print(f1.__name__)

f1()

-->
1651636361.2582045
wrapper         #函数的名字发生了改变，成了wrapper，即装饰器中内嵌函数的名字
```

在某些情况下，函数的名字发生了变化，会影响代码。

无装饰器时：

```py
def f1():
    '''
        This is f1      
    '''                  #增加一段函数的注释
    print(f1.__name__)

print(help(f1))    #利用内置函数help查看f1的文档

-->
f1()
    This is f1      

None
```

有装饰器时：

```py
import time

def decorator(func):     
    def wrapper():
        print(time.time())     
        func()          
    return wrapper

@decorator
def f1():
    '''
        This is f1      
    '''                 #增加一段函数的注释
    print(f1.__name__)

print(help(f1))    

-->
wrapper()        #不仅函数名字发生了改变
                 #函数的注释也没有了
None
```

由此可见：
加了装饰器后，不仅老函数的名字发生了改变，函数的注释也变没了。
当执行带装饰器的函数f1时，本质上是直接执行的wrapper()，而不是f1.所以。函数的名字会丢失。

>七月老师：
>python写框架时，会非常依赖函数的名字。因为不要滥用装饰器，否则会容易发生不可预知的错误。



## 5.2 那么，如何在加了装饰器后，函数的名字不会改变，依然保持原有的函数名呢？

导入另一个内置的装饰器`wraps`
作用：装饰器`wraps`知道所传入函数f1的各种信息，并将其复制到闭包函数中


```py
import time
from functools import wraps    #从函数库里，导入另一个函数（装饰器）

def decorator(func): 
    @wraps(func)      #位置：在原装饰器内部，在闭包函数的上面加，并传新参
    def wrapper():
        print(time.time())     
        func()          
    return wrapper

@decorator
def f1():
    '''
        This is f1      
    '''                 
    print(f1.__name__)

f1()   

-->
1651638131.3523476
f1         #函数的名字，又返回到了其本来的名字
```

```py
import time
from functools import wraps    #从函数库里，导入另一个函数（装饰器）

def decorator(func): 
    @wraps(func)      #位置：在原装饰器内部，在闭包函数的上面加，并传新参
    def wrapper():
        print(time.time())     
        func()          
    return wrapper

@decorator
def f1():
    '''
        This is f1      
    '''                 
    print(f1.__name__)

print(help(f1))

-->
f1()
    This is f1

None
```

当执行带装饰器的函数f1时，本质上是直接执行的rwrappe()，而不是f1。所以，函数的名字会丢失。
而加了装饰器`wraps`后，就将f1的各种信息，复制到了闭包函数rwrappe()中。



# 六、编程能力

## 6.1 什么是编程能力，凭什么你比别人强，你的薪水比别人高？

不是会的语言多 ，--------你会8门语言，别人会5门语言
不是会的框架多， --------你会7个框架，别人会4个框架  
不是api、语法背的熟，
而是简单的东西，有深入的理解，并能应用之解决问题。

因此，学习编程的动机、目的，是为了解决问题，做出好的项目、产品来。
而不是面向面试编程。
算法、数据结构、框架源码的确对面试有所帮助，但这不是初衷。

>七月老师：
>很多人面试优秀，但动手很差；
>而有的人面试一般，但动手能力很强。

## 6.2 如何提高编程能力？

做高标准的真实项目，非demo。



# 七、海象运算符

walrus operator,python 3.8 新增的运算符。
符号为`:=`

作用：对一个表达式（函数的调用）进行求值，完成后可以在一行内完成一个变量的定义、赋值操作。

>七月老师：
>有点像Go语言中的，声明并赋值。

**例题1:**
求字符串`'python'`的长度？

```py
a = 'python'
b = len(a)
print(b)

-->
6
```

**例题2:**
判断字符串`'python'`的长度，如果大于5，就打印“长度大于5;真实的长度为”？

写法1：
把字符串的长度求出来，赋值给新变量b。

```py
a = 'python'
b = len(a)

if b >5:
    print('长度大于5；'+'真实的长度为'+str(b))

-->
长度大于5；真实的长度为6
```

写法2：
没有新变量b，以后每次都要用len函数。

```py
a = 'python'

if len(a) >5:
    print('长度大于5;'+'真实的长度为'+str(len(a)))     #使用了两次len(a)

-->
长度大于5；真实的长度为6
```

写法3：


```py
a = 'python'

if b := len(a) >5:        #海象运算符，像赋值运算符一样，默认顺序最后
    print('长度大于5;'+'真实的长度为'+str(b))

-->
长度大于5;真实的长度为True    #注意运算符的优先级顺序
```

改正：

```py
a = 'python'

if (b := len(a)) >5:       #用下括号， 调整为我们想要的运算符顺序
    print('长度大于5;'+'真实的长度为'+str(b))

-->
长度大于5;真实的长度为6
```

**小结：**
写法3 的优点：
针对写法1：省去了单独定义的那行`b = len(a)
`
针对写法2：避免了函数的重复求值，提高了性能，作用很大。



# 八、用f关键字做字符串拼接

**1.以往的字符串拼接**

```py
a = 'python'
if (b := len(a)) >5:
    print('长度大于5;'+'真实的长度为'+str(b))

-->
长度大于5;真实的长度为6
```

**2.用f关键字做字符串拼接**

f关键字，花括号{ }包裹动态变量

```py
a = 'python'
if (b := len(a)) >5:
    print(f'长度大于5;真实的长度为{b}')    #f关键字，花括号包裹动态变量b

-->
长度大于5;真实的长度为6
```

当然，不止能拼接一个动态变量：


```py
a = 'python'
if (b := len(a)) >5:
    print(f'长度大于5;{a}真实的长度为{b}')    #f关键字，花括号包裹动态变量b

-->
长度大于5;python真实的长度为6
```

优点：

- 不用加号+了，可读性更好
- 不用考虑数据类型的转型了



# 九、装饰器：@dataclass数据类，自动生成构造函数，简化代码

作用：自动生成构造函数，简化代码

**1.以前的定义类时构造函数的写法**

```py
class Student():   
    def __init__(self,name,age,school_name):
        self.name = name
        self.age = age
        self.school_name = school_name    #显得呆板、机械
        
    def test(self):      #实例方法定义，必须传入self
        print(self.name)

student = Student('7yue',18,'Tsinghua')
student.test()

-->
7yue         
```

**2.用数据类dataclass定义类的构造函数**


```py
from dataclasses import dataclass    #从模块dataclasses中，导入装饰器dataclass

@dataclass         #语法糖 @dataclass
class Student():   
    name : str       #形参 : 形参类型
    age : int
    school_name : str    
        
    def test(self):      
        print(self.name)

student = Student('7yue',18,'Tsinghua')
student.test()

-->
7yue         #结果跟上面的写法1相同
```

由此可见，写法2中的语法糖`@dataclass`会自动生成之前的构造函数。这在实例化参数比较多时，会比较方便。

**3.装饰器dataclass只能生成构造函数吗？**


不是。还可以生成其他的魔术方法`__repr__()`。

普通构造函数时的`__repr__()`：

```py
class Student():   
    def __init__(self,name,age,school_name):
        self.name = name
        self.age = age
        self.school_name = school_name   
        
    def test(self):      
        print(self.name)

student = Student('7yue',18,'Tsinghua')
print(student.__repr__())

-->
<__main__.Student object at 0x0000014E825A7C40>    #可读性差 
```



使用装饰器dataclass的`__repr__()`：

```py
from dataclasses import dataclass    #从模块dataclasses中，导入装饰器dataclass

@dataclass         #语法糖 @dataclass
class Student():   
    name : str       #形参 : 形参类型
    age : int
    school_name : str    
        
    def test(self):      
        print(self.name)

student = Student('7yue',18,'Tsinghua')
print(student.__repr__())

-->
Student(name='7yue', age=18, school_name='Tsinghua')  #类似于像构造函数签名一样的文本
```


还可以生成`eq`方法

**4.以上生成的方法，可控吗？**
可控。

关闭装饰器@dataclass生成构造函数`init()`：

```py
from dataclasses import dataclass  

@dataclass(init = False)        #关闭了装饰器@dataclass自动生成构造函数
class Student():   
    name : str       
    age : int
    school_name : str    
        
    def test(self):      
        print(self.name)

student = Student('7yue',18,'Tsinghua')
student.test()

-->
error  ❌    #所以，类下面没有了构造函数，实例化对象时，自然就传不了实参
```

关闭了装饰器@dataclass生成魔术方法`__repr__()`：

```py
from dataclasses import dataclass    

@dataclass(repr = False)         
class Student():   
    name : str      
    age : int
    school_name : str    
        
    def test(self):      
        print(self.name)

student = Student('7yue',18,'Tsinghua')
print(student.__repr__())

-->
<__main__.Student object at 0x000001EC639DBCA0>  #变成了默认的repr返回结果
```



