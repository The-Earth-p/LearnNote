# 过滤器

原理图

![image-20250108132139298](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132139298.png)

## 实现过程

![image-20250108132252532](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132252532.png)

代码实例：

实现Filter接口

![image-20250108132328524](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132328524.png)

重写过滤方法：该方法在请求来的时候会经过，在响应之前也会经过该方法。要注意放行代码，不然执行不了对应的servlet。

![image-20250108132524270](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132524270.png)

配置过滤器：

1. web.xml中配置

和servlet配置很相似

先配置filter

![image-20250108132623050](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132623050.png)

配置哪些请求路径用这个filter，可以通过url和servletname来配置

![image-20250108132802233](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108132802233.png)

2. 注解配置：

![image-20250108134822808](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108134822808.png)

## 生命周期

和servlet的生命周期很像

1. 构造   项目启动时构造器构造
2. 初始化  构造完毕就初始化    有一个filterconfig对象传进来，带一些参数
3. doFilter  每次请求
4. destroy  项目或者服务关闭

## 过滤器链

![image-20250108133841770](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108133841770.png)

具体工作流程图：

![image-20250108134159835](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108134159835.png)

决定filter工作顺序：

web.xml中filter-mapping的书写顺序

注解配置的话，通过类名在项目包中的顺序

# 监听器

发生某些事件或者行为的时候，执行相关代码，就是通过监听器实现的，有以下八类

![image-20250108135603776](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108135603776.png)

着重了解ServletContextListener即可

监听器定义：其中参数就是事件对象

![image-20250108135942648](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108135942648.png)

生效方法：

1. web.xml中配置

![image-20250108140024644](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108140024644.png)

2. 注解配置：

![image-20250108140048230](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250108140048230.png)