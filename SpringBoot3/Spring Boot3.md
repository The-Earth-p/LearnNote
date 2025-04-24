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

@EnableConfigurationProperties  快速注册组件，有了这个，在配置类中通过这个注解指定想要绑定的类，这个类就自定变成了容器中的组件，不需要再用@Component或者@Bean来注册组件了。一般用来导入第三方包中的组件

整合第三方框架：

**选场景**，导入到项目

- 官方：starter（一般就是xxxx-starter）对应的配置类就是“场景名+AutoConfiguration”，这个配置类中会用@Bean注解对应组件

- 第三方：去仓库搜

**写配置，改配置文件关键项**

- 从@EnableConfigurationProperties中可以看到想要绑定属性的是哪一个类，到这个类中可以看到具体的一些名称，在properties配置文件中再添加前缀具体指定属性值即可

分析这个场景给我们导入了**哪些能用的组件**，在配置类中会明确用@Bean指出有哪些组件，自动注入这些组件进行后续使用，如果不满意boot提供的自动配好的默认组件，不满意这个组件中的一些方法的实现逻辑，则需要自己来写满意的实现逻辑，也就是自定义组件
