# Spring Boot3学习笔记

特性：快速创建、嵌入tomcat（jar包即可运行，不用配其他环境）、提供场景启动器，简化应用整合、自动配置

## 快速启动

1.所有springboot项目在pom中都要继承一下<parent>，在这个标签下的是spring-boot-starter-parent这个父类。添加web场景启动器

```
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
</parent>
<dependencies>
				<!-- web开发的场景启动器 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```



2.构建整个项目的唯一启动器，Main Application（名字自拟，放在最初构建的最低的文件夹下），这是一个实体类，需要加上一个@SpringBootApplication的注解告知是一个springboot项目，然后写接口就行

```
@SpringBootApplication //这是一个SpringBoot应用
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```



3.pom中还可以写一个打包插件，用<build><plugins>中的<plugin>来修饰，是spring-boot-maven-plugin，然后在右侧maven中就可以打包成一个jar包(要先clean一下，再package），在装有Java17的服务器上直接运行这个jar包就可以获得这个项目的效果，jar包放在target文件夹中。想要更改一些配置的话，比如现在我的电脑上只有一个jar包，不知道里面的具体代码，我可以通过在jar包所在的文件夹下增加一个application.properties的文件夹，运行jar包时就会自动使用这个配置文件中的配置内容

```
<!--    SpringBoot应用打包插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

## Spring Initializer

可以在构建的时候直接勾选一些依赖，这样上面快速启动里面的那些代码就直接帮我们写好了，一般都用这个来创建，上面那个太麻烦。

## 依赖管理机制

上文提到的那些场景启动器之内其实还有很多的依赖，根据依赖的传递性机制，相应的把好多需要的依赖都导进来了；

依赖的版本号不用写明，因为父项目就是一个版本仲裁中心，会把所有常见的jar依赖版本都声明好，但是如果想要自定义版本号，就在pom的<properties>标签中把想要自定义版本号的依赖的具体版本指定出来，或者直接在相应的依赖下面加一行<version>声明一下也行。

如果不是常见的依赖，就需要指明版本号

## 自动配置机制

1.只需要导入场景启动器，自动配置Tomcat、SpringMVC等

2.默认包扫描规则，会扫描主程序（用SpringBootApplication注解标注的那个实体类）所在包及其下面的子包，也就是自动的component-scan的

3.自定义扫描路径的话，可以在@SpringBootApplication中指定scanBasePackages这个参数，来指定要扫描的包，也可以用@ComponentScan这个注解来指定要扫描的包

其实配置文件的所有配置项，都是会和某个类的对象的某个属性对应的，也就是配置属性类，专门用来和配置文件中的值来绑定，比如ServerProperties类等等

spring-boot-starter是所有starter（场景启动器）的starter，因为它导入了一个spring-boot-autoconfigure包，这包里囊括了全部场景的全部配置属性类，但是不会全都开启，项目导入了哪个，它才开启哪个

## 常用注解

### 组件注册

可以在config包下写一个AppConfig配置类，配置类存在多个没关系，都生效，用@Configuration（也可以用@Spring Boot Configuration来标注）来标注，在这个类当中用@Bean标注一下自定义的返回类型为想要注册成组件的类的方法，最终容器中的名字就是这个配置类当中对应的这个方法名，也可以在注解中定义名称，组件创建后默认是单实例，@Scope(prototype)注解可以表示多实例创建

组件注册也可以通过@Controller这种来更快的注册

想要第三方中的组件放到本项目的容器中，像上面的方法用@Bean可以导入，也可以用@Import导入，给容器中放了指定类型的组件，组件名称是全类名

### 条件注解

注解指定的条件成立，则触发指定行为

```java
@ConditionalOnClass：如果类路径中存在这个类，则触发指定行为
@ConditionalOnMissingClass：如果类路径中不存在这个类，则触发指定行为
@ConditionalOnBean：如果容器中存在这个Bean（组件），则触发指定行为
@ConditionalOnMissingBean：如果容器中不存在这个Bean（组件），则触发指定行为
@ConditionalOnBean（value=组件类型，name=组件名字）：判断容器中是否有这个类型的组件，并且名字是指定的值
场景：
● 如果存在FastsqlException这个类，给容器中放一个Cat组件，名cat01，
● 否则，就给容器中放一个Dog组件，名dog01
● 如果系统中有dog01这个组件，就给容器中放一个 User 组件，名zhangsan 
● 否则，就放一个User，名叫lisi
    具体实现如下：
同样的注册逻辑，不同的就是在@Bean注解上面再多加一个注解，即想要的条件注解标识，在参数中指定条件即可
```

条件注解还可以放在整个配置类上，标识符合条件，整个配置类才生效

### 属性绑定

配置文件properties中的属性值和注册为组件的类的属性的值，用@ConfiguratonProperties来标注，参数prefix指定properties中的前缀名。这个组件就是标注在实体类里还是配置类的方法上都行，注册方法也都适用

如果有复杂对象，比如类中嵌套类，类中有list这种，具体如下：

```
@Component
@ConfigurationProperties(prefix = "person") //和配置文件person前缀的所有配置进行绑定
@Data //自动生成JavaBean属性的getter/setter
//@NoArgsConstructor //自动生成无参构造器
//@AllArgsConstructor //自动生成全参构造器
public class Person {
    private String name;
    private Integer age;
    private Date birthDay;
    private Boolean like;
    private Child child; //嵌套对象
    private List<Dog> dogs; //数组（里面是对象）
    private Map<String,Cat> cats; //表示Map
}
```

那么在properties文件中就需要：

```
person.name=张三
person.age=18
person.birthDay=2010/10/12 12:12:12
person.like=true
person.child.name=李四
person.child.age=12
person.child.birthDay=2018/10/12
person.child.text[0]=abc
person.child.text[1]=def
person.dogs[0].name=小黑
person.dogs[0].age=3
person.dogs[1].name=小白
person.dogs[1].age=2
person.cats.c1.name=小蓝//c1就是一个key
person.cats.c1.age=3
person.cats.c2.name=小灰
person.cats.c2.age=2
```

@EnableConfigurationProperties  快速注册组件，有了这个，在配置类中通过这个注解指定想要绑定的类，这个类就自定变成了容器中的组件，不需要再用@Component或者@Bean来注册组件了。一般用来导入第三方包中的组件

整合第三方框架：

**选场景**，导入到项目

- 官方：starter（一般就是xxxx-starter）对应的配置类就是“场景名+AutoConfiguration”，这个配置类中会用@Bean注解对应组件

- 第三方：去仓库搜

**写配置，改配置文件关键项**

- 从@EnableConfigurationProperties中可以看到想要绑定属性的是哪一个类，到这个类中可以看到具体的一些名称，在properties配置文件中再添加前缀具体指定属性值即可

分析这个场景给我们导入了**哪些能用的组件**，在配置类中会明确用@Bean指出有哪些组件，自动注入这些组件进行后续使用，如果不满意boot提供的自动配好的默认组件，不满意这个组件中的一些方法的实现逻辑，则需要自己来写满意的实现逻辑，也就是自定义组件

## Yaml

properties里面层次结构不清晰，yaml就是为了方便人类读写

### 语法规则

kv的话，冒号后面加空格；层级关系（子属性）换行实现，层级之间要多搞几个空格，相同层级左侧要对齐

复杂对象举例，像属性绑定里面写的，yaml可以这么写

```
person:
  name: 张三
  age: 18
  birthDay: 2010/10/10 12:12:12
  like: true
  child:
    name: 李四
    age: 20
    birthDay: 2018/10/10
    text: ["abc","def"]
  dogs:#list
    - name: 小黑#加个-表示list中的一个元素
      age: 3
    - name: 小白
      age: 2
  cats:#map键值对
    c1:
      name: 小蓝
      age: 3
    c2: {name: 小绿,age: 2} #对象也可用{}表示
```

驼峰命名可以变成“-小写字母”；单引号不会转义，双引号会转义特殊字符；`|`然后换行，表示写的是大文本，这个大文本写在下层，保留文本格式，换行符正确显示，`>`这个是不保留换行符的，会压缩换行变成空格；`---`表示多个文档，也就是上下是两个文档

备注：下面这个依赖以可自动生成构造器、getter/setter等

```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>compile</scope>
</dependency>
```

## 日志

一般都是一个日志接口+一个日志实现来完成日志的配置

spring5之后，commons-logging直接作为内部日志，被spring自己写了。

核心场景（spring-boot-starter)引入了日志的所有功能spring-boot-starter-logging，默认使用的是logback+slf4j作为默认底层日志，日志用的是监听器机制配置好的；日志的配置可以在配置文件中自己配置，以logging开头

### 日志格式

默认格式：时间和日期  日志级别  进程id  ---  线程名（使用[]包含）  Logger名（产生日志的类名）  消息（日志记录的内容）

这个格式可以自己修改，在配置文件中写logging.pattern.console来修改：`'%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{15} ===> %msg%n'`，也可以单独更改其中一个格式的表示，如日期等

在实现类代码中写自己的日志

```Java
Logger logger = LoggerFactory.getLogger(getClass());//这一行是用来获取记录这个类的日志的
```

经过上述代码获得logger后，通过logger.info(信息)即可添加自己的日志记录

或者上面都不做，直接在实现类前面加一个lombok中的@SLF4J注解，然后在代码里写log.info(信息)即可

### 日志级别

由低到高：`ALL,TRACE, DEBUG, INFO, WARN, ERROR,FATAL,OFF`；

**只会打印指定级别及以上级别的日志**

- ALL：打印所有日志

- TRACE：追踪框架详细流程日志，一般不使用

- DEBUG：开发调试细节日志

- INFO：关键、感兴趣信息日志

- WARN：警告但不是错误的信息日志，比如：版本过时

- ERROR：业务错误日志，比如出现各种异常

- FATAL：致命错误日志，比如jvm系统崩溃

- OFF：关闭所有日志记录

默认打印INFO及以上，可以通过配置文件中的logging.level.root来更改最低级别，这个最低级别多加几个路径，也可以指定具体的包中的打印级别，日志还能分组，默认有sql组和web组，统一调level

```
logging.group.tomcat=org.apache.catalina,org.apache.coyote,org.apache.tomcat
logging.level.tomcat=trace
```

### 文件保存

| logging.file.name | logging.file.path | 示例     | 效果                                                     |
| ----------------- | ----------------- | -------- | -------------------------------------------------------- |
| 未指定            | 未指定            |          | 仅控制台输出                                             |
| **指定**          | 未指定            | my.log   | 写入指定文件，可以加路径                                 |
| 未指定            | **指定**          | /var/log | 写入指定目录，文件名为spring.log                         |
| **指定**          | **指定**          |          | 同时存在，以logging.file.name为准，所以一般就用file.name |

### 归档和切割

归档：日志会越写越大，因此每天的文档存到一个独立的文件中，这是可以设置的

切割：设置每个文件的大小，超了就切成两个

滚动规则：

| 配置项                                               | 描述                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| logging.logback.rollingpolicy.file-name-pattern      | 日志存档的文件名格式（默认值：${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz） |
| logging.logback.rollingpolicy.clean-history-on-start | 应用启动时是否清除以前存档（默认值：false）                  |
| logging.logback.rollingpolicy.max-file-size          | 存档前，每个日志文件的最大大小（默认值：10MB）               |
| logging.logback.rollingpolicy.total-size-cap         | 日志文件被删除之前，可以容纳的最大大小（默认值：0B）。设置1GB则磁盘存储超过 1GB 日志后就会删除旧日志文件 |
| logging.logback.rollingpolicy.max-history            | 日志文件保存的最大天数(默认值：7)                            |

### 自定义配置

可以自己写一个xml配置文件，这些文件在resource包下面：

| 日志系统                | 自定义                                                       |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | logback-spring.xml, logback-spring.groovy, logback.xml, or logback.groovy |
| Log4j2                  | log4j2-spring.xml or log4j2.xml                              |
| JDK (Java Util Logging) | logging.properties                                           |

这些配置文件内容可以直接网上找找，配置文件名称里最好写个`-spring`

## Web开发

### web初始化

加入web的场景启动器依赖即可，绑定了一些配置文件的配置项。SpringMVC的所有配置 `spring.mvc`；Web场景通用配置 `spring.web`；文件上传配置 `spring.servlet.multipart`；服务器的配置 `server`: 比如：编码方式。总的来说就是会放很多组件，有很多默认的效果。

主要三种方式：全自动、手自一体、全手动，一般采用的都是手自一体，具体如下

用@Configuration+WebMvcConfigurer的实现再+WebMvcRegitrations，但是不要标注@EnableWebMvc，这个标注是自动配置效果的禁用器，这样就能够保留自动配置效果，但仍然能够手动设置部分功能。上面说的哪个configurer组件，里面就能添加各种定制功能，最后都是能够和对应功能绑定的

```
	@Configuration(proxyBeanMethods = false)
	@Import(EnableWebMvcConfiguration.class) //额外导入了其他配置
	@EnableConfigurationProperties({ WebMvcProperties.class, WebProperties.class })
	@Order(0)
	public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ServletContextAware{
        
    }
```

![image-20250429155533663](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250429155533663.png)

WebMvcConfigurer具体功能：

| 提供方法                           | 核心参数                              | 功能                                                         | 默认                                                         |
| ---------------------------------- | ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| addFormatters                      | FormatterRegistry                     | **格式化器**：支持属性上@NumberFormat和@DatetimeFormat的数据类型转换 | GenericConversionService                                     |
| getValidator                       | 无                                    | **数据校验**：校验 Controller 上使用@Valid标注的参数合法性。需要导入starter-validator | 无                                                           |
| addInterceptors                    | InterceptorRegistry                   | **拦截器**：拦截收到的所有请求                               | 无                                                           |
| configureContentNegotiation        | ContentNegotiationConfigurer          | **内容协商**：支持多种数据格式返回。需要配合支持这种类型的HttpMessageConverter | 支持 json                                                    |
| configureMessageConverters         | List<HttpMessageConverter<?>>         | **消息转换器**：标注@ResponseBody的返回值会利用MessageConverter直接写出去 | 8 个，支持byte，string,multipart,resource，json              |
| addViewControllers                 | ViewControllerRegistry                | **视图映射**：直接将请求路径与物理视图映射。用于无 java 业务逻辑的直接视图页渲染 | 无 <mvc:view-controller>                                     |
| configureViewResolvers             | ViewResolverRegistry                  | **视图解析器**：逻辑视图转为物理视图                         | ViewResolverComposite                                        |
| addResourceHandlers                | ResourceHandlerRegistry               | **静态资源处理**：静态资源路径映射、缓存控制                 | ResourceHandlerRegistry                                      |
| configureDefaultServletHandling    | DefaultServletHandlerConfigurer       | **默认 Servlet**：可以覆盖 Tomcat 的DefaultServlet。让DispatcherServlet拦截/ | 无                                                           |
| configurePathMatch                 | PathMatchConfigurer                   | **路径匹配**：自定义 URL 路径匹配。可以自动为所有路径加上指定前缀，比如 /api | 无                                                           |
| configureAsyncSupport              | AsyncSupportConfigurer                | **异步支持**：                                               | TaskExecutionAutoConfiguration                               |
| addCorsMappings                    | CorsRegistry                          | **跨域**：                                                   | 无                                                           |
| addArgumentResolvers               | List<HandlerMethodArgumentResolver>   | **参数解析器**：                                             | mvc 默认提供                                                 |
| addReturnValueHandlers             | List<HandlerMethodReturnValueHandler> | **返回值解析器**：                                           | mvc 默认提供                                                 |
| configureHandlerExceptionResolvers | List<HandlerExceptionResolver>        | **异常处理器**：                                             | 默认 3 个 ExceptionHandlerExceptionResolver ResponseStatusExceptionResolver DefaultHandlerExceptionResolver |
| getMessageCodesResolver            | 无                                    | **消息码解析器**：国际化使用                                 | 无                                                           |



### 错误处理

spring boot会自适应处理错误，响应页面或json数据，mvc处理错误的方法也会保留，处理不了再转给spring boot

我们还能自定义错误json响应：使用@ControllerAdvice+@ExceptionHandler注解标注一个实现类来统一处理异常

我们主要用的就是前后端分离的技术栈，web开发这块儿比较少

## 数据访问

再多导几个数据包依赖：可以勾选MyBatis Framework和MySQLDriver来直接实现导入

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.1</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

先配置数据源：

```
spring.datasource.url=jdbc:mysql://192.168.200.100:3306/demo
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
```

再配置mybatis，写好mybatis的mapper和xml文件（mybatisx插件可以自动生成mapper接口的xml文件）

```
#指定mapper映射文件位置
mybatis.mapper-locations=classpath:/mapper/*.xml
#参数项调整，驼峰命名
mybatis.configuration.map-underscore-to-camel-case=true
```

别忘了在启动类里面加一个@MapperScan的注解，告知spring boot看哪里的mapper

## 基础特性

### SpringApplication

自定义banner：banner就是在运行开始后，打印在控制台上面的哪个立体字。在配置文件中，指定一下banner.txt的文件路径就行，这个文件一般放在resource里面，banner的打印还可以关闭

自定义application的底层设置：

```java
import org.springframework.boot.Banner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(MyApplication.class);
        application.setBannerMode(Banner.Mode.OFF);//自定义底层设置的例子，但是这个配置不优先，还是配置文件优先级高一点
        application.run(args);
    }

}
```

还有流式写法：FluentBuilder API

```java
new SpringApplicationBuilder()
    .main(当前类.class)
    .sources(Parent.class)//这个和上面哪个应该一样的
    .child(Application.class)
    .bannerMode(Banner.Mode.OFF)
    .run(args);
```

### Profiles

环境隔离能力：开发、测试、生产

步骤：

1.标识环境：指定那些组件、配置在哪个环境下生效

2.切换环境：这个环境下，哪些对应组件就应该生效

profile指定环境过程：先指定环境，在实体类里面添加@Profile注解（可标注任意组件，没这个注解就全部环境都生效）就行，参数指定具体的生效环境（可以通过{}添加多个生效环境），然后激活具体的环境（可以是多个环境）`spring.profiles.active=production,test`，默认的环境是default，默认环境也可以指定。

包含环境：总是要生效的环境，`spring.profiles.include=....`

综上：**生效的环境** = **激活的环境/默认环境**  + **包含的环境**

项目里面一般把基础的配置，如mybatis、log、xxx写到包含环境中，其他动态变化的放在特定的环境中，如redis、db等等。profile也可以分组。

`application-{profile}.properties`在profile指定的环境下生效的配置文件，不带{profile}的就是主配置文件，若激活环境配置文件和主配置文件冲突，以环境配置文件为主

### 外部化配置

在jar应用所在文件夹下放一个application.properties就行

下面配置的哪些东西都可以通过@Value来获取，参数中用${}来指定想要取的值的对应的键，常见的优先级：命令行>配置文件>springappliation

```
以下是 SpringBoot 属性源加载顺序。后面的会覆盖前面的值。由低到高，高优先级配置覆盖低优先级
1. 默认属性（通过SpringApplication.setDefaultProperties指定的）
2. @PropertySource指定加载的配置（需要写在@Configuration类上才可生效）
3. 配置文件（application.properties/yml等）
4. RandomValuePropertySource支持的random.*配置（如：@Value("${random.int}")）
5. OS 环境变量
6. Java 系统属性（System.getProperties()）
7. JNDI 属性（来自java:comp/env）
8. ServletContext 初始化参数
9. ServletConfig 初始化参数
10. SPRING_APPLICATION_JSON属性（内置在环境变量或系统属性中的 JSON）
11. 命令行参数
12. 测试属性。(@SpringBootTest进行测试时指定的属性)
13. 测试类@TestPropertySource注解
14. Devtools 设置的全局属性。($HOME/.config/spring-boot)
```

配置文件：包外>包内，profile>主application，application>yaml

总结来说：

![image-20250503160157808](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250503160157808.png)

规律：最外层的最优先。

- 命令行 > 所有
- 包外 > 包内
- config目录 > 根目录
- profile > application 

配置不同就都生效（互补），配置相同高优先级覆盖低优先级

`spring.config.import`可以导入指定的配置文件，但是导入的配置低于当前的优先级

属性占位符：${}，参数写想要取的值的对应键的名，就可以拿到对应的值

### 单元测试

`spring-boot-starter-test`场景启动器添加依赖即可整合测试

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

会生成test文件包，所有要进行的测试都在test文件夹下的哪个测试类当中书写，这个测试类由@Spring BootTest的注解来标注，在具体的测试方法上要再加上一个@Test即可。自己写的测试类一定要和主程序类再同一个包下。

测试流程：@Autowired自动注入要测试的类，在用@Test标注的方法下直接调用注入组件中要测试的方法即可

JUnit5还有很多注解：

```
● @Test :表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
● @ParameterizedTest :表示方法是参数化测试，下方会有详细介绍
● @RepeatedTest :表示方法可重复执行，下方会有详细介绍
● @DisplayName :为测试类或者测试方法设置展示名称
● @BeforeEach :表示在每个单元测试之前执行
● @AfterEach :表示在每个单元测试之后执行
● @BeforeAll :表示在所有单元测试之前执行,就1次
● @AfterAll :表示在所有单元测试之后执行
● @Tag :表示单元测试类别，类似于JUnit4中的@Categories
● @Disabled :表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
● @Timeout :表示测试方法运行如果超过了指定时间将会返回错误
● @ExtendWith :为测试类或测试方法提供扩展类引用
```

断言：在Assertions中，里面有很多断言方法

| 方法              | 说明                                 |
| ----------------- | ------------------------------------ |
| assertEquals      | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals   | 判断两个对象或两个原始类型是否不相等 |
| assertSame        | 判断两个对象引用是否指向同一个对象   |
| assertNotSame     | 判断两个对象引用是否指向不同的对象   |
| assertTrue        | 判断给定的布尔值是否为 true          |
| assertFalse       | 判断给定的布尔值是否为 false         |
| assertNull        | 判断给定的对象引用是否为 null        |
| assertNotNull     | 判断给定的对象引用是否不为 null      |
| assertArrayEquals | 数组断言                             |
| assertAll         | 组合断言                             |
| assertThrows      | 异常断言                             |
| assertTimeout     | 超时断言                             |
| fail              | 快速失败                             |

嵌套测试：JUnit 5 可以通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

```java
@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```

参数化测试@ParameterizedTest来注解：多项注解

```
@ValueSource: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型
@NullSource: 表示为参数化测试提供一个null的入参
@EnumSource: 表示为参数化测试提供一个枚举入参
@CsvFileSource：表示读取指定CSV文件内容作为参数化测试入参
@MethodSource：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)
```

测试代码

```java
//因为有些方法是需要参数的，但是在测试的方法中要不断的去添加参数很麻烦，所有可以直接通过注解的方式来把参数值传给这个测试方法的形参当中，那么在这个方法中就可以直接调用形参来给要测试的那个功能传入参数。就像下面这个，string就会在strings中选一个作为自己的值，然后投入到具体的测试当中

@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}


@ParameterizedTest
@MethodSource("method")    //指定方法名，标识name是来自method这个方法的返回值的
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

static Stream<String> method() {
    return Stream.of("apple", "banana");
}
```

