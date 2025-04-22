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