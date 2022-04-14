---
title: docker学习
date: 2022-03-05 01:21:27
tags:
---
## Docker 安装
### Docker 的基本组成
### Docker 安装
#### 环境准备
1. 需要会一点点 Liunx的基础
2. CentOS 7
3. 使用Xsheell连接远程服务器
#### 环境查看
```
# 系统内核是 3.10 以上的

[root@VM-4-11-centos ~]# uname -r
3.10.0-1160.45.1.el7.x86_64
```
```
# 系统版本查看

[root@VM-4-11-centos ~]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

#### 安装
#### 1. 卸载旧的版本
```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
#### 2. 下载安装需要的安装包
```
yum install -y yum-utils
```
#### 3. 设置镜像仓库(默认是国外的)
```
yum-config-manager \
    --add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
#### 4. 更新yum软件包索引
```
yum makecache fast
```
#### 5. 安装Docker （docker-ce社区版）
```
sudo yum install docker-ce docker-ce-cli containerd.io
```
#### 6. 启动docker
```
systemctl start docker
docker version
```
#### 7. hello-world 测试运行
```
docker run hello-world
```
#### 8. 查看下载的hello-world镜像
```
docker images
```
#### 了解：卸载docker
1. 卸载依赖
```
yum uninstall docker-ce docker-ce-cli containerd.io
```
2. 删除资源
```
rm -rf /var/lib/docker
```

## Docker的常用命令
> [docker命令文档](https://docs.docker.com/engine/reference/commandline/)
### 帮助命令
> docker version     # 显示docker的版本信息
> docker info        # 显示docker的系统信息，包括镜像和容器的数量
> docker 命令 --help # 帮助命令
### 镜像命令
#### 1. 查看本地镜像
```
docker images
```

#### 2. 搜索docker商店中的镜像
```
docker search 镜像名 [参数]
```
例子：
```
docker search mysql --filter=stars=3000
```

#### 3. docker pull 下载镜像
```
# 下载镜像 docker pull 镜像名[:tag]

docker pull mysql
```

#### 4. docker rmi 删除镜像
```
docker rmi 镜像id						# 删除指定的镜像
docker rmi -f 镜像id 					# 删除指定的镜像，（-f）强制删除
docker rmi -f 镜像id 镜像id 镜像id .... 	# 删除多个镜像
docker rmi -f $(docker images -aq)     	# 删除全部的镜像
```

### 容器命令
`docker run [可选参数] image` 
参数说明
> --name="name" 容器命令
> -d 			后台运行
> -it 			使用交互方式运行，进入容器查看内容
> -p 			指定容器的端口 -p 8080:8080
 > -p ip:主机端口:容器端口
 > -p 主机端口:容器端口
 > -p 容器端口
 > 容器端口
> -P 			随机指定端口

#### 1. 启动并进入容器
```
[root@VM-4-11-centos docker]# docker run -it centos
[root@a35f43f27331 /]# 
```

#### 2. 退出容器
```
[root@a35f43f27331 /]# exit
exit
```

#### 3. 列出所有运行的容器
```
# docker ps 命令
	# 列出当前正在运行的容器
-a  # 列出当前正在运行的容器+历史运行过的容器
-n=? # 列出最近创建的容器（?是几个）
```
#### 4. 退出容器
```
exit		# 容器停止并退出
ctrl+p+q	# 容器不停止退出
```
#### 5. 删除容器
```
docker rm 容器id							# 删除指定的容器
docker rm -f 容器id 					# 删除指定的容器，（-f）强制删除
docker rm -f 容器id 容器id 容器id .... 	# 删除多个容器
docker rm -f $(docker ps -aq)     		# 删除全部的容器
```
#### 6. 启动和停止容器的操作
```
docker start 容器id		# 启动容器
docker restart 容器id	# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id 		# 强制停止当前容器
```

### 常用其他命令

#### 1. 后台启动容器
> 命令 docker run -d 镜像名
`docker run -d centos`

#### 2. 查看日志
> docker logs -f -t --tail 容器
```
# 编写shell脚本
docker run -d centos /bin/sh -c "while true;do echo 'centos is running';sleep 1;done"
# 显示日志
-tf				# 显示日志
--tail number 	# 要显示的日志条数
[root@VM-4-11-centos ~]# docker logs -ft --tail 5 f849ac6b6b4d
```
#### 3. 查看容器中进程信息
> docker top 容器id
```
[root@VM-4-11-centos ~]# docker top f849ac6b6b4d
```
#### 4. 查看容器的元数据
> docker inspect 容器id
```
[root@VM-4-11-centos ~]# docker inspect f849ac6b6b4d
```
#### 5. 进入容器
1. docker exec -it 容器id bashShell
```
[root@VM-4-11-centos ~]# docker exec -it f849ac6b6b4d /bin/bash
[root@f849ac6b6b4d /]# 
```

2. docker attach 容器id 
```
[root@VM-4-11-centos ~]# docker attach f849ac6b6b4d
```
> 1. docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
> 2. docker attach		# 进入容器正在执行的终端，不会启动新的进程

#### 6. 从容器内拷贝文件到主机上
> docker cp 容器id:容器内路径 目的主机路径
```
# 1. 运行一个docker容器并进入
[root@VM-4-11-centos home]# docker run -it centos
[root@b623f3f58c14 /]# 

# 2. 进入home文件夹下创建一个测试文件test.sh
[root@b623f3f58c14 /]# cd home
[root@b623f3f58c14 home]# touch test.sh
[root@b623f3f58c14 home]# ls
test.sh

# 3. 退出容器
[root@b623f3f58c14 home]# exit
exit

# 4. 拷贝容器中的文件到宿主机中并查看
[root@VM-4-11-centos home]# docker cp b623f3f58c14:/home/test.sh /home
[root@VM-4-11-centos home]# ls
test.sh

```

### 总结
![docker命令图](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2Fimg_convert%2Fe86543bb903f3f989485e4cb5e3e194e.png&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1651570312&t=d9bd7f43219463af3dc82e8410dc4c16)

|命令|详细|实例|解释|
|----|-----|----|-----|
|attach|[进入容器](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=5.-,%E8%BF%9B%E5%85%A5%E5%AE%B9%E5%99%A8,-docker%20exec%20%2Dit)|docker attach 容器id|当前shell下attach链接指定的运行容器|
|cp|[拷贝](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=6.-,%E4%BB%8E%E5%AE%B9%E5%99%A8%E5%86%85%E6%8B%B7%E8%B4%9D%E6%96%87%E4%BB%B6%E5%88%B0%E4%B8%BB%E6%9C%BA%E4%B8%8A,-docker%20cp%20%E5%AE%B9%E5%99%A8)|docker cp 容器id:容器内路径 目的主机路径|从容器内拷贝文件到主机上|
|images|[查看本地镜像](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=%E9%95%9C%E5%83%8F%E5%91%BD%E4%BB%A4-,%E6%9F%A5%E7%9C%8B%E6%9C%AC%E5%9C%B0%E9%95%9C%E5%83%8F,-1)|docker images -aq|查看所有本地镜像的id|
|search|[搜索docker商店中的镜像](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=docker%20images-,%E6%90%9C%E7%B4%A2docker%E5%95%86%E5%BA%97%E4%B8%AD%E7%9A%84%E9%95%9C%E5%83%8F,-1)|docker search mysql --filter=stars=3000|搜索stars三千以上的镜像|
|pull|无|docker pull mysql|下载镜像|
|run|[run](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=docker%20run%20%5B%E5%8F%AF%E9%80%89%E5%8F%82%E6%95%B0%5D%20image)|docker run hello-world|运行容器|
|ps|[列出容器](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=%E5%85%A8%E9%83%A8%E7%9A%84%E9%95%9C%E5%83%8F-,%E5%AE%B9%E5%99%A8%E5%91%BD%E4%BB%A4,-docker%20run%20%5B%E5%8F%AF)|docker ps -a|列出全部容器|
|rm|[删除容器](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=%E5%85%A8%E9%83%A8%E7%9A%84%E9%95%9C%E5%83%8F-,%E5%AE%B9%E5%99%A8%E5%91%BD%E4%BB%A4,-docker%20run%20%5B%E5%8F%AF)|docker rm 容器id|删除容器|
|start/restart/stop/kill|[启动和停止容器](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=%E5%85%A8%E9%83%A8%E7%9A%84%E9%95%9C%E5%83%8F-,%E5%AE%B9%E5%99%A8%E5%91%BD%E4%BB%A4,-docker%20run%20%5B%E5%8F%AF)|docker start 容器id|启动和停止容器|
|logs|[查看日志](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=2.-,%E6%9F%A5%E7%9C%8B%E6%97%A5%E5%BF%97,-docker%20logs%20%2Df)|docker logs -f -t –tail 容器|显示容器运行日志|
|top|[查看容器中进程信息](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=3.-,%E6%9F%A5%E7%9C%8B%E5%AE%B9%E5%99%A8%E4%B8%AD%E8%BF%9B%E7%A8%8B%E4%BF%A1%E6%81%AF,-docker%20top%20%E5%AE%B9%E5%99%A8)|docker top 容器id|查看容器中进程信息|
|inspect|[查看容器元数据](http://127.0.0.1:4000/2022/03/05/docker%E5%AD%A6%E4%B9%A0/#:~:text=4.-,%E6%9F%A5%E7%9C%8B%E5%AE%B9%E5%99%A8%E7%9A%84%E5%85%83%E6%95%B0%E6%8D%AE,-docker%20inspect%20%E5%AE%B9%E5%99%A8)|docker inspect 容器id|查看容器的元数据|

## 容器数据卷
### 使用数据卷
#### 1. 使用命令来挂载 -v
> docker run -v 主机目录:容器内目录
```
# 1.将容器home目录挂载在宿主机/home/test目录并启动容器
[root@VM-4-11-centos home]# docker run -it -v /home/test:/home centos
[root@02fff9fa8191 /]#

# 2.在容器home目录创建ces文件
[root@02fff9fa8191 home]# touch ces
[root@02fff9fa8191 home]# ls
ces

# 3.在宿主机/home/test中查看是否存在ces文件
[root@VM-4-11-centos test]# ls
ces

# 4.停止容器并在宿主机目录下修改ces文件
[root@02fff9fa8191 home]# exit  # 在容器中退出后，容器没有提供服务自动关闭
[root@VM-4-11-centos test]# vim ces
我做了一些修改
:wq    # 保存退出

# 5.启动容器并在home目录下查看ces文件是否修改成功
[root@VM-4-11-centos home]# docker start 02fff9fa
02fff9fa
[root@VM-4-11-centos home]# docker attach 02fff9fa
[root@02fff9fa8191 /]# cd home
[root@02fff9fa8191 home]# cat ces
我做了一些修改
```

### 安装MySQL
> 利用数据卷实现数据持久化
```
# 1.获取镜像
[root@VM-4-11-centos home]# docker pull mysql:5.7

# 运行容器，做数据挂载，并为mysql设置密码
# 官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 2.自定义mysql启动
[root@VM-4-11-centos home]# docker run -d -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -p 5000:3306 --name dockermysql mysql:5.7

-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名字

# 3.测试是否可以连接
利用本地mysql连接服务器上的数据库
连接名：docker-mysql
主机：ip地址
端口：5000
用户名：root
密码：123465

# 4.创建数据库并在服务器挂载目录查看是否生成文件
```

### 具名挂载和匿名挂载
>-v 容器目录 			# 匿名挂载
>-v 卷名:容器目录		# 具名挂载
>-v 宿主机目录:容器目录	# 路径挂载

#### 匿名挂载
>-v 容器目录 			# 匿名挂载
```
# 1.创建匿名卷
[root@VM-4-11-centos test]# docker run -v /ect/nginx -d -P nginx

# 2.列出全部卷名
[root@VM-4-11-centos test]# docker volume ls
local     01f7192eff5b5d017767feffb5cea39cdf8ec857f517314fb86b305ba3d110ef

# 3.查看卷名详细
[root@VM-4-11-centos test]# docker volume inspect 01f7192eff5b5d017767feffb5cea39cdf8ec857f517314fb86b305ba3d110ef
[
    {
        "CreatedAt": "2022-04-04T17:04:46+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/01f7192eff5b5d017767feffb5cea39cdf8ec857f517314fb86b305ba3d110ef/_data",
        "Name": "01f7192eff5b5d017767feffb5cea39cdf8ec857f517314fb86b305ba3d110ef",
        "Options": null,
        "Scope": "local"
    }
]
```

#### 具名挂载
>-v 卷名:容器目录

```
# 1.创建具名卷
[root@VM-4-11-centos test]# docker run --name nginx02 -d -P -v test-v:/ect/nginx nginx

# 2.列出所有卷
[root@VM-4-11-centos test]# docker volume ls
local     test-v

# 3.查看卷的详细内容
[root@VM-4-11-centos test]# docker volume inspect test-v
[
    {
        "CreatedAt": "2022-04-04T17:12:34+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/test-v/_data",
        "Name": "test-v",
        "Options": null,
        "Scope": "local"
    }
]

# 4.进入宿主机/var/lib/docker/volumes/目录查看全部卷
[root@VM-4-11-centos test]# cd /var/lib/docker/volumes
[root@VM-4-11-centos volumes]# ls
01f7192eff5b5d017767feffb5cea39cdf8ec857f517314fb86b305ba3d110ef  c91bc719194837821b5d18745ccb1f9a67cc8b61b3c15cade88851a5538687bf
97d977d14e26e88b5753cebea7b099836dea7f534ac4657363b9022690a5aeb3  d076838211861cc452a72b0380a3f71113f6fc2e56e01ec53845c80c44ac44ec
9a3bf71a59a2bc358f7e250721ec109287ce7ae8ef04d43e468ebf4277b6408d  ef8f0af8705bec1b87e6fc12633f1264114fa391375e0d8faeb093a238743d3b
backingFsBlockDev                                                 metadata.db
c44a9c6f0fcb7f0776281800526b971f005e1359cc4a013be8ad9209d52026e7  test-v
c8b7d55ada286c04514b89d4ebde23c944025fbcef2921c7e1fbeabfacda45ff
```

#### ro和rw修改读写权限
> ro readonly 	#只读
> rw readwrite	#可读可写（默认）

一旦这个设置了容器权限，就无法在容器中修改内容，只能从宿主机中修改
```
docker run --name nginx02 -d -P -v /home/test-v:/ect/nginx:ro nginx
docker run --name nginx02 -d -P -v /home/test-v:/ect/nginx:rw nginx

# 在宿主机/home/test-v中创建ces1文件兵查看
[root@VM-4-11-centos test-v]# touch ces
[root@VM-4-11-centos test-v]# ls
ces

# 在容器的/ect/nginx目录下创建文件ces2失败
root@d4b1f86d8f2e:/ect/nginx# touch ces2
touch: cannot touch 'ces2': Read-only file system
```

#### 初识Dockerfile
```
# 1.创建dockerfile1文件
[root@VM-4-11-centos test1]# vim dockerfile1

# 文件内容
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "----end-----"
CMD /bin/bash

# 2.生成镜像
docker build -t iheng/centos:1.0 -f dockerfile1 .
# 使用docker build --help查看使用方法
docker build [OPTIONS] PATH | URL | -

# 3.运行容器并进入volume01目录下创建test.sh
[root@VM-4-11-centos test1]# docker run -it iheng/centos:1.0 /bin/bash
[root@4ac0ae3d452a /]# cd volume01
[root@4ac0ae3d452a volume01]# touch test.sh
[root@4ac0ae3d452a volume01]# ls
test.sh

# 4.退出容器，查看volume01挂载路径并进入查看文件
[root@VM-4-11-centos test1]#docker inspect 4ac0ae3d452a
 "Mounts": [
            {
                "Type": "volume",
                "Name": "82cb87e063928d1b596893912edc3b6325c25b37eaabe2b83a08e217820eab40",
                "Source": "/var/lib/docker/volumes/82cb87e063928d1b596893912edc3b6325c25b37eaabe2b83a08e217820eab40/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "f514502b70e30b804a600e3d076349591bdcb095fb22ddf8d6f47bf42a32ba91",
                "Source": "/var/lib/docker/volumes/f514502b70e30b804a600e3d076349591bdcb095fb22ddf8d6f47bf42a32ba91/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
[root@VM-4-11-centos test1]# cd /var/lib/docker/volumes/82cb87e063928d1b596893912edc3b6325c25b37eaabe2b83a08e217820eab40/_data
[root@VM-4-11-centos _data]# ls
test.sh
```

#### 数据卷容器
>--volumes-from 容器id
```
# 1.创建新容器，通过--volume-from与上一个容器挂载在同一个目录中
[root@VM-4-11-centos _data]# docker run -it --name docker02 --volumes-from 4ac0ae3d452a iheng/centos:1.0

# 2.进入volume查看文件是否存在
[root@95cde1c41a89 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02
[root@95cde1c41a89 /]# cd volume01
[root@95cde1c41a89 volume01]# ls
test.sh
```

## DockerFile
### DockerFile构建过程
#### 基础知识
1. 每个保留关键字（指令）都必须是大写字母
2. 执行从上到下顺序执行
3. `#` 表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交
![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic2.zhimg.com%2Fv2-025095f80a29a477651db5e3a33060d5_b.jpg&refer=http%3A%2F%2Fpic2.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1651660440&t=435c49d7a70e2f1d8725924a68b4a1f1)

#### DockerFile的指令

|指令|解释|
|----|----|
|FROM|基础镜像，一切从这里开始构建|
|MAINTAIINER|镜像是谁写的，姓名+邮箱|
|RUN|镜像构建的时候需要运行的命令|
|ADD|将本地文件加入镜像文件中|
|WORKDIR|镜像的工作目录|
|VOLUME|挂载的目录|
|EXPOSE|保留端口配置|
|CND|指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代|
|ENTRYPOINT|指定这个容器启动的时候要运行的命令，可以追加命令|
|ONBUILD|当构建一个被继承DockerFile 这个时候就会运行ONBUILD的指令|
|COPY|类似ADD，将我们文件拷贝到镜像中|
|ENV|构建的时候设置环境变量|

![](https://img1.baidu.com/it/u=1860678141,412445329&fm=253&fmt=auto&app=138&f=PNG?w=500&h=294)

#### 实战测试
```shell
# 编写dockerfile文件
[root@VM-4-11-centos test1]# vim dockerfile 

FROM centos:7
MAINTAINER iheng<12334@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash

# 生成镜像
[root@VM-4-11-centos test1]# docker build -t ces/centos:0.1 .

# 查看镜像生成历史
[root@VM-4-11-centos test1]# docker history 7532b3fd084f
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
7532b3fd084f   3 hours ago    /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B        
7dfdd3324b03   3 hours ago    /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B        
4696ff255a81   3 hours ago    /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B        
50250d67eaf1   3 hours ago    /bin/sh -c #(nop)  EXPOSE 80                    0B        
a1db5e29cdad   3 hours ago    /bin/sh -c yum -y install net-tools             161MB     
20f2d1515e88   3 hours ago    /bin/sh -c yum -y install vim                   216MB     
20d28c9e35de   3 hours ago    /bin/sh -c #(nop) WORKDIR /usr/local            0B        
77967854ad5b   3 hours ago    /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B        
6c97428615ba   3 hours ago    /bin/sh -c #(nop)  MAINTAINER iheng<12334@qq…   0B        
eeb6ee3f44bd   6 months ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      6 months ago   /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B        
<missing>      6 months ago   /bin/sh -c #(nop) ADD file:b3ebbe8bd304723d4…   204MB  

# 运行容器并检测是否能使用vim命令
[root@VM-4-11-centos test1]# docker run -it --name centosyum ces/centos:0.1 /bin/bash
[root@ae9fe9d14bff local]# vim t
```

#### CMD和ENTRYPOINT区别
> CMD 			# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
> ECTRYPOINT	# 指定这个容器启动的时候要运行的命令，可以追加命令

**测试cmd**
```shell
# 编写 dockerfile文件
[root@VM-4-11-centos test1]# vim dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

# 构建镜像
docker build -f dockerfile-cmd-test -t cmdcentos .

# run运行，ls -a命令生效 
docker run ac563351c01
.
..
.dockerenv
bin
dev
etc
home

# 追加一个命令-l ls -al
[root@VM-4-11-centos test1]# docker run ac563351c01 -l
docker: Error response from daemon: failed to create shim: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container: context canceled 
# cmd的情况下 -l替换了CMD["ls","-a"]命令，-l不是命令，所以报错
```
**测试ENTRYPOINT**
```shell
# 编写dockerfile文件
[root@VM-4-11-centos test1]# vim dockerfile-cmd-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]

# 构建镜像
[root@VM-4-11-centos test1]# docker build -f dockerfile-cmd-entrypoint -t entrypoint .

# 追加命令运行，运行成功
[root@VM-4-11-centos test1]# docker run ab09672b1 -l
total 56
drwxr-xr-x   1 root root 4096 Apr  5 08:22 .
drwxr-xr-x   1 root root 4096 Apr  5 08:22 ..
-rwxr-xr-x   1 root root    0 Apr  5 08:22 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  340 Apr  5 08:22 dev
drwxr-xr-x   1 root root 4096 Apr  5 08:22 etc
drwxr-xr-x   2 root root 4096 Nov  3  2020 home
```
### 发布镜像
[docker官方教程](https://docs.docker.com/docker-hub/#:~:text=%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8-,Docker%20Hub%20%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8,-%E9%A2%84%E8%AE%A1%E9%98%85%E8%AF%BB%E6%97%B6%E9%97%B4)
### 总结
![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2Fimg_convert%2F5c0c425b1d4bf7e4cca244d91b920805.png&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1651745518&t=acf6ffe103b3e442034288710d0d296a)

## Docker网络
### 理解Docker0
```shell
# 在宿主机中查看网卡
[root@VM-4-11-centos test2]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 52:54:00:87:e3:98 brd ff:ff:ff:ff:ff:ff
    inet 10.0.4.11/22 brd 10.0.7.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe87:e398/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:bd:9c:b1:27 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:bdff:fe9c:b127/64 scope link 
       valid_lft forever preferred_lft forever

# 创建一个容器，在宿主机ping容器
docker run -d -it tomcat /bin/bash 
ip addr 	查看容器的ip地址

# 退出容器，ping容器ip地址
ping 容器ip

# 两个容器互ping
# 再创建一个容器，并ping第一个容器
```

### --link
```
# 创建三个容器
[root@VM-4-11-centos test2]# docker run -d -it --name tomcat03 tomcat
56de3b2fb3814f44cd62f7ed6f474e2eee15b6f43b5c1f263bc32d259c1a5062
[root@VM-4-11-centos test2]# docker run -d --name tomcat04 --link tomcat03 tomcat
d08335f05bfa8568b706da6a20e1fcf5caca8a0a14cad2946c7edaf60e28224f
[root@VM-4-11-centos test2]# docker run -d -it --name tomcat02 tomcat
9c037ebbebd0a6b2d87795502b1a3f3f3f7699bb51497642d9e25782e94a37db

# 容器4 分别ping容器2、容器3，有不同的结果
[root@VM-4-11-centos test2]# docker exec -it tomcat04 ping tomcat02
ping: tomcat02: Name or service not known
[root@VM-4-11-centos test2]# docker exec -it tomcat04 ping tomcat03
PING tomcat03 (172.17.0.2) 56(84) bytes of data.
64 bytes from tomcat03 (172.17.0.2): icmp_seq=1 ttl=64 time=0.122 ms
64 bytes from tomcat03 (172.17.0.2): icmp_seq=2 ttl=64 time=0.071 ms

# 容器3反过来ping容器4，查看是否能够ping通
[root@VM-4-11-centos test2]# docker exec -it tomcat03 ping tomcat04
ping: tomcat04: Name or service not known

# 查看容器4和容器3的网络配置
[root@VM-4-11-centos test2]# docker exec -it tomcat04 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.2	tomcat03 56de3b2fb381
172.17.0.3	d08335f05bfa

[root@VM-4-11-centos test2]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.2	56de3b2fb381
```

### 自定义网络
#### 查看所有的docker网络

	[root@VM-4-11-centos test2]# docker network ls
	NETWORK ID     NAME                          DRIVER    SCOPE
	cbdac67a9a74   accountpool_default           bridge    local
	d0b6580a3a50   bridge                        bridge    local
	8ef2274420df   host                          host      local
	5be787d9aba5   none                          null      local
	7aeb1ea8d9f8   proxypool_default             bridge    local
	b2f6e490e3f0   scrapycompositedemo_default   bridge    local
	
```shell
# 创建自己的网络
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

--driver 连接方式
--subnet 子网
--gateway 网关

# 在自定义的网络下创建两个容器
docker run -d -it --network mynet --name tomcat01 tomcat
docker run -d -it --network mynet --name tomcat02 tomcat

# 测试docker01 来ping docker02是否能够ping通
[root@VM-4-11-centos test2]# docker exec -it tomcat01 ping tomcat02
PING tomcat02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.085 ms
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.057 ms
```

### 网络连通
> 利用docker network connect 连接容器和网络

```
# 创建两个新的容器
[root@VM-4-11-centos test2]# docker run -d --name tomcat03 tomcat
[root@VM-4-11-centos test2]# docker run -d --name tomcat04 tomcat

# 将tomcat04和mynet连通
[root@VM-4-11-centos test2]# docker network connect mynet tomcat04

# 容器3、4分别ping容器2
[root@VM-4-11-centos test2]# docker exec -it tomcat03 ping tomcat02
ping: tomcat02: Name or service not known
[root@VM-4-11-centos test2]# docker exec -it tomcat04 ping tomcat02
PING tomcat02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.062 ms
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.047 ms
```

## Docker Compose
### 初体验
#### 编写app.py
```py
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host="redis",port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr("hits")
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -=1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return "Hello World!{}".format(count)
```

#### 编写requirements.txt
为app.py提供依赖
```txt
flask
redis
```
#### 编写Dockerfile
```
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask","run"]
```

#### 编写Docker-compose
```
version: "3.9"
services: 
  web: 
    build: .
    ports: 
      - "5000:5000"
  redis: 
    image: "redis:alpine"
```