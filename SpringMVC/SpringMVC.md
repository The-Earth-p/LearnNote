# SpringMVC

代码实例，也可以用RestController来融合Controller和ResponseBody，表示这个类下面的全部方法的返回值都在响应体中

![image-20250214112516817](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214112516817.png)

## 路径映射：@RequestMapping

通配符：精确优先，精确唯一![image-20250214113515393](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214113515393.png)

## 请求限定

相关注解中的参数值如下

请求路径：value

请求方式：method

请求参数：params

请求头：headers

请求内容类型：consumes

响应内容类型：produces

## 请求处理

1. 使用普通变量接请求参数：没携带，包装类型自动封装null，基本类型封装为默认值，变量名和参数名要求一致

2. 用@RequestParam来标注变量名（请求路径中）和参数名（方法中）的对应关系，还可以通过设置是否需要，是否有默认值
3. 使用POJO类匹配封装全部变量，构建一个实体类，封装相应属性数据即可，但是属性的命名也要和请求路径中的变量名一样。不需要在参数前面加注解，4也一样
4. pojo级联封装复杂对象 实体类A中还有实体类B，若请求路径中用“实体类名B.属性”的格式标明了变量名，则可以直接封装到实体类A中的实体类B
5. @RequestBody接收json格式对象（Post发来的） 这个注解加在方法的参数前面
6. 文件上传 ，不用加RequeBody，因为不是json格式的文件，而是使用requestparam注解，用MultiparFile这个对象来承接，可以在properties配置文件中规定上传的文件大小的约束
7. HTTPEntity泛型来承接整个请求体，泛型可以换成其他类型，这样entity里面的body就会编程对应的类型

![image-20250214153024684](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250214153024684.png)

## 响应处理

