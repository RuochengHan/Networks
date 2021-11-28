1. If DNS service is lost on CentOS 7 system:
```bash
/etc/NetworkManager/NetworkManager.conf：
[main]
plugins=ifcfg-rh
dns=none

$ systemctl restart NetworkManager.service

/etc/resolv.conf:
nameserver 114.114.114.114
nameserver 8.8.8.8
```

2. network interface lost:
```bash
$ systemctl start NetworkManager
```

3. .ssh/config, use proxy:
```bash
Host targetserver
Hostname targetserver.domain
User username
ProxyCommand ssh -q -Y username@proxyserver.domain -W %h:%p
IdentityFile ~/.ssh/id_rsa
```
4. centos wifi start:
```bash
$ sudo service NetworkManager start
$ sudo sudo nmtui
```
5. show router ip address
```bash
$ ip route show
```

6. double route connections with first the priority:
```bash
$ sudo route add -net 0.0.0.0/0 wlp3s0 # wireless interface
$ sudo route add -net 0.0.0.0/0 gw 10.0.144.1 # wireless router
$ sudo route add -net 192.0.0.0/8 enp5s0 # eth interface
$ sudo route add -net 192.0.0.0/8 gw 192.168.1.1 # eth router
```

7. disable ipv6 if it is default for the router, in this case the router table is hidden and traceroute/route -n not work.
```bash
$ sudo nmcli connection modify $wifiname ipv6.method "ignore"
$ sudo nmcli connection up $wifiname
# check
$ ip address show $interfacename
```

8. check wifi speed:
```bash
nmcli dev wifi
```
