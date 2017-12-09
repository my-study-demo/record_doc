基础
==========

1、网络配置
-------
安装虚拟机时选择网络桥连接
安装完成后配置网络（/etc/sysconfig/network-scripts/ifcfg-**）
我这里是ifcfg-ens33，注意需要root权限
```bash
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static	#dhcp改成static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=491c2f77-7c70-4062-87a1-eb4c9f63dd0d
DEVICE=ens33
ONBOOT=yes	#改成yes

DNS1=223.5.5.5
DNS2=223.6.6.6	
IPADDR=192.168.1.88		#ip地址
NETMASK=255.255.255.0	#子网隐码
GATEWAY=192.168.1.1		#网关IP
```
修改完成后，保存！

重启网络
```bash
service network restart
```

查看网络状态
```bash
ip addr
```