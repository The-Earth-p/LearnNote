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

1. 返回json，构造一个实体类，把数据放在实体类对象中，以这个类的形式返回数据，即为json格式
2. 响应时是浏览器中进行文件下载，用ResponseEntity<byte[]>来作为返回数据类型,另外还需要设置很多参数。如下：

![image-20250216210527844](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250216210527844.png)

## RESTFUL风格

软件架构风格，实例如下

![image-20250217101822737](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217101822737.png)

CRUD案例：

1. Dao层的开发，需要注入jdbc对象来实现增删改查，在dao中的同时有接口类和接口的实现类，是在实现类中注入的jdbc对象，dao就是负责实现对数据库的操作的
2. Service层开发，同样需要接口类和实现类，在实现类中，注入dao层接口类即可调用在其实现类中实现的方法，另外可以做一下防null处理，把数据库里的取出来，和页面值比较，页面没有就用数据库的
3. Controller层开发，路径中的动态数据，用{}符号来包装，在方法参数中加入@PathVariable（动态变量名称），通过注入service的接口类，直接调用相应的实现方法，返回的是json格式，方法上面的注解可以用方法+Mapping的格式来写
4. 统一返回R对象，使用泛型，如下 在泛型中，写一个构造方法，可以根据具体响应逻辑需要返回的数据类型做重载（注意有无返回对象，书写泛型相应的标准），写完构造方法后，在controller中调用即可

![image-20250217110327107](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217110327107.png)

![image-20250217110003064](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217110003064.png)

![image-20250217110210940](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217110210940.png)

5. 跨域，浏览器要求发请求的源和要去的服务器的源要一样，也就是同源，可以通过后端在响应头中添加字段：Access-Control-Allow-Origin = * 来解决。复杂的跨域会发两次，一次是option预检，第二次才是真正的
6. @PathVariable写法

![image-20250217115132164](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217115132164.png)

## 拦截器

最初要先写一个拦截器的类，拦截器类中有三种方法：preHandle（返回true才能执行after）、postHandle（目标方法之后执行）、afterCompletion，三者顺序也是这样。在config包中做一个配置，为设置拦截器要拦截的路径，mvc提供的是一个相关类是webMvcConfigurer类

拦截器类：

![image-20250217143050723](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217143050723.png)

配置类：其中要注入自己编写的拦截器类

![image-20250217143222144](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217143222144.png)

多拦截器时，每个拦截器都要在配置类里面配置，即addPathpatterns多写几遍

![image-20250217143943228](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217143943228.png)

## 异常处理

使用声明式异常处理，保持“精确优先”原则，若出现异常，本类和全局都不能处理，则有自适应处理机制

1. 使用@ExceptionHandler来设置，带这个注解的方法，会在本类出现相应异常的时候自动跳转过来执行这个方法

![image-20250217145039810](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217145039810.png)

2. @RestControllerAdvice，这是一个全局异常处理器的注解，需要另写一个类，里面也需要用到第一种注解来标注方法，然后这些方法就能够知道全局的那些异常
3. 大型系统，需要很多异常，可能需要自己编写异常类来抛出，比如BizException加上枚举类，来囊括全部的业务异常而非编写异常。在异常类里面加上一个构造器，参数是枚举类，在枚举类中把每一类错误给定死，在之后的代码编写中可以直接调用，最后再搭配全局异常处理的方法，接收抛出的自定义异常类，把相应的信息返回给前端

![image-20250217152211062](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217152211062.png)

![image-20250217152339396](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217152339396.png)

![image-20250217152438449](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217152438449.png)

![image-20250217152611975](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250217152611975.png)

## 数据校验

![image-20250218110523266](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218110523266.png)

直接在实体类的属性上加一些注解：

1. @NotBlank 不能为空，还能加参数message，出错时返回给前端
2. @Max/Min 参数“value”划定参数，也能返回message
3. @Email 要求邮箱格式

如果校验出错，返回给前端：

![image-20250218110242341](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218110242341.png)


在方法参数中再添加一个BindingResult类的参数，有很多方法可以获取错误信息

![image-20250218110642431](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218110642431.png)

也可以通过全局异常来处理错误

![image-20250218110952062](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218110952062.png)

自定义注解来实现校验：

1. 注解编写：

![image-20250218111431697](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218111431697.png)

2. 校验器编写

![image-20250218111535464](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218111535464.png)

3. 添加注解即可

![image-20250218111655234](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218111655234.png)

各种xxO：

1. Pojo：普通Java类
2. Dao：Database Access Object
3. TO：transfer Object
4. VO：Value Object

vo实例：新建一个专门用来接请求数据的实体类，在接口中实现属性值的传递，即vo转为do，在把do用于数据库的存储，关键方法是BeanUtils中的copyProperties，数据对拷

## 接口文档

Knife4j直接生成，有相应的注解
