# Servlet学习笔记

## 动态资源 静态资源

静态资源：运行之前写好了的，如html这种

动态资源：运行时产生的资源

![image-20250106104137273](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250106104137273.png)

## Servlet简介

处理客户端请求并做出响应的技术标准：Servlet。必须运行在tomcat当中

1.tomcat接收请求后，会把请求报文转换为HttpServletRequest对象

2.同时会创建HttpServletResponse对象，承载响应信息

3.tomcat根据请求中的资源路径找到对应的servlet，将servlet实例化，调用service方法，同时将request和response对象传入，在service方法里面需要我们来实现相关的代码，包括读请求对象和写响应对象

![image-20250106111422177](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250106111422177.png)

Java类可以没有，但是有Java类可以帮助servlet的service方法更轻便，协助处理数据，服务端组件就是servlet

## Servlet开发代码

开发注意：

1.一定要添加tomcat依赖在structure中

2.在web.xml文件中配置servlet请求路径

开发代码实例：

![image-20250106112859552](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250106112859552.png)

web配置：

tomcat就是通过全路径（servlet-class）来确定要实例化的servlet类的

servlet-mapping是用来做映射的，把url的请求路径和对应的servlet类关联起来，通过servlet-name来关联

注意：

一个name可以对应多个url

不同的servlet不能对应相同的url

一个name可以有多个mapping

url的匹配：

1. 精确匹配
2. 模糊匹配：*作为通配符；/匹配全部，jsp文件除外；/ *匹配全部，包括jsp文件

举例：/a/*表示前面是/a/就行，后面随便写，匹配前缀，  *.action匹配后缀，以action结束就匹配



![image-20250106113313889](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250106113313889.png)

servlet-api.jar 导入问题：最好是通过添加依赖来导入，通过tomcat部署的项目能够共享其lib中的所有jar包，其中就包括servlet-api

content-type：记录在tomcat整体配置的web.xml文件中，文件里会有对应的映射，根据响应的文件后缀，来判断是什么类型的文件，给这个type赋相应的值，以上是针对静态资源的，如果是响应动态资源，则默认是html文件，编程过程中，应当人为设置一下： `response.setContentType("值")` 

## Servlet注解配置

![image-20250107101608743](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107101608743.png)

路径映射

注解和web.xml不可同时配置

## Servlet生命周期

实例化                            构造器   第一次请求或服务启动时才执行

初始化                            init         构造完毕就执行

接收请求，处理请求     service  每次请求都执行

销毁                                destroy 关闭服务才执行

![image-20250107103004934](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107103004934.png)

多个客户端同时请求，出现线程安全问题：servlet是单例的，而它的成员变量是多个对象中共享的，我们尽量不要修改成员变量

单例原理：看缓冲池有没有servlet对象，存在则直接使用缓存池的servlet对象，不存在才创建一个servlet对象，然后添加进servlet的缓存池，相当于一直只有一个servle对象

参数 `load on start-up` ：

-1表示不用实例化，其他正整数表示实例化的顺序，若序号冲突，tomcat会自动协调

有些序号已经被占用了，在全局的web.xml中已经配置过了

default-servlet：请求资源和现有自己定义的所有servlet没有匹配上，就会去匹配default-servlet，其实基本上就是用这个来匹配静态资源的

## Servlet继承结构

GenericServlet处理除service以外的其他方法

HttpServlet处理service方法，其中做了参数的父转子，从而可以调用更多的接口

最好是重写do...一类的方法，而不是直接重写service方法，通过老的service方法，调用自己重写的do...方法更好

## ServletConfig的使用

在调用init方法的时候会将servletconfig传入

servletconfig对象是按servlet来分的，不是通用的

![image-20250107112432176](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107112432176.png)

可在web.xml中配置，也可以通过注解配置

![image-20250107112351468](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107112351468.png)

## ServletContext

config和context的差别

config是多例的，context是单例的，一个项目就一个对象

![image-20250107112553680](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107112553680.png)

### 其他重要的api

获取某个文件的真实路径

获取项目上下文路径

![image-20250107114045564](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250107114045564.png)