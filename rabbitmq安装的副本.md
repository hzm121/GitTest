**首先获取rabbit镜像：**

```
docker pull rabbitmq:3.7.7-management
```

**创建并运行容器：**

```
docker run -d --hostname my-rabbit --name rabbit -p 8080:15672 rabbitmq:management
--hostname：指定容器主机名称
--name:指定容器名称
-p:将mq端口号映射到本地

或在运行时设置用户和密码
docker run -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:management
15672：控制台端口号
5672：应用访问端口号
```

**查看rabbit运行状况：**

```
docker logs rabbit
```

![img](http://www.iamlintao.com/wp-content/uploads/2019/04)容器运行正常，使用http://localhost:15672可以访问rabbit控制台

### **以下方法是创建带有目录映射的方法：**

1、docker下安装rabbitmq命令（不带目录映射）：

docker run -itd –name rabbit  -p 1884:1883 -p 5673:5672 -p 15673:15672 -p 25673:25672 -p 61614:61613 docker.io/rabbitmq:3-management

说明: rabbitmq:3-management是带有web管理功能的；lasted虽然是最近版，不一定带有web管理功能

2、配置实例：

192.168.0.238上docker实例：容器名：rabbit；

初始化默认账号：guest / guest

3、如何把容器中rabbitmq配置文件、数据存储目录、日志目录映射处理：

（1）首先创建一个没有目录映射的docker容器：

docker run -itd –name rabbit -p 1884:1883 -p 5673:5672 -p 15673:15672 -p 25673:25672 -p 61614:61613 docker.io/rabbitmq:3-management

（2）进入docker查看rabbitmq的相关目录：

docker exec -it rabbit /bin/bash

配置文件目录：**/etc/rabbitmq**

数据存储目录：/var/lib/rabbitmq

日志目录：/var/log/rabbitmq

（3）把（2）中的三个目录从容器中复制到宿主机：

mkdir -p /home/rabbitmq/lib

mkdir -p /home/rabbitmq/etc

mkdir -p /home/rabbitmq/log

docker cp -a docker cp -a 容器ID:/var/lib/rabbitmq /home/rabbitmq/lib/

docker cp -a 容器ID:/etc/rabbitmq /home/rabbitmq/etc/

docker cp -a 容器ID:/var/log/rabbitmq /home/rabbitmq/log/

宿主机上创建rabbitmq用户

useradd rabbitmq

cd /home/

chmod -Rf rabbitmq:rabbitmq rabbitmq/

（4）目录映射：

-v /home/rabbitmq/etc/rabbitmq:/etc/rabbitmq -v /home/rabbitmq/lib/rabbitmq:/var/lib/rabbitmq -v /home/rabbitmq/log/rabbitmq/:/var/log/rabbitmq

4、创建带有目录映射的容器

docker run -itd –name rabbit -v /home/rabbitmq/etc/rabbitmq:/etc/rabbitmq -v /home/rabbitmq/lib/rabbitmq:/var/lib/rabbitmq -v /home/rabbitmq/log/rabbitmq/:/var/log/rabbitmq -p 1884:1883 -p 5673:5672 -p 15673:15672 -p 25673:25672 -p 61614:61613 docker.io/rabbitmq:3-management