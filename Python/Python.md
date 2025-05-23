# Python

## 变量

和C还有Java相比

1. 不需要声明变量数据类型
2. 变量数据类型可以变化
3. 变量使用之前必须赋值

全部大写字母表示常量，编程时不改变其值（人为约定）

小众数据类型：

列表：“［”和“］”将列表中的元素括起来，逗号分隔，元素之间没有任何关联

元组：“(”和“)”作为边界将元素括起来，其余同上

字典：唯一的键值映射类型，用{}来表示，逗号分隔，键必须是不可变类型（int，float，str等）

## 运算符

测试运算符：in ，not in，is，is not

运算符注意点：

1. +不支持不同类型的连接
2. *运算符可以用于**列表**、**元组**或**字符串**与整数的相乘，用于将这些序列**重复**整数所指定的次数。它不适用于字典和集合与整数的相乘。
3. 关系运算符可以连用，例如：3<5>2这种
4. 避免实数的相等判断
5. is和is not测试的是地址的相等，而不是数据的相等，==才是数据的相等

## 控制台输入输出

input(prompt=None)：返回类型一定是字符串

eval(source, globals=None, locals=None, /)：source是一个字符串，这个字符串能表示成Python表达式，或者是能够通过编译的代码；globals是可选的参数，默认为None，如果设置属性不为None的话，就必须是dictionary对象;locals也是可选的参数，默认为None，如果设置属性不为None的话，可以是任何map对象
print(value, …, sep=’ ‘, end=’\n’, file=sys.stdout, flush=False):

```
value：需要输出的对象，一次可以输出一个或者多个对象(其中…表示任意多个对象)，对象之间要用逗号(,)分隔
sep：对象之间的间隔符，默认用一个空格分隔；
end：以何字符结尾，默认值是换行符；
file：表示输出位置，可将输出到文件，file指定的对象要有“写”的方法，默认值是sys.stdout(标准输出，即控制台屏幕)；
flush：将缓存里面的内容是否强制刷新输出，默认值是False（一般不需要）。
```

## 流程控制

分支：if，if/else，if/elif/else（没有switch）

循环：while，for  循环中常用的range函数，返回一个可迭代对象（一般是整数，for循环可以直接用遍历：`for i in range(0（开始）,10（结束，不包括）,1(步长默认为1))`），这个对象可以被转换成列表或者元组

循环中有特殊之处，可以增加一个else语句，表示当循环条件不满足时，执行一次这个else语句，然后跳出循环

## 常用数据类型

### 列表

创建：[]或者list()

访问元素: list1[n]  从左往右从0开始，从右往左从-1开始

切片: [start: end:step]形式

```
[start：end)：左封闭(能取到)，右开放(取不到)
左索引start为0时可缺省，右索引end为列表长度时可缺省。
右索引end不写表示取到末尾
```

列表的方法：

```
-查：
.index(value,[start=0[,stop]])下标为start的开始寻找，找到与value相等的元素的下标，如果没找到则报错
.count()数数
-增：
.extend()末尾一次性添加另一个列表的多个值
.insert(index,value)在index索引位加入value
.append()末尾添加一个值
-删：
.pop()默认移除列表中最后一个元素，并返回该值，空列或索引超出会异常
.remove()移除与value相等的第一个元素，没找到就报错
.clear()删除所有元素，保留对象
-序：
.sort(reverse=False,key=str)默认升序排列,True为降序，key表示排序依据
.reverse()反转列表元素
```

命令：del 直接删除指定元素或者整个列表

列表遍历：`for i in ['train','car','plane']:`

### 元组

元组和列表基本一样，两者的转化：

```
列表转元组
vehicle = ['train', 'bus', 'car', 'ship', 'subway', 'bicycle']  #list
tuple(vehicle) #元组
t = tuple(vehicle)
元组转列表
vehicle=('train','bus','car','ship','subway','bicycle')
list1 = list(vehicle)
```

### 字典

无序集合，通过字典名+键名来查找到对应的值，也可通过这种方法来修改对应的值，若在修改时没有找到对应的键，则会新增一个键值对

方法：

1.keys()、values()

keys()方法将字典中的键以可迭代的dict_keys对象返回。values()方法将字典中的值以可迭代的dict_values对象形式返回

2.items()

将字典中的所有键和值以可迭代的dict_items对象返回，每对键值对组成元组作为一个元素

3.setdefault()

使用dict.setdefault(key, default=None)时，

- 如果字典中包含参数key对应的键，则返回该键对应的值；
- 否则以参数key的值为键，以参数default的值为该键对应的值，在字典中插入键-值对元素，并返回该元素的值部

4.update(字典名)

将另一个字典中的所有**键值对**一次性地添加到当前字典中
如果两个字典中存在有相同的键，则以另一个字典中的值更新当前字典

5.clear()

clear()方法将字典中的所有条目删除，变成空字典

6.pop()

pop()方法能够弹出并删除字典中的键值对

7.popitem()

返回最后一个键值对

8.get()

返回指定键所对应的值，如果键不存在则返回默认值。默认值为None，也可以自己指定

遍历字典：

```python
#遍历字典的key
for i in abbreviation:        #默认遍历字典的键
    print(i)
    print(i,abbreviation[i])
for i in abbreviation.keys(): #和上面相同
    print(i,abbreviation[i])
#遍历字典的value
for i in abbreviation.values():
    print(i)
#遍历字典的key-value
for i in abbreviation.items():  #keys() values() items()
    print(i)
```

字典、元组、列表的转换

```python
字典转换为列表
list(abbreviation)         #默认将键转化为列表
list(abbreviation.keys())   #键
list(abbreviation.values()) #值
list(abbreviation.items())  #键值对
字典->元组
tuple(abbreviation)          #默认将键转化为元组
tuple(abbreviation.keys())   #键
tuple(abbreviation.values()) #值
tuple(abbreviation.items())  #键值对

```

### 集合

1.union():做并集运算

2.intersection():做交集运算

3.update()方法相当于集合元素**合并**运算，注意与union()方法的区别

4.difference()做差集运算，等于集合A-集合B

5.symmetric_difference()对称差运算

6.add添加元素

7.remove删除元素，没有则出错

8.discard删除元素，没有不报错

9.pop()方法的作用是从集合中删除**任一**元素，并返回该元素；如果集合为空，则抛出KeyError异常

10.clear从集合中删除所有元素，变成一个空集合

## 可迭代对象和迭代器

Iterable 有列表、元组、字典、字符串等，都可以用for in来遍历，只要能用这两个来遍历，就是可迭代对象，一个对象只要实现了`__iter__()`方法，这个对象就是可迭代对象

Iterator  实现了`__iter__`方法和`__next__`方法，并且可以通过`__next__`方法不断返回下一个值的对象

iter()函数可以返回一个迭代器，并具有上述功能

## 推导式

### 列表推导式

在一个方括号里包含一个函数或表达式（再次计算），接着是一个for语句（遍历），然后是0个或多个for（遍历）或者if语句（过滤），在逻辑上等价于循环语句

```
语法形式：
[函数 / 表达式
for 值1 in 可迭代对象1 if 条件1
for 值2 in 可迭代对象2 if 条件2
……
for 值n in 可迭代对象n if 条件n ]
```

使用推导式和循环结构的对比

```python
n=[[10,-33,21],[5,-7,-9,3,28,-16,37]]   #一层嵌套列表
number=[]                               #常规写法：
for i in n:  #每一行
    for j in i:   #行中的每一列
	    number.append(j*2)

n=[[10,-33,21],[5,-7,-9,3,28,-16,37]]   #列表推导式：
number=[j*2 for i in n for j in i]
```

包含if判断的推导式：

```python
#将一个数字列表中的正偶数扩大2倍、正奇数扩大3倍组成新列表
n=[10,-33,21,5,-7,-9,3,28,-16,37]
number=[i*2 if i%2==0 else i*3 for i in n if i>0]
#在第一个for之前的式子都看作循环体部分，for之前的if也是看作在循环体中的判断，而for之后的if看作是否开启这次循环的判断
```

包含函数的推导式：

```python
#利用列表推导式快速生成包含15个30~80（包含30、80）之间的随机整数列表rlist。
import random
rlist=[random.randint(30,80) for i in range(15)] #使用randint()函数
rlist
# 对rlist中的元素，如果被3整除则该数除以3，如果被7整除则该数扩大2倍，其他的数扩大5倍，这些数组成新列表number
def ff(x):
    if x%3==0:
        x/=3
    elif x%7==0:
        x*=2
    else:
        x*=5
    return x 
number=[ff(i) for i in rlist] #使用自定义的ff()函数
```

同时遍历多个可迭代对象：

```python
#有两个成绩列表score1和score2，将score1中分数90及以上和score2中分数85及以下的元素两两分别组成元组，将这些元组组成列表nn中的元素
score1=[86,78,98,90,47,80,90]
score2=[87,78,89,92,90,47,85]
nn=[(i,j) for i in score1 if i>=90 for j in score2 if j<=85]
```

### 字典推导式

和列表的没什么区别，就是括号变成了花括号，需要两个表达式，一个生成键，一个生成值，两个表达式之间使用冒号分隔

```
语法形式：
{函数/表达式:表达式/函数
for 值1 in 可迭代对象1 if 条件1
for 值2 in 可迭代对象2 if 条件2
……
for 值n in 可迭代对象n if 条件n }
```

### 集合推导式

列表推导式中的方括号变成花括号，其他没有任何区别

## 字符串

ASC码：标准ASCII码为7位编码，包括英文字母、数字、标点符号等128个字符。用1个字节存储，最高位为0

Unicode编码：把所有语言统一到一套编码里；采用2个字节或4个字节（生僻字符）的编码

UTF-8编码：可变长编码，英文字符用1个字节（兼容ASCII码），中文字符用3个字节，其他语言也有用2或4个字节

GB2312、GBK（中文编码）：一般用2个字节

注意：

1. 引号成对出现
2. 单引号双引号皆可
3. 加\实现转义
4. 在字符串前加上r，就能够使得它不会转义
5. 三重引号能够实现行与行之间的回车等任何信息保存下来

### 字符串初始化

1. %符号格式化字符串，格式字符和C语言差不多，多对象时，在第二个%后面用元组把对应位置上的数据或者变量填进来

   ![image-20250305102256124](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250305102256124.png)

2. format()方法格式化字符串![image-20250305102718309](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250305102718309.png)

3. 示例：

   ```python
   '我叫{},今年{}岁'.format('张清',18)   #以下输出均为'我叫张清,今年18岁'
   '我叫{0},今年{1}岁'.format('张清',18)
   '我叫{1},今年{0}岁'.format(18,'张清')
   
   my=['张清',18]
   '我叫{},今年{}岁'.format(*my)         #列表前加*，自动解包
   '我叫{0[0]},今年{0[1]}岁'.format(my)  #列表不加*，则需要用列表的索引元素
         #{后的0表示第一个参数(若有第2个参数，则为1)，[]中的数字表示列表元素的索引
   '我叫{name},今年{age}岁'.format(name='张清',age=18)  #直接写变量名
   '我叫{name},今年{age}岁'.format(age=18,name='张清')  #有了名字之后，就可以任意顺序
   
   my={'name':'张清','age':18}
   '我叫{name},今年{age}岁'.format(**my)  #字典前加**，自动解包
   
   '{0:.2f}'.format(2/3)  #十进制的0.67
   '{0:b}'.format(8)      #二进制
   '{0:o}'.format(8)      #八进制
   '{0:x}'.format(18)     #十六进制
   '{:,}'.format(1234567890)  	#千分位格式化，1,234,567,890
   '{0:*>10}'.format(18)  		#右对齐  '********18'
   '{0:*<10}'.format(18)  		#左对齐  '18********'
   '{0:*^9}'.format(18)  		#居中对齐 '***18****'
   '{0:0=10}'.format(-18) 		    #0放在-和18中间  '-000000018'
   '{0:_},{0:#x}'.format(9999) 	#_作为分隔符（起到千分位的作用）   '9_999,0x270f'
   ```

4. 带f前缀的格式化字符串

   ```python
   name='张清'
   age=18
   f'我叫{name},今年{age}岁'
   ```

### 字符串截取

和列表一样，使用索引和切片

```python
s="student"
s[1:3]       #取出位置为1到位置为2的字符，不包括位置3的字符  'tu'
s[:3]        #取出从头至位置为2的字符   'stu'
s[-2:]       #取出从倒数第2个位置开始的所有字符,从左往右取（步长默认是1）  'nt'
s[:]         #正序全部字符
s[::-1]      #逆序全部字符
s[::2]       #步长为2
```

### 正则表达式

模式串：用来统一表示一种格式的字符串

常用的元字符：

1.'.'表示任意单个字符（除换行符之外

2.'[]'指定字符集，表示这个位置的字符只能是[]中指定字符中的一个，可以用^表示补集

3.“^xxx”：匹配`行首`，匹配每行中以^后面的字符开头的字符串（必须放在模式串的开头），只匹配多行字符串的首行，若要多行匹配，需要单独设置re.M参数

4.’xxx$'匹配行尾，每行中以该符号之前的字符结束的字符串，默认只匹配多行字符串的最后一行，想要多行匹配一样要设置参数

5.'\\' 反斜杠后面加不同字符有不同含义

```
\b匹配单词头或单词尾（\b放前面，会匹配单词头；\b放后面，会匹配单词尾）；相当于^或$(但这两个只能用于整个字符串，不能切分单词)

\B与\b相反，匹配非单词头或单词尾（不查找单词头或单词尾，只查其他部分）；

\d匹配任何数字字符；相当于[0-9]；

\D与\d相反，匹配任何非数字字符，相当于[^0-9]；

\s匹配任何空白字符，相当于[\t\n\r\f\v]；

\S与\s相反，匹配任何非空白字符，相当于[^\t\n\r\f\v]；\t是制表符，\f是换页符，\v是垂直制表符

\w匹配任何字母、数字或下画线字符，相当于[a-zA-Z0-9_]；

\W与\w相反，匹配任何非字母、数字和下画线字符，相当于[^a-zA-Z0-9_]；

也可以用于取消所有的元字符：\、[。

这些特殊字符都可以包含在[]中。如：[\s,.]将匹配任何空白字符、",“或”."
```

6.+：匹配前一个字符1-∞次

7.*：匹配前一个字符0-∞次

8.？：匹配前一个字符0-1次

9.{数字m}：匹配前一个字符m次

10.{数字m，数字n}：匹配前一个字符m-n次，省略m表示0-n，省略n表示m-∞

## 函数

定义格式：

```python
def 函数名(形式参数):  
    函数体
```

注意点：

1. 函数如果没有return，则系统会在末尾自动加入`return None`的语句
2. 传递的实参变量如果是不可变对象（int,float,str,bool,tuple,set)则形参的更改不影响实参的更改，如果是可变对象(list,dict)则更改形参也会造成实参的更改

### 参数

默认参数（声明时形参里有个等号，则调用时不传递的话就用这个值

位置参数：通过相对位置来赋值给形参

关键字参数：指明形参名来赋值

```python
def sayHello(s,n=2,m=1): 
    for i in range(1,n+1):
        print(s*m)           
#---------------------- 主程序 ---------------------
sayHello("Ha!")
print()
sayHello("Ha!",3)       #此时的3是位置参数，对应形参的n：不直观    
print()
sayHello("Ha!",m=3)     #原第2个形参取默认值
print()
sayHello(m=3,s="Ha!")   #可以不按顺序
```

通过在形参前面使用一个标识符*，表示若干个参数，这些参数会被收集成一个元组,也可以和普通参数一起使用，可以在函数声明中参数前面添加\*来承接多个实参，也可以通过在调用时的\*来将传入的实参解构分别赋值给形参

```python
def all_3(s,*args): 
    print(s)
    print(args)
           
#--------------- 主程序 ------------------
all_3("abc","a",2,3,"b")
```

字典形式参数：在声明的时候参数前面加上**，表示引用一个字典，这样的话在实际调用这个函数的时候，需要强制通过关键字参数的形式来赋值，并能够以变量名为key，以表达式值为value

### lambada函数

这是一种匿名函数，定义格式：`lambda 形式参数 : 表达式`

## 类与对象

类包含静态和动态两种特性，也就是属性（数据）和方法（操作）

python3之后所有类都是新式类：object是祖先类，也是唯一没有父类的类，其他类直接或间接继承了这个祖先类

定义格式：类名首字母大写

```python
class  <类名>：
      类属性1
      ……
      类属性n
      <方法定义1>
      ……
      <方法定义n>
```



### 属性

类的属性分为两种，一种是类属性，一种是实例属性

类属性：在类体中进行初始化，通过`类名.类属性名`的方法来访问，类属性是属于整个类的，而不是某个实例的一个，是所有实例共享的一个副本，类似于静态变量,但是可以随意更改甚至增加

实例属性：在__init\_\_()方法（构造方法）中通过如下形式初始化`self.实例变量名 = 初始值`

在实例的方法中，通过self调用，但是要记得传self参数

示例：

```python
class Rectangle:
    def __init__(self,w,h):
        self.width=w             #定义两个实例属性：width和height
        self.height=h
    def getArea(self):
        return self.width * self.height       #1.在本类的其他实例方法中，是可以访问实例属性的
    def getPerimeter(self):
        return (self.width + self.height) * 2

```

通过对属性名前的下划线的控制，区分属性的访问权限

没有下划线：公有

一个下划线：保护

两个下划线：私有，但是实际上是伪私有，仍然可以通过`对象名._类名__私有属性名`的格式来访问

### 方法

类体中方法定义的格式：

```python
def 方法名(self [,形参列表]):  
    函数体  
```

方法的访问权限，和属性的控制方法一模一样

实例的构造与初始化用到的是new和init这两个方法，在实例化一个类对象的时候会自动调用这两个方法，子类如果想要调用父类的init方法必须显示调用，不然不会自动调用父类的

实例化的过程：先通过new方法生成一个对象，然后传给init中的self，再通过init来实现调用。new方法一般不需要自己实现，基本使用的都是object类当中的那个

静态方法：一般通过类名来直接调用

```python
#所有实例共享一个静态方法，这个静态方法只能用来访问类属性和类方法
@staticmethod#必须加这个装饰器
def 静态方法名([形参列表])#不需要self
    函数体
```

类方法：一般对类本身进行操作,通过类名调用

```python
@classmethod
def 静态方法名(cls,[形参列表])#不可通过类方法访问特定的实例对象
    函数体
```

列表、字典、集合都是可变对象，int,float,str，元组等都是不可变对象，传参传可变，则改形参就改实参，传参传不可变，则改形参不改实参

### get和set

防止随意将实例属性设成任意值，则将属性私有化，通过get和set方法来实现获取和更改

## 类的重用

### 继承

基本和C++还有Java的继承差不多，但是要注意：

1. python支持多重继承，即可以同时有好几个父类
2. python是一种is-a的继承模式
3. 子类只能通过父类中的公有属性来访问私有属性
4. 子类如果定义了和父类名字相同的属性，则父类的属性在子类中会被覆盖
5. 方法同4，但是子类想要调用父类中的方法，可以使用`super(子类类名, self).方法名称(参数)  `的格式

定义语法格式：

```python
class ChildClassName(父类名1[, 父类名2[,父类名3, …]]):
    类体或pass语句
```

若小括号内没有指定父类，则表示从object类派生

继承关系中的__init\_\_方法：

1. 如果子类没有提供初始方法，则默认执行父类的初始化方法
2. 子类提供了初始化方法，则执行子类的
3. 可以通过如下两种方式调用父类的初始化方法 

```python
父类名.init(self, 其他参数)
super(本子类名, self).init(其他参数)
```

### 组合

是一种has-a的关系，也就是一个类通过把其他类的对象作为自己的属性，这就是组合

两种初始化的方法：

1. 把参数传给作为自身属性的对象的类的初始化函数

```python
#第一种方法：实现对象属性初始化。
#------------- Display、Memory类 -----------------
class Display(object):
    def __init__(self,size):
        self.size = size

class Memory(object):
    def __init__(self,size):
        self.size = size
#------------- Computer类 -----------        
class Computer(object):
    def __init__(self,displaySize,memorySize):
        self.display = Display(displaySize)  #传递参数给Display()的初始化方法
        self.memory = Memory(memorySize)     #.........Memory()............

#-------------------- 主程序 ----------------------
def main():
    c = Computer(23,2048)
    
if __name__ == "__main__":
    main()
```

2. 把类实例化的对象作为参数传给组合类的初始化参数

```python
#（第二种方法）
#------------- Display、Memory类 -----------------
class Display(object):
    def __init__(self,size):
        self.size = size
class Memory(object):
    def __init__(self,size):
        self.size = size
#--------------- Computer类 -----------------
class Computer(object):
    def __init__(self,display,memory):
        self.display = display
        self.memory = memory

#------------- 主程序 -----------------
def main():
    display = Display(23)    #创建被组合对象Display，此时传递参数
    memory = Memory(2048)
    c = Computer(display,memory)
    
if __name__ == "__main__":
    main()
```

## 异常

python中标准异常类：

![image-20250307115731095](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250307115731095.png)

异常处理语法：

```python
try:
    <可能出现异常的语句块>      # 自动抛出标准异常类的对象
    raise 自定义的异常类名字()  # 或者 主动创建并抛出 自定义异常类的对象
except <异常类名字name1>:
    <异常处理语句块1>   #如果在try部份引发了'name1'异常，执行这部分语句块   
except <异常类名字name2> as e1： #将抛出的异常对象传给e1（处理语句需要其中的信息）
    <异常处理语句块2>   #如果在try部份引发了'name2'异常，执行这部分语句块
except < (异常类名字name3, 异常类名字name4, …)> as e2: #本块可以处理多种异常类对象
    <异常处理语句块3>   #如果引发了'name3'、'name4'、…中任何一个异常，执行该语句块
…
except:
    <异常处理语句块n>   #如果引发了异常，但与上述异常都不匹配，执行此语句块
else:                  #如果没有上述所列的异常发生，执行else语句块
    <else语句块>       
finally:              #无论有无异常，都需要执行的语句块
    <任何情况下都要执行的语句块>
```

### 自定义异常类

可以继承所有异常的基类或其子类，类名要以Error或者Exception为后缀，系统无法识别自定义类，所以想要捕获，必须通过raise显式的抛出

### with语句

基本用于简化打开文件时try except语句的使用，如

```python
#常规异常处理写法
try:
    f=open('testwith.txt','r')  
    for line in f:
        print(line,end='')
finally:
    f.close()
#简化后的代码
with open('testwith.txt','r') as f:   # 不需要写f.close()语句了
    for line in f:
        print(line,end='')

```

### 断言 assert

类似于raise-if-not，断言是真，则无动作，断言为假，则出发AssertionError异常，格式如下：

```python
	assert expression [, arguments] #后面的argument作为抛出的异常对象的失败信息
```

