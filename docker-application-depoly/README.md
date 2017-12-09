docker日常维护
===

安装
==
1、关闭防火墙
---
安装docker之前，需要关闭防火墙：
```bash
    systemctl stop firewalld.service #停止firewall
    systemctl disable firewalld.service #禁止firewall开机启动
```
参考：https://www.cnblogs.com/silent2012/archive/2015/07/28/4682770.html

2、安装docker
--
```bash
    # step 1: 安装必要的一些系统工具
    sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    # Step 2: 添加软件源信息
    sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    # Step 3: 更新并安装 Docker-CE
    sudo yum makecache fast
    sudo yum -y install docker-ce
    # Step 4: 开启Docker服务
    sudo service docker start
```
安装校验
---
```bash
    docker version
```
参考：
https://cr.console.aliyun.com/?spm=5176.100239.blogcont29941.12.MkthiN#/accelerator
https://yq.aliyun.com/articles/110806
需要注意阿里最新的镜像源对应的centos系统版本号， 否则安装报错，具体错误参考如下：http://blog.csdn.net/abcd_d_/article/details/53996791

安装完成后，docker info时会提示权限问题

需设置免sudo使用docker命令，需要将/var/run/docker.sock文件要dev用户可读可写，属组需添加到docker属组。
如果还没有 docker group 就添加一个：
```bash
    sudo groupadd docker
```
将用户加入该 group 内。然后退出并重新登录就生效啦。
```bash
    sudo gpasswd -a dev docker
```
重启 docker 服务
```bash
    sudo service docker restart
```
切换当前会话到新 group 或者重启 X 会话
```bash
    newgrp - docker
```
具体参考：http://blog.csdn.net/baidu_36342103/article/details/69357438

3、配置docker加速器
===
您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器：
```bash
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
        "registry-mirrors": ["https://viw9gg11.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
```
参考：https://cr.console.aliyun.com/?spm=5176.100239.blogcont29941.12.MkthiN#/accelerator

设置docker自启：
$ sudo chkconfig docker on  

移除所有的容器和镜像（大扫除）
==
```bash
docker kill $(docker ps -q)
docker rm $(docker ps -a -q)
dockerrmi $(docker images -q -a)
```