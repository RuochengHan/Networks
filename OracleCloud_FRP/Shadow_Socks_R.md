
### On server ###
Use CentOS 8 system on Oracle
```bash
# yum -y install wget
# wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh 
# chmod +x ssr.sh 
# bash ssr.sh
```

Settings:
```bash
port 8787
aes-256-cfb
auth_sha1_v4
http_simple
```

Allow the port 8787 Firewall and Whitelist with FTP protocol.

Copy SSR link:
```bash
# bash ssr.sh
input 5
```

### On client ###

1. Download: https://github.com/shadowsocksr-backup/shadowsocksr-csharp/releases.
2. Copy the SSR link from server.

### Problem ###
1. Github not work...

### Reference ###
1. https://www.bbsmax.com/A/Vx5M7gPvzN/
2. https://github.com/weizx-coder/SSR
