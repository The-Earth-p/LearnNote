# MyBatis

持久层框架，这种框架下，dao包下直接一个Mapper接口文件，加上@Mapper的注解，直接设置方法即可，也可以在配置类中用MapperScan注解来说明哪些是mapper。方法设置完成后，需要重新构建一个xml文件，放在resource下的mapper文件夹中，在该文件中完成对应的映射即可，要调用这个方法，直接自动注入接口类即可，另外需要注意要在properties中高速mybatismapper文件都在哪个文件夹下

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

## 关联查询

总共有1-1 1-N N-N三种数据关系

1-1的关联查询：association标签能够帮忙解释从数据库中取出来的两表联合的数据应该对应到哪一个对象的属性中去

1-N的关联查询：collection标签能够说明对象中的某一属性是一个集合，通过oftype指定集合中元素的类型

## 分步查询

人工的分步，相当于自己写了两个接口，按顺序调用，即可得到结果

自动分步，写一个方法，可以自动调用两个方法甚至更多，是一种嵌套的思想，在resultMap中会明确指出collection或者association，通过这两个标签下的select标签指明下一个要调用的方法，在传参的时候，通过第一步的方法得到的列表名和参数名相匹配，即{}这种形式，就可以完成第一步到第二步的参数传递

![image-20250219150540979](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219150540979.png)

分步开启懒加载：![image-20250219150604162](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219150604162.png)

## 动态SQL

根据不同条件来拼接语句，比如某些数据可能缺失进行比较会报错，就不需要在控制层或服务层来一条一条去判断，直接在xml里面写就行

1. if标签 test来写判断条件，Java咋写他究咋写，只更新带值的部分，null的保持原值![image-20250219151241835](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219151241835.png)
2.  where标签 解决and多少的问题，会自动填充and![image-20250219151541098](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219151541098.png)
3.  set标签 ，主要解决逗号问题![image-20250219152010049](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219152010049.png)
4. trim标签 自定义截串，用来代替set和where的，比前两个标签更强大，保证语法正确，通过prefix添加前缀，通过prefixOverrides来用空串覆盖前缀

![image-20250219152523483](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219152523483.png)

5. choose when otherwise三者用来选择单一条件，此时不需要考虑语法

6. foreach标签 collection用来承接方法中的参数，也就是传过来的数组，item表示数组中的单个元素，separator用来表明分隔符，open表示以什么开始，close表示以什么结束

批量查询

![image-20250219203941069](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219203941069.png)

批量插入

![image-20250219204633747](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219204633747.png)

批量修改，要发多个sql，需要开启多sql支持![image-20250219205036225](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219205036225.png)

![image-20250219204815862](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250219204815862.png)

## 缓存机制

Mybatis有二级缓存机制，默认事务期间，会开启事务级别缓存，缓存有失效的情况

一级：同一个事务有缓存，前后查询不需要多次访问数据库，即为事务级别的缓存，前后执行相同的查询，不会再给数据库发sql

二级：一次事务的结果会存在二级里

## 分页插件

1. 引入分页插件pagehelper
2. 写一个配置类，创建一个分页插件对象，在配置类中，可以设置分页对象的一些属性，比如是否合理化
3. 设置参数，pagehelper.startPage通过调用这个方法，下一条执行的sql会根据这个方法的参数来分页

前后端怎么串联信息：后端新建一个PageInfo<>对象，用来存后端返回给前端的信息

