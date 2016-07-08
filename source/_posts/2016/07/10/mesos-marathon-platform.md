title: 基于Docker搭建多节点Mesos/Marathon

date: 2016-07-10 10:00

tags: [Docker, Mesos, Marathon]

---

**摘要:** 在之前的一篇博客中，我介绍了[基于Docker搭建单机版Mesos/Marathon](http://kiwenlau.com/2015/09/18/150918-single-mesos-docker/)，仅仅使用了单个节点。在这篇博客中，我将介绍[基于Docker搭建多节点Mesos/Marathon](http://kiwenlau.com/2016/07/10/mesos-marathon-platform/)，开发者可以使用3个节点快速地搭建一个真正的分布式容器集群系统。**服务发现**和**负载均衡**是容器集群必不可少的功能，我选择了[Marathon LB](https://github.com/mesosphere/marathon-lb)来实现。然后，我将通过部署Nginx介绍Mesos/Marathon平台的使用方法与运行原理。

<!-- more -->

- 作者: [KiwenLau](http://kiwenlau.com/)
- 日期: [2016-07-10](http://kiwenlau.com/2016/07/10/mesos-marathon-platform/)

| 节点    | IP           | 运行的容器                                      |
|:------- |:-------------| :----------------------------------------------|
| Master  | 192.168.59.1 | zookeeper, mesos_master, marathon, marathon-lb |
| Slave1  | 192.168.59.2 | mesos_slave1, nginx                                   |
| Slave2  | 192.168.59.3 | mesos_slave2, nginx                                   |

<img src="mesos-marathon-platform/mesos-marathon-platform.png" width = "500"/>

## 一. Mesos/Marathon简介

## 二. 搭建步骤

#### **1. 开启Docker daemon的监听端口**



Master, Slave1 and Slave2:

```
sudo vim /etc/default/docker
```

修改**DOCKER_OPTS**

```
DOCKER_OPTS="-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock"
```

重启Docker

```
sudo restart docker
```

#### **2. 下载Docker镜像** 

On Master:

```
sudo docker pull kiwenlau/marathonlb:1.3.0
sudo docker pull kiwenlau/marathon:1.1.1  
sudo docker pull kiwenlau/mesos:0.26.0    
sudo docker pull kiwenlau/zookeeper:3.4.8 
```

On Slave1 and Slave2:

```
sudo docker pull kiwenlau/mesos:0.26.0
```

#### **3. 下载GitHub仓库** 

On Master:

```
git clone https://github.com/kiwenlau/mesos-marathon-platform
```


#### **4. 运行容器** 

On Master:

```
cd mesos-marathon-platform
sudo ./start-containers.sh
```

网页管理:

- Mesos: [http://192.168.59.1:5050/](http://192.168.59.1:5050/)
- Marathon: [http://192.168.59.1:8080/](http://192.168.59.1:8080/)  

#### **5. 运行Nginx:** 

下载nginx镜像(Slave1和Slave2):

```
sudo docker pull nginx:1.10
```

运行Nginx(Master):

```
sudo ./run-nginx.sh 
```

访问Nginx: [http://192.168.59.1:10000/](http://192.168.59.1:10000/)