# MyBatis

持久层框架，这种框架下，dao包下直接一个Mapper接口文件，加上@Mapper的注解，直接设置方法即可。方法设置完成后，需要重新构建一个xml文件，放在resource下的mapper文件夹中，在该文件中完成对应的映射即可，要调用这个方法，直接自动注入接口类即可，另外需要注意要在properties中高速mybatismapper文件都在哪个文件夹下

## 相关细节

![image-20250218131142187](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218131142187.png)

CRUD标签中，写的sql语句可以通过#{}的形式调用接口中声明的参数，

回填技术：

![image-20250218131951435](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218131951435.png)

开启驼峰命名自动映射封装，properties中直接加一句

![image-20250218132315112](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218132315112.png)

## 参数传递

#{}：参数位置动态取值，无注入问题

${}：表名等位置，不支持预编译，只能用这个

都是直接调用方法中声明的参数，若有map或对象，同样是直接通过map的属性名格式调用

若是多参数，且参数中map、实体类都有，则可以通过@Param来给方法中的参数取名，标签中调用则可以通过注解中的名字来调用，比如map，则通过map.属性名的方式填入参数

## 结果封装

返回值是什么，resulttype就写对应全类名或者是value的元素类，若返回的是map，则在实体类中添加注解@MapKey指明哪个字段作为map的键，实体类其实最后返回的HashMap而非实体类对象

返回自定义结果集：默认封装规则是根据属性名去数据库中找对应列名的值；而自定义规则则是我们主动封装：在xml中自定义一个resultMap，其中column是数据库中的名，property是对象属性名，在sql标签中再指明这个自定义的映射即可

![image-20250218150912555](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250218150912555.png)