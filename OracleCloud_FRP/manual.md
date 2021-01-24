# Build a FRP server using always free oracle cloud machine #

The always free instance is 1 Core AMD cpu (EYPC), 1 GB memory, ~40 GB SSD, and 0.48 Gbps network.

## Register and setup of oracle cloud machine ##

1. Register for free oracle account, you need to bind a credit/debit card, **prepaid card NOT** suppported.

2. Click **Create Compute Instance**.

3. Click **Edit**.

4. Change the following options \
Image -> CentOS 7 (or as you prefer); Add SSH keys -> Generate SSH key pair, Save Private Key, Save Public Key; Configure boot volume -> Use in-transit encryption. Others using the Default and Always Free Eligible options. Click **Create**.

5. In your instance, Click **Start**.

6. Click corresponding **Virtual Cloud Network**. In the **Subnet** section click corresponding subnet name. In the **Security Lists** section, click corresponding security list name. 

7. Click **Add Ingress Rules**. \
Add rules: TCP Source 0.0.0.0/0 Destination port range 6000,7000 (for FRP port forwardings); ICMP Source 0.0.0.0/0 Type and Code ALL (for ping).

8. Upload Private Key and Public Key to client computer.

9. Add ssh config. (Client)
```bash
$ (client) vim ~/.ssh/config
```bash
Host cloud
    User opc # oracle username
    Hostname xxx.xxx.xxx.xxx #orale cloud machine IP
    IdentityFile ~/.ssh/id_rsa_cloud # you Key file
```

## Setup FRP ##

1. Login to the oracle server. (Client)
```bash
$ (client) ssh cloud
```

2. Download and unzip FRP from github. (Server)
```bash
$ (server) sudo yum install wget
$ (server) wget https://github.com/fatedier/frp/releases/download/v0.27.1/frp_0.27.1_linux_amd64.tar.gz # or any newer version
$ (server) tar -zxvf frp_0.27.1_linux_amd64.tar.gz
$ (server) cd frp_0.27.1_linux_amd64
$ (server) rm -rf frpc*
```

3. Modify frps.ini file. (Server)
```bash
$ (server) vim frps.ini

[common]
bind_port = 7000   ## VM binding port for conecting to client
vhost_http_port = 80 ## VM http port
vhost_https_port = 443 ## VM https port
dashboard_port = 7500 ## dashboard port
# dashboard
dashboard_user = admin
dashboard_pwd = admin
```

4. Set firewall. (Server)
```bash
# set tcp port 6000 and 7000 open to public
$ (server) firewall-cmd --zone=public --add-port=7000/tcp --permanent
$ (server) firewall-cmd --zone=public --add-port=6000/tcp --permanent

# check and make sure opening port
$ (server) firewall-cmd --permanent --zone=public --list-ports

# reload firewall
$ (server) firewall-cmd --reload
```

5. Download and unzip FRP from github. (Client)
```bash
$ (client) sudo yum install wget
$ (client) wget https://github.com/fatedier/frp/releases/download/v0.27.1/frp_0.27.1_linux_amd64.tar.gz # or any newer version
$ (client) tar -zxvf frp_0.27.1_linux_amd64.tar.gz
$ (client) cd frp_0.27.1_linux_amd64
$ (client) rm -rf frps*
```

6. Modify frpc.ini file. (Client)
```bash
$ (client) vim frpc.ini

[common]
server_addr = xxx.xxx.xxx.xxx # frp server, orale cloud machine IP
server_port = 7000 ## frp server port
admin_addr = 127.0.0.1 ## frp client admin IP
admin_port = 7400 ## frpc client port

[ssh]
type = tcp
local_port = 22 ## local port
remote_port = 6000 ## remote port on frp server (for forward local port 22)
use_encryption = true
use_compression = true
```

7. Start frp: Id success, both print out some infomation.
```bash
$ (server) frps ./frps -c frps.ini
$ (client) frpc ./frpc -c frpc.ini
```

8. Check the connection from another computer.
```bash
$ (another computer) ssh clientusername@serverIP -p 6000
# then type client user passwd
# it should be the same as ssh clientusername@clientIP
```

## Possible issues ##

