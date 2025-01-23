# Java学习笔记

## 基础篇：

### 隐式转换

取值范围：![image-20250109135924979](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250109135924979.png)

小的转大的，可以直接转，程序会自动编译，注意，byte、short、char这三种类型在运算时都会直接提升为int，再进行运算

### 强制转换

大的转小的，不可以直接操作，一定要通过强制转换进行。

格式：![image-20250109140202535](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250109140202535.png)

### 代码块

局部代码块：可以在局部代码块中声明一个变量，但是这个变量的生命周期就只在这个代码块当中。

构造代码块：给类中的属性赋一个初值，属于这个类的每一个对象创建的时候都会加载一次构造代码块，类似于显式赋值，也是给类的对象的属性赋值的一种方式

静态代码块：在类加载的时候，做一些初始化操作

![image-20250114134833925](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250114134833925.png)

### 静态成员

分为静态属性和静态方法，Java当中通过类名直接调用静态对象，因为所有通过这个类生成的对象，都是使用共同的一个静态对象的

![image-20250114135724700](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250114135724700.png)

### final

用来修饰变量、方法、类，表示不可改变

注意：

修饰变量必须在声明时或者在构造器中初始化（每个构造中都要写），否则编译器报错

![image-20250123111709578](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250123111709578.png)

修饰方法表示不能被子类重写或者覆盖

修饰类表示这个类不能被继承，其中的所有方法也都自动变成final

### this

帮助做成员变量还是形参的区分：

![image-20250123112051475](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250123112051475.png)

还可以通过this调用本类中的其他方法

### super

调用父类的成员及方法，表示对父类的引用

## 中级篇

### 方法

定义格式：基本包含static，和c++中的static不太一样，子类继承过去之后也是可以使用的

![image-20250123115442610](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250123115442610.png)

满足下列条件的多个方法相互构成重载

- 多个方法在同一个类中
- 多个方法具有相同的方法名
- 多个方法的参数不相同，类型不同或者数量不同
- 和函数的返回类型无关

标准类制作：

① 类名需要见名知意

② 成员变量使用private修饰

③ 提供至少两个构造方法

- 无参构造方法
- 带全部参数的构造方法

④ get和set方法

 提供每一个成员变量对应的setXxx()/getXxx()

⑤ 如果还有其他行为，也需要写上



### 继承

子类可以直接调用父类中的方法。

不能继承的内容：构造方法、非虚方法表（非static、final、private修饰的）里的方法

父类子类重名：

1.成员变量重名，优先访问子类中的成员值，要访问父类的需要使用super关键字；

2.方法重名，子类优先调用自己的

方法重写：父类子类方法完全一致，及发生重写，需要用override注解来标识

Java只支持单继承，不支持多继承；一个类可以有多个子类；可以多层继承；顶层父类是object

### 多态

