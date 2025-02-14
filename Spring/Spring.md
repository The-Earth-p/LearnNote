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

注入的时候，一般注入接口类，Java的特性会使得哪怕注入的是接口类，也能够包含其实现类的功能，这就是依赖倒置

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

### 日志

方法1 硬编码，直接在方法里写

方法2 静态代理，定义一个代理对象类，这个对象本身不是目标对象，而是将目标对象作为自己的属性，定义期间就决定好了代理的关系，包装组件，执行业务，从代理开始，不直接调用组件

![image-20250213112101681](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213112101681.png)

代理原始代码，写了一个构造器，其他都是继承接口类的方法，在这里实现，之后在其他地方调用，就可以执行这里的实现

![image-20250213112240536](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213112240536.png)

测试代码如下：上面是直接调用，下面是通过静态代理实现

![image-20250213112443195](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213112443195.png)

方法3 动态代理（拦截器），在执行期间会被动态拦截，会插入指定逻辑，在真正执行方法之前，运用invoke来实现拦截

![image-20250213120620886](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213120620886.png)

![image-20250213120725933](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213120725933.png)

动态代理加日志，把创建动态代理抽取到一个代理对象类中，可代理任意对象（Object）

![image-20250213121149329](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213121149329.png)

### 切面

切面能够更好的做日志的记录

专业术语

![image-20250213122816012](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213122816012.png)

步骤

1. 导入AOP依赖
2. 编写Aspect切面类，通过@Aspect来告诉Spring组件是切面
3. 编写通知方法
4. 指定切入点表达式
5. 测试AOP动态织入

切入点表达式细节：

![image-20250213124308257](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213124308257.png)

可以通过execution，也可以通过args（看参数）、annotation（看注解）来指定从哪里切入。

切面看似不需要代理对象，其实Spring会为被切入的组件创建代理对象，无视接口，代理对象中保存了切面类里面所有的通知方法构成的增强器链，目标方法执行时，会先去执行增强器链中的需要提前执行的通知方法

通知的执行顺序：正常链路：前置通知->目标方法->返回通知->后置通知；异常链路：前置通知->目标方法->异常通知->后置通知

连接点：JoinPoint，包装了当前目标方法的所有信息，想要拿切入点的返回值，注解里加returning就行，如下，result的就是目标方法的返回值

![image-20250213131825101](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213131825101.png)

可以用@Pointcut来连接切入点表达式，并将其赋予一个定义的函数，之后写这个函数名就表示其对应的切入点了

![image-20250213132342678](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213132342678.png)

![image-20250213132402612](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213132402612.png)

多切面：套两层代理对象，通过@Order来确定顺序，数字越小，优先级越高，越先执行

环绕通知：可以修改目标方法执行的参数、返回值

![image-20250213134600265](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213134600265.png)

## 声明式事务

用注解的方法来实现

### 整合数据源

在properties中写，Spring会自动配一个数据源

![image-20250213140644516](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213140644516.png)

![image-20250213140711793](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213140711793.png)

使用jdbcTemplate可以直接执行增删改查，这个也是自动注入的，参数里面可以有sql的动态参数，用？作为占位符

![image-20250213141634707](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250213141634707.png)

主入口类加EnableTransactionManagement开启事务管理，在对应的方法上加上@Transactional开启声明式事务，事务直接放在dao包对于数据库的操作类当中，保证数据一致性；在这个注解后面可以添加很多参数，有：

1. timeout 超过这个参数的时间（从方法开始到最后一次数据库执行之间的时间），判定失败
2. readonly 只读
3. rollbackfor 指明哪些异常需要回滚：运行时回滚+指定回滚异常

事务管理器：管理提交和回滚，是一个接口，底层用的是jdbc的事务管理器

事务拦截器：是一个切面，控制何时提交，两个方法：

![image-20250214101500485](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214101500485.png)

### 传播行为

propagation，大事务当中的小事务和大事务是绑定的，属性也是听大事务的，这些传播是设置在dao的transactional中的

![image-20250214110241069](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214110241069.png)

![image-20250214105008285](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214105008285.png)

### 隔离级别

也是在Transactional当中设置的![image-20250214103947789](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214103947789.png)

![image-20250214103315511](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214103315511.png)
