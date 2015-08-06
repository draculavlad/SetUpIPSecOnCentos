# SetUpIPSecOnCentos(not success)
* for centos7

## prepare##
```shell
rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
yum update -y
yum install epel-release
yum install openswan xl2tpd ppp lsof
cat /etc/ipsec.conf
***
config setup
	protostack=netkey
	dumpdir=/var/run/pluto/
	nat_traversal=yes
	virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:25.0.0.0/8,%v4:100.64.0.0/10,%v6:fd00::/8,%v6:fe80::/10

include /etc/ipsec.d/*.conf
***
touch L2TP.conf
cat L2TP.conf
***
conn L2TP-PSK-NAT
    rightsubnet=vhost:%priv
    also=L2TP-PSK-noNAT
conn L2TP-PSK-noNAT
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    dpddelay=30
    dpdtimeout=120
    dpdaction=clear
    rekey=no
    ikelifetime=8h
    keylife=1h
    type=transport
    left=${your_vps_ip}
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
***
touch my.secrets
cat my.secrets
***
${your_vps_ip}  %any: PSK "${your_private_secret_key}"
***
echo " \ 
net.ipv4.ip_forward = 1 \
net.ipv4.conf.default.accept_redirects = 0 \
net.ipv4.conf.default.send_redirects = 0 \
net.ipv4.conf.eth0.rp_filter = 0 \
net.ipv4.conf.default.rp_filter = 0" >>  /etc/sysctl.conf
sysctl -p
ipsec setup start
ipsec verify
```

