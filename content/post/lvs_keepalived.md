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

- DIP: 192.168.2.172            
- RIP[1]: 192.168.2.173         
- RIP[2]: 192.168.2.174     
- VIP: 192.168.2.175            

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

## lvs + keepalived
director side 

```
global_defs {              
    router_id LVS_MASTER   
}
vrrp_instance VI_1 {       
    state MASTER           
    interface em1          
    virtual_router_id 51   
    priority 100           
    advert_int 1           
    authentication {       
    auth_type PASS
    auth_pass 1111
    }
virtual_ipaddress {        
    192.168.2.175
    }
}
virtual_server 192.168.2.175 80 {
    delay_loop 6            
    lb_algo wrr             
    lb_kind DR              
    nat_mask 255.255.255.0  
    #persistence_timeout 5  
    protocol TCP            
    real_server 192.168.2.173 80 {      
        weight 3                   
        TCP_CHECK {               
        connect_timeout 5        
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