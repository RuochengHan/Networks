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
sudo service NetworkManager start
sudo sudo nmtui
```
