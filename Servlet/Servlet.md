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

![image-20250106113313889](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250106113313889.png)

servlet-api.jar 导入问题：最好是通过添加依赖来导入，通过tomcat部署的项目能够共享其lib中的所有jar包，其中就包括servlet-api

content-type：记录在tomcat整体配置的web.xml文件中，文件里会有对应的映射，根据响应的文件后缀，来判断是什么类型的文件，给这个type赋相应的值，以上是针对静态资源的，如果是响应动态资源，则默认是html文件，编程过程中，应当人为设置一下： `response.setContentType("值")` 