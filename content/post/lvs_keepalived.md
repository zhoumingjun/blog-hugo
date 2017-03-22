+++
categories = []
tags = ["devops"]
date = "2017-03-22T17:23:36+08:00"
title = "lvs keepalived"
description = ""

+++

# introduction
- lvs:      
  [lvs](http://www.linuxvirtualserver.org/) is a kind of L4 load balancer.              
  some references here:
  [howto] (http://www.austintek.com/LVS/LVS-HOWTO/HOWTO/index.html)

  
- keepalived:       
  [keepalived](http://www.keepalived.org/) is a facilitator for lvs

# configuration

topology:

DIP: 192.168.2.172
RIP[1]: 192.168.2.173
RIP[2]: 192.168.2.174
VIP: 192.168.2.175 
## lvs only
director side 
```
#install ipvsadm
yum -y install ipvsadm
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf 
sysctl -p
touch /etc/sysconfig/ipvsadm 
systemctl start ipvsadm 
systemctl enable ipvsadm 

# add vip
ifconfig em1:0 192.168.2.175 netmask 255.255.255.255

# configuration
ipvsadm -C
ipvsadm -A -t 192.168.2,175:80 -s rr
ipvsadm -a -t 192.168.2,175:80 -r 192.168.2,173:80 -g
ipvsadm -a -t 192.168.2,175:80 -r 192.168.2,174:80 -g
ipvsadm -l 
```

realserver side
```
# add vip
ifconfig lo:0 192.168.2.175 netmask 255.255.255.255 broadcast 192.168.2.175 

# disable VIP arp
echo "1" > /proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" > /proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" > /proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" > /proc/sys/net/ipv4/conf/all/arp_announce
```

# lvs + keepalived
director side 

```
global_defs {               ##全局配置部分
    router_id LVS_MASTER       ##运行keepalived机器的一个标识
}
vrrp_instance VI_1 {       ##设置vrrp组，唯一且同一LVS服务器组要相同
    state MASTER               ##备份LVS服务器设置为BACKUP
    interface em1             # #设置对外服务的接口
    virtual_router_id 51       ##设置虚拟路由标识
    priority 100               #设置优先级，数值越大，优先级越高，backup设置小于100，当master宕机后自动将backup高的变为master。
    advert_int 1               ##设置同步时间间隔
    authentication {           ##设置验证类型和密码，master和buckup一定要设置一样
    auth_type PASS
    auth_pass 1111
    }
virtual_ipaddress {        ##设置VIP，可以多个，每个占一行
    192.168.2.175
    }
}
virtual_server 192.168.2.175 80 {
    delay_loop 6               ##健康检查时间间隔，单位s
    lb_algo wrr                ##负载均衡调度算法设置为加权轮叫
    lb_kind DR                 ##负载均衡转发规则
    nat_mask 255.255.255.0     ##网络掩码，DR模式要保障真是服务器和lvs在同一网段
    #persistence_timeout 5     ##会话保持时间，单位s
    protocol TCP               ##协议
    real_server 192.168.2.173 80 {      ##真实服务器配置，80表示端口
        weight 3                   ##权重
        TCP_CHECK {                ##服务器检测方式设置
        connect_timeout 5          ##连接超时时间
        nb_get_retry 3
        delay_before_retry 3
        connect_port 80
        }
    }
    real_server 192.168.2.174 80 {
        weight 3
        TCP_CHECK {
        connect_timeout 10
        nb_get_retry 3
        delay_before_retry 3
        connect_port 80
        }
    }
}
```

realserver side
```
# add vip
ifconfig lo:0 192.168.2.175 netmask 255.255.255.255 broadcast 192.168.2.175 

# disable VIP arp
echo "1" > /proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" > /proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" > /proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" > /proc/sys/net/ipv4/conf/all/arp_announce
```