# Spring学习笔记

其实就是springframework，是IOC（反转控制）+AOP（面向切面编程）的框架

## 容器篇

有一定功能的对象是组件，容器就是纯储存属性数据的，二者区别：如果使用组件实现功能，会不断的创建新的，而使用容器，只需要创建一个新的，之后调用就调用已存在的那个

IoC： Inversion of Control（控制反转）

控制：资源的控制权交给容器来做

反转：和传统方式不一样

DI： dependency injection（依赖注入）

依赖：组件的依赖关系，

注入：通过setter方法，构造器等方式自动注入

### 注册组件

容器启动过程中，就会创建组件对象；组件默认是单例的，每次获取直接从容器中拿就行

容器中获取组件：

1. 组件不存在，抛异常
2. 组件不唯一，按照类型只要一个抛异常；按照名字要一个，正确返回；要一堆，不抛异常
3. 组件唯一，正确返回

组件四大特性：

1. 名字：方法名，或者通过注解方式命名，名字全局唯一
2. 类型：组件对应的实体类的名称

项目主入口类，也称为主程序类

![image-20250212124735590](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212124735590.png)

主文件是resources里面的properties

方法1 @Bean在容器中注册组件

![image-20250212125114973](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212125114973.png)

方法2 @Configuration重新建一个包来存放各个实体类对应的bean，也就是在各种配置类中，存放bean，配置类本身也是bean一样的组件，而不是直接在主入口类中定义

![image-20250212131243655](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212131243655.png)

![image-20250212131230244](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212131230244.png)

方法3 有@controller、@service、@repository、@component(前三个都可以被这一个替代）这类，分层注解

![image-20250212131702795](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212131702795.png)

![image-20250212131759070](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212131759070.png)

组件扫描：ComponentScan

默认组件和主入口类同一个包下的，可以通过上述注解，决定要扫哪些包，即批量扫描

![image-20250212132058486](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212132058486.png)

方法4 从第三方导入相应组件，用@Import导入即可，注释书写的位置没关系

![image-20250212132419421](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212132419421.png)

### 注入组件

方法1 @Autowired 自动装配，就是Spring调用容器的getbean方法，先按照类型找，再按照名字找

![image-20250212133304699](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212133304699.png)

实例

![image-20250212133555864](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212133555864.png)

方法2 @Resource，也是自动注入，和Autowired区别就是规定这两个注解的属于不一样，Autowired就是属于spring的，而resource是属于java规范的，具有更强的通用性；at可以调整是否必须有，而resource则是必须有

方法3 构造器注入

![image-20250212135359843](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212135359843.png)

方法4 在方法上标注autowired，可以通过setter来创建

@value给组件中属性赋值

1. 直接赋值
2. 动态从配置文件（properties）中取出
3. SpEL语言表达式

![image-20250212170113568](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212170113568.png)

也可以自己写新的配置文件，把相应的数据放在对应的配置文件当中去，通过@PropertySource注解，位置无所谓

![image-20250212170503619](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212170503619.png)

![image-20250212170415271](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212170415271.png)

类路径找资源写法：

classpath:包名/配置文件名，从自己项目下面找，path后面加个*，从整个项目找

@Profile配置测试环境，也就是条件注解，在测试环境配置类组件中，在bean上面加上该注解，在properties中激活就行，通过spring.profiles.active来选择要激活的组件

![image-20250212172423722](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212172423722.png)

![image-20250212172548147](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212172548147.png)

测试类测试当前对应主程序下主程序类，直接写在测试类里面，写法和主入口类是一样的，都是注入然后调用相应方法，加一个@Test注解

![image-20250212173539222](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212173539222.png)

![image-20250212173708165](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250212173708165.png)

## AOP篇 面向切面

