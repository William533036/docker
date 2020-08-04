docker

# 1、 Docker Image概述

## 什么是Image
	文件和meta data的集合(root filesystem)
	分层的，并且每一层都可以添加改变删除文件，成为一个新的image
	不同image可以共享相同的layer
	Image本身是read-only的


容器（container）和镜像（image）之间的区别
![](http://dockone.io/uploads/article/20190626/401405294bff9c66a1ec0e842391c856.png)
## docker引擎是一个c/s结构的应用，主要组件见下图
![](http://hainiubl.com/images/2016/engine-components-flow.png)
## Docker构架
Docker使用C/S架构，Client 通过接口与Server进程通信实现容器的构建，运行和发布。client和server可以运行在同一台集群，也可以通过跨主机实现远程通信。

![](http://hainiubl.com/images/2016/architecture.jpg)

## DIY一个Base Image
DOckerfile
	FROM STRATCH
	ADD hello /
	CMD ["/hello"]
docker bulit -t

## 什么是Container
	通过Image创建(Copy)
	在Image layer之上建立一个container layer(可读写)
	类比面向对象: 类和实例
	Image负责App的存储和分发，Container负责运行App

# 2、docker --help: 45 commands
## 12 images 相关的命令：

docker pull获取所有repo上的docker images

docker images列出所有的本地的docker images

docker search 查找docker images

docker build/commit/import/load 生成docker images, build是根据docker file编译生成，commit是提交container的change生成一个新的, import是读取tarball生成, load是读取stdout生成

docker save 保存docker image到a tar archive (streamed to STDOUT）

docker push 提交docker images

docker tag 给要提交到repo里面的image打tag

docker rmi 删除images

docker history 查看image的历史

Docker Container相关的命令：

## 14个常用container指令：

create :创建新的container

start/restart: 启动/重新启动container

stop/kill: 停止或者杀掉container

rm:删除container

exec/run: 在 container内部执行指令

cp: 把localsystem和container之间cp文件

attach: attach到当前正在运行的container

rename: container重新命名

ps: 列出所有container

update: 更新container的配置

## 7个containder进程相关：

pause/unpause: 暂停/运行container内部所有进程

logs:查看container的日志

stats: 查看container资源使用情况

port:  列出container的端口映射情况

top: 显示当前container当前正在运行的进程

wait: 等待这个container正常推出并返回退出码

## 2个container文件系统相关：

diff: 检查container文件系统的变化

export: 导出container到tar

## 1个container和images共用的检查指令：

inspect: 返回container或者image的底层信息

# 3、 Dockerfile语法梳理及实践
## FROM
FROM STRATCH   制作base image
FROM centos       使用base image
## LABLE
LABLE maintainer="huangyingjie011@163.com"
LABLE version="1.0"
LABLE description="这里是描述"
LABLE Metadata不可少，类似代码的注释
## RUN
RUN yum update && yum install -y vim \
        python-dev  #反斜线换行，注意清理cache
## WORKDIR
WORKDIR /test #若果没有会自动创建
WORDDIR demo
RUN pwd #输出结果应该是/test/demo

用WORKDIR，不要用RUN cd，尽量使用绝对路径
## ADD 和 COPY
ADD hello /
ADD test.tar.gz / #添加到根目录并解压

WORKDIR /root
ADD hello test/ #/root/test/hello

添加远程文件/目录请使用curl或者wget

## ENV
EVN MYSQL_VERSION 5.6 #设置常量
RUN apt-get install -y mysql-server="{MYSQL_VERSION}" \
&& rm -rf /var/lib/apt/list/* #引用常量

尽量使用EVN增加可维护性

## RUN vs CMD vs ENTRYPOINT
RUN
执行命令并创建新的Image layer
CMD
设置容器启动后默认执行的命令和参数
有多个CMD，每次只执行最后一个
如果docker run 指定了其他命令，CMD命令会被忽略
ENTRYPOINT
设置容器启动时运行的命令
让容器以应用程序或者服务的形式运行
不会被忽略，一定执行
最佳实践
COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld"]

## shell和Exec格式
shell格式
RUN echo "Hello World!"
Exec格式
RUN ["/bin/echo", "Hello World!"]

# 4、镜像的发布
## 1、直接push
1、在docker hub上注册账号
2、登录后可以看到自己push的镜像
3、执行：docker login
4、docker push imagename:tagname
## 2、通过发布Dockerfile

# 5、镜像实践
FROM python:3.7
LABLE  "maintainer=huangyingjie011@163.com"
RUN pip  install flask
COPY app.py /app/
WORKDIR /app
EXPOSE 5000
CMD ["python", "app.py"]

docker build -t hungyingjie/flask-hello-world .
docker run -d 在后台运行
docker run -d hungyingjie/flask-hello-world 
docker exec -ti  交互式的进入容器
docker exec -ti fdasfasdf /bin/bash
docker exec -ti fdasfasdf ip a
docker exec -ti fdasfasdf python
输出所有退出状态的容器
docker rm $(docker ps -aq)
查看容器详情
docker inspect

# 6、资源限制
docker run -vm 1  一个进程  --cpu-share 设置cpu --memory 设置内存