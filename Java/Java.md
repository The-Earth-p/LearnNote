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

前提条件：

1. 有**继承**或者实现关系
2. 方法的重写【意义体现：不重写，无意义】
3. 父类引用指向子类对象

运用实例：

若没有多态下图中只能定义三个不同的方法接收三种数据类型，分别为学生、老师、管理员

![image-20250123163119099](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250123163119099.png)

但是如果有了多态，就可以用一个统一发方法，一个父类的形参来接收各种各样的形参

![image-20250123163216079](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250123163216079.png)

注意：

- 当一个方法的形参是一个类，我们可以传递这个类所有的子类对象。
- 当一个方法的**形参是一个接口**，我们可以传递这个**接口所有的实现类对象**。
- 而且多态还可以根据传递的不同对象来调用不同类中的方法

代码实例：

```java
    public static void main(String[] args) {
        //创建三个对象，并调用register方法

        Student s = new Student();
        s.setName("张三");
        s.setAge(18);


        Teacher t = new Teacher();
        t.setName("王建国");
        t.setAge(30);

        Administrator admin = new Administrator();
        admin.setName("管理员");
        admin.setAge(35);



        register(s);
        register(t);
        register(admin);


    }

    //这个方法既能接收老师，又能接收学生，还能接收管理员
    //只能把参数写成这三个类型的父类
    public static void register(Person p){
        p.show();
    }
}```
```

### instanceof

关键字，用来做类型的校验。特性：

```java
//先判断a是否为Dog类型，如果是，则强转成Dog类型，转换之后变量名为d
//如果不是，则不强转，结果直接是false
if(a instanceof Dog d){
    d.lookHome();
}else if(a instanceof Cat c){
    c.catchMouse();
}else{
    System.out.println("没有这个类型，无法转换");
}```
```

1
