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

