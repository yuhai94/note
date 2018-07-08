# Etcd介绍

## 一、Etcd简介

![etcd Logo](assets\etcd.svg)

etcd是一个分布式键值存储系统，旨在快速可靠地保存和提供对关键数据的访问。它通过分布式锁定，领导者选举和写入障碍等实现可靠的分布式协调。etcd集群旨在实现高可用性和永久数据存储和检索。 

### 特点

etcd是一个分布式可靠的键值存储系统，面向分布式系统的最关键数据。受到 [Apache ZooKeeper](http://zookeeper.apache.org/) 项目和 [doozer](https://github.com/ha/doozerd) 项目的启发，etcd 在设计的时候重点考虑了下面四个要素： 

- *简单*：具有定义良好、面向用户的API（gRPC）
- *安全*：具有支持客户端证书身份验证的自动TLS
- *快速*：支持并发 10 k/s 的写操作 
- *可靠*：基于raft一致性算法实现的分布式

### 版本

目前etcd有v2和v3两个版本



### 源码

etcd采用Go语言编写，其源码可在github上获取：[github.com/coreos/etcd](https://github.com/coreos/etcd)

## 二、Etcd使用（7.7&7.8）

### 1. etcd 跑起来

#### 1) 直接使用发行版

github上etcd项目[https://github.com/coreos/etcd/releases]提供预先编译好的二进制文件，支持OSX, Linux, Windows等系统:

![1530949747856](assets\1530949747856.png)

根据自己的系统，下载对应的版本的文件，解压之后，可以看到有以下两个目录：

```bash
~#ls
总用量 34536
drwxrwxrwx 0 root root     4096 7月   7 15:52 ./
drwxrwxrwx 0 root root     4096 7月   7 15:52 ../
drwxrwxrwx 0 root root     4096 7月   7 15:52 default.etcd/       #etcd默认数据存储位置
drwxrwxrwx 0 root root     4096 6月  16 00:56 Documentation/      #etcd相关说明文档
-rwxrwxrwx 1 root root 19287584 7月   7 15:52 etcd*               #etcd可执行文件
-rwxrwxrwx 1 root root 16018720 7月   7 15:52 etcdctl*            #etcd对应的官方客户端
-rwxrwxrwx 1 root root    38864 7月   7 15:52 README-etcdctl.md*
-rwxrwxrwx 1 root root     7262 7月   7 15:52 README.md*
-rwxrwxrwx 1 root root     7855 7月   7 15:52 READMEv2-etcdctl.md*

```

执行下面命令即可拉起一个etcd：

```bash
~#./etcd
2018-07-07 15:59:17.153946 I | etcdmain: etcd Version: 3.3.8
2018-07-07 15:59:17.155219 I | etcdmain: Git SHA: 33245c6b5
2018-07-07 15:59:17.156422 I | etcdmain: Go Version: go1.9.7
2018-07-07 15:59:17.157521 I | etcdmain: Go OS/Arch: linux/amd64
2018-07-07 15:59:17.158397 I | etcdmain: setting maximum number of CPUs to 4, total number of available CPUs is 4
2018-07-07 15:59:17.159188 W | etcdmain: no data-dir provided, using default data-dir ./default.etcd
2018-07-07 15:59:17.160376 N | etcdmain: the server is already initialized as member before, starting as etcd member...
2018-07-07 15:59:17.165210 I | embed: listening for peers on http://localhost:2380
2018-07-07 15:59:17.166045 I | embed: listening for client requests on localhost:2379
2018-07-07 15:59:17.190039 I | etcdserver: name = default
2018-07-07 15:59:17.190524 I | etcdserver: data dir = default.etcd
2018-07-07 15:59:17.191184 I | etcdserver: member dir = default.etcd/member
2018-07-07 15:59:17.191743 I | etcdserver: heartbeat = 100ms
2018-07-07 15:59:17.192279 I | etcdserver: election = 1000ms
2018-07-07 15:59:17.192821 I | etcdserver: snapshot count = 100000
2018-07-07 15:59:17.196225 I | etcdserver: advertise client URLs = http://localhost:2379
2018-07-07 15:59:17.199735 I | etcdserver: restarting member 8e9e05c52164694d in cluster cdf818194e3a8c32 at commit index 4
2018-07-07 15:59:17.202426 I | raft: 8e9e05c52164694d became follower at term 2
2018-07-07 15:59:17.203195 I | raft: newRaft 8e9e05c52164694d [peers: [], term: 2, commit: 4, applied: 0, lastindex: 4, lastterm: 2]
2018-07-07 15:59:17.216444 W | auth: simple token is not cryptographically signed
2018-07-07 15:59:17.220881 I | etcdserver: starting server... [version: 3.3.8, cluster version: to_be_decided]
2018-07-07 15:59:17.225434 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
2018-07-07 15:59:17.227072 N | etcdserver/membership: set the initial cluster version to 3.3
2018-07-07 15:59:17.227749 I | etcdserver/api: enabled capabilities for version 3.3
2018-07-07 15:59:18.906687 I | raft: 8e9e05c52164694d is starting a new election at term 2
2018-07-07 15:59:18.907352 I | raft: 8e9e05c52164694d became candidate at term 3
2018-07-07 15:59:18.908975 I | raft: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 3
2018-07-07 15:59:18.910082 I | raft: 8e9e05c52164694d became leader at term 3
2018-07-07 15:59:18.910935 I | raft: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 3
2018-07-07 15:59:18.917305 I | etcdserver: published {Name:default ClientURLs:[http://localhost:2379]} to cluster cdf818194e3a8c32
2018-07-07 15:59:18.919571 I | embed: ready to serve client requests
2018-07-07 15:59:18.922782 N | embed: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!

```

该etcd进程以server的形式存在，进程拉起的时候，通过日志我们可以获取到一下信息：

- `default.etcd`：数据存储位置，因为etcd启动时不指定实例名，默认使用`default`作为实例名
- `http://localhost:2380`：peer监听端口，用于与集群中的其他etcd节点建立连接
- `http://localhost:2379`：client监听端口，用于与监听etcd client的连接请求

使用官方提供的etcdctl可以便捷的对etcd进行操作

**v2方式：**

```bash
~#./etcdctl mk testkey testvalue
testvalue
~#./etcdctl get testkey
testvalue
```

**v3方式：**(执行etcdctl前，先初始化环境变量`ETCDCTL_API`为`3`)

```bash
~# ETCDCTL_API=3 ./etcdctl put testkey1 testvalue1
OK
~# ETCDCTL_API=3 ./etcdctl get testkey1
testkey1
testvalue1
```

etcdctl在连接的时候，如果不指定server的地址，默认连接到`http://localhost:2379`

#### 2）源码编译

1.etcd使用Go语言实现，在编译之前必须先安装[Go](https://golang.org/)工具，版本要求1.9+以上:

```bash
~#go version
go version go1.10.1 linux/amd64
```

2.从Github上面下载源码， linux环境下执行源码目录下的`build`文件，即可编译

```bash
~# cd etcd
~# ./build
```

3.编译完成之后，在bin目录下即可看到编译生成的二进制文件：

```bash
~# cd bin/
~# ls
etcd  etcdctl
```

### 2. etcd client（v2 && v3）

#### 1) cmd方式



etcdctl 命令行参数介绍

#### 2) restconf方式

postman, curl，基于curl开发的客户端，linu

#### 3) grpc方式

go etcd client

java etcd client

restconf call grpc

### 3. etcd server

#### 1) 单节点使用

#### 2) 集群部署

#### 3) 容器下部署

## 三、Etcd原理介绍

### 1.一致性

### 2.持久性

## 四、Etcd使用场景（7.14&7.15）

### 1. 服务注册发现

## 五、关键代码分析（7.21&7.22）

### 1.代码结构

### 2.相关开源代码

### 3.重要代码分析

## 六、相似软件比较（7.29）

## 七、参考资料

 

 