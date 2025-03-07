# Docker

和什么样的系统无关，主要就是一个云服务器上，安装了Docker这样的一个工具，然后我们自己就可以在上面运行一个镜像，镜像可以是自己整的，也可以是DockerHub上下载过来的，运行一个容器来启动这个镜像，镜像中包含的功能或者内容就能够通过Docker暴露端口给所有人来访问调用

## 镜像

```shell
#查看运⾏中的容器
docker ps
 #查看所有容器
docker ps -a
#查看所有容器的id
docker ps -aq
 #搜索镜像
docker search nginx
 #下载镜像
docker pull nginx
 #下载指定版本镜像
docker pull nginx:1.26.0
 #查看所有镜像
docker images
 #删除指定id的镜像
docker rmi e784f4560448
 #运⾏⼀个新容器
docker run nginx
 #停⽌容器
docker stop keen_blackwell
 #启动容器
docker start 592(id)
 #重启容器
docker restart 592(id前三位)
 #查看容器资源占⽤情况
docker stats 592
 #查看容器⽇志
docker logs 592
 #删除指定容器
docker rm 592
 #强制删除指定容器
docker rm -f 592
 # 后台启动容器
docker run -d --name mynginx(自己起的名字) nginx
 # 后台启动并暴露端⼝,如果不暴露的话，只能到对应主机上去访问这个端口才会有页面返回，暴露出去的端口号要唯一
docker run -d --name mynginx -p 80:80 nginx
 # 进⼊容器内部
docker exec -it mynginx /bin/bash
 # 提交容器变化打成⼀个新的镜像
docker commit -m "update index.html" mynginx mynginx:v1.0
 # 保存镜像为指定⽂件，-o表示写出成一个文件名的文件
docker save -o mynginx.tar mynginx:v1.0
 # 删除多个镜像
docker rmi bde7d154a67f 94543a6c1aef e784f4560448
 # 加载镜像
docker load -i mynginx.tar 
# 登录docker hub
 docker login
# 重新给镜像打标签
docker tag mynginx:v1.0 leifengyang/mynginx:v1.0
# 推送镜像
docker push leifengyang/mynginx:v1.0
```

## 存储

容器和服务器之间的数据关系，相互影响

```
目录挂载:在容器外，服务器上重新写一个目录（但初始是空的），告诉docker看哪儿，下面这句话加在run后面，冒号前面这个就是容器外本机上的目录，后面的是容器中的路径
-v /app/nghtml:/usr/share/nginx/html

卷映射：默认会创建一个目录，容器中的路径下有什么，这里就有什么
-v ngconf:/etc/nginx
```

## 网络

每个容器会默认加入docker0（不支持主机域名）中，在内部有一个默认网络，会为每一个容器分配一个内部的IP

```shell
#⾃定义⽹络，容器名就是稳定域名
docker network create mynet
#容器启动时加入到自定义的网络,加在run后面
--network mynet
#读写分离，主节点来负责写，从节点来负责读，从节点和主节点保持一致
 #主节点
docker run -d -p 6379:6379 \-v /app/rd1:/bitnami/redis/data \-e REDIS_REPLICATION_MODE=master \-e REDIS_PASSWORD=123456 \--network mynet --name redis01 \
 bitnami/redis（镜像名）
 #从节点
docker run -d -p 6380:6379 \-v /app/rd2:/bitnami/redis/data \-e REDIS_REPLICATION_MODE=slave \-e REDIS_MASTER_HOST=redis01 \-e REDIS_MASTER_PORT_NUMBER=6379 \-e REDIS_MASTER_PASSWORD=123456 \-e REDIS_PASSWORD=123456 \--network mynet --name redis02 \
 bitnami/redis
```

## DockerCompose

配置文件中的元素

![image-20250307165154766](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250307165154766.png)

基本语法：

```shell
name: myblog
 services:
  mysql:
    container_name: mysql#自己起的容器名
    image: mysql:8.0#用哪个镜像
    ports:
      - "3306:3306"#外部端口：内部端口
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=wordpress
    volumes:#卷挂载
      - mysql-data:/var/lib/mysql
      - /app/myconf:/etc/mysql/conf.d
    restart: always
    networks:#要用的自定义网络
      - blog
  wordpress:
    image: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html
    restart: always#自启动
    networks:
      - blog
    depends_on:
      - mysql
 volumes:
  mysql-data:
  wordpress:
 networks:#指示一下是哪个自定义网络
  blog:
```

配置好上述yaml文件后，需要通过vim命令在docker的服务器中创建一个相同内容的文件并复制进去，然后通过

`docker compose -f compose.yaml up -d`

命令来创建一个新自定义网络，容器的名字会加上配置文件中的name

![image-20250307170121534](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250307170121534.png)

最后通过`docker compose -f compose.yaml down`可以配置相关的参数，移除对应的对象

## 制作自己的镜像

编写DockerFile文件

常见指令：

![image-20250307170956310](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250307170956310.png)

整体步骤：

1. 首先是要把自己的jar包或者其他想要搞到一个文件里的东西上传到docker服务器，通过rz实现
2. 编写DockerFile文件，在里面书写常见指令，如下：

```shell
 FROM openjdk:17#docker自己就有jdk，直接指定就行
 LABEL author=leifengyang
 COPY app.jar /app.jar#把当前目录下的jar包（前一个）放到后面这个有新容器/新系统具体路径的地方
 EXPOSE 8080#暴露给外界的端口
 ENTRYPOINT ["java","-jar","/app.jar"]
```

3. 指令`docker build -f Dockerfile -t myjavaapp:v1.0 . `-t用来指明它的具体名字和对应的版本号；一定要注意末尾的.号，表示当前目录
4. 然后myjavaapp的容器就自动启动了

镜像分层存储机制：两个镜像有相同点，则把相同的部分合在一起，作为底层，两个镜像相当于公用了同一块内存

