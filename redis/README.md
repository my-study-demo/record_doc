Redis集群搭建与简单使用
=====
用一台虚拟机模拟6个节点，创建出3 master、3 salve 环境。
版本：redis-3.2.11
系统：centos7


安装过程

1、下载并解压
-----
下载：https://redis.io/download
````bash
    cd /usr/local
    mkdir software
    mkdir redis  #将下载好的安装文件上传到该目录
    tar -zxvf redis-3.2.11.tar.gz
 ````
 或通过wget方式下载
 ```bash
 wget http://download.redis.io/releases/redis-3.2.11.tar.gz
 ````
 
2、编译安装
-----

```bash
    cd redis-3.2.11
    make && make install
```   

3、将 redis-trib.rb 复制到 /usr/local/bin 目录下
---

```bash
    cd src
    cp redis-trib.rb /usr/local/bin/
```

4、创建 Redis 节点
---

首先在本机器上 /root/software/redis-3.2.4 目录下创建 redis_cluster 目录
```bash
    mkdir redis_cluster
```
在 redis_cluster 目录下，创建名为7000、7001、7002、7003、7004、7005的目录，并将 redis.conf 拷贝到这三个目录中
```bash
    cd redis_cluster
    mkdir 7000 7001 7002 7003 7004 7005
    cd /usr/local/software/redis/redis-3.2.11目录
    cp redis.conf redis_cluster/7000
    cp redis.conf redis_cluster/7001
    cp redis.conf redis_cluster/7002
    cp redis.conf redis_cluster/7003
    cp redis.conf redis_cluster/7004
    cp redis.conf redis_cluster/7005
```
分别修改这三个配置文件，修改如下内容

```bash
    port  7000                                 //端口7000,7002,7003
    bind 本机ip                                //默认ip为127.0.0.1 需要改为其他节点机器可访问的ip 否则创建集群时无法访问对应的端口，无法创建集群
    daemonize    yes                          //redis后台运行
    pidfile  /var/run/redis_7000.pid          //pidfile文件对应7000,7001,7002
    cluster-enabled  yes                      //开启集群  把注释#去掉
    cluster-config-file  nodes_7000.conf      //集群的配置  配置文件首次启动自动生成 7000,7001,7002
    cluster-node-timeout  15000               //请求超时  默认15秒，可自行设置
    appendonly  yes                           //aof日志开启  有需要就开启，它会每次写操作都记录一条日志　
```

5、启动各个节点
---
```bash
    redis-server redis_cluster/7000/redis.conf
    redis-server redis_cluster/7001/redis.conf
    redis-server redis_cluster/7002/redis.conf
    redis-server redis_cluster/7003/redis.conf
    redis-server redis_cluster/7004/redis.conf
    redis-server redis_cluster/7005/redis.conf
```

6、检查 redis 启动情况
---
```bash
    ps -ef | grep redis
    netstat -tnlp | grep redis        #netstat命令需要安装
```

7、创建集群
---
Redis 官方提供了 redis-trib.rb 这个工具，就在解压目录的 src 目录中，第三步中已将它复制到 /usr/local/bin 目录中，可以直接在命令行中使用了。使用下面这个命令即可完成安装。
```bash
redis-trib.rb  create  --replicas  1  192.168.1.88:7000 192.168.1.88:7001  192.168.1.88:7002 192.168.1.88:7003 192.168.1.88:7004 192.168.1.88:7005
```
出错了。这个工具是用 ruby 实现的，所以需要安装 ruby。安装命令如下：
```bash
    yum -y install ruby ruby-devel rubygems rpm-build
    gem install redis -v 3.3.3
```
完成后再执行
```bash
    redis-trib.rb  create  --replicas  1  192.168.1.88:7000 192.168.1.88:7001  192.168.1.88:7002 192.168.1.88:7003 192.168.1.88:7004 192.168.1.88:7005
```
8、集群验证
---
```bash
    redis-trib.rb check  192.168.1.88:7000    #验证集群分布状态
    redis-cli -h 192.168.1.88 -c -p 7000
    192.168.1.88:7000> set hello world
```
然后退出，切换另外一台上查看。
```bash
    get hello
```
问题：
---
1、修改redis.conf文件后上传需要手动将文件编码更新下
```bash
    yum -y install dos2unix*
    dos2unix redis.conf
```
2、安装redis会报错<br>
3、创建集群时会报错

参考
---
https://www.cnblogs.com/wuxl360/p/5920330.html
http://blog.csdn.net/libra_ts/article/details/71195128

