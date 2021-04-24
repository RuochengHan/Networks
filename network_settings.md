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

3. mount Windows' harddisk to Linux:
```bash
# pay attention to space in Windows username "\ "
sudo mount -t cifs //winIP/winfolder /mnt/win -o username=winsuer,password=winpass
```
