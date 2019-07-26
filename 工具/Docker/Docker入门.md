# Docker简介

Docker是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。



# Docker基本架构

- docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；
- docker客户端(Client)：连接docker主机进行操作；
- docker仓库(Registry)：用来保存各种打包好的软件镜像；
- docker镜像(Images)：软件打包好的镜像；放在docker仓库中；
- docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用



# 命令

镜像相关

- docker search 镜像名
- docker pull 镜像名:版本
- docker images
- docker rmi  镜像id

容器相关：

```shell
# 1、搜索镜像
docker search tomcat
# 2、拉取镜像
docker pull tomcat
# 3、根据镜像启动容器
docker run --name mytomcat -d tomcat:latest
# 4、docker ps  
查看运行中的容器
# 5、 停止运行中的容器
docker stop  容器的id
# 6、查看所有的容器
docker ps -a
# 7、启动容器
docker start 容器id
# 8、删除一个容器
 docker rm 容器id
# 9、启动一个做了端口映射的tomcat
# -d：后台运行
# -p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口
docker run -d -p 8888:8080 tomcat
# 10、为了演示简单关闭了linux的防火墙
service firewalld status # 查看防火墙状态
service firewalld stop： # 关闭防火墙
# 11、查看容器的日志
docker logs container-name/container-id

#更多命令参看
#https://docs.docker.com/engine/reference/commandline/docker/
#可以参考每一个镜像的文档
```





# Docker Hub 镜像缓存

对于使用 upstart 的系统（Ubuntu 14.04、Debian 7 Wheezy），在配置文件 `/etc/default/docker`中的 `DOCKER_OPTS` 中配置Hub地址：

```
DOCKER_OPTS="--registry-mirror=https://docker.mirrors.ustc.edu.cn/"
```

重新启动服务:

```
sudo service docker restart
```



对于使用 systemd 的系统（Ubuntu 16.04+、Debian 8+、CentOS 7）， 在配置文件 `/etc/docker/daemon.json` 中加入：

```
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```

重新启动 **docker**d：

```
sudo systemctl restart docker
```