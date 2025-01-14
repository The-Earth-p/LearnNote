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
