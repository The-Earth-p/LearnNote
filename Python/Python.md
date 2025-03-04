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
.index(value,[start=0[,stop]])下标为start的开始寻找，找到与value相等的元素的下标
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

