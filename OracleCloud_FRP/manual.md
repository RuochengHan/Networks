# Build a FRP server (for NAT traversal) using always free oracle cloud machine #

The always free instance is 1 Core AMD cpu (EYPC), 1 GB memory, ~40 GB SSD, and 0.48 Gbps network.\
You need another cloud VM or local computer as frp client (which you want to have NAT traversal). If your local computer already has public IP or only a controlable router is between your local computer and public IP, there is **no need** for NAT traversal. In the latter case, you only need to set your local computer as **DMZ** machine in route manage page (192.168.1.1 or similar).

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
Host cloud
    User opc # oracle username
    Hostname xxx.xxx.xxx.xxx #orale cloud machine IP
    IdentityFile ~/.ssh/id_rsa_cloud # you Key file
    
$ (client) chmod 600 /root/.ssh/id_rsa_frp.pub 
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
bind_port = 7000   ## frp server binding port for conecting to client
vhost_http_port = 80 ## frp server http port
vhost_https_port = 443 ## frp server https port
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
$ (server) sudo ./frps -c frps.ini
$ (client) sudo ./frpc -c frpc.ini
```

8. Check the connection from another computer.
```bash
$ (another computer) ssh clientusername@serverIP -p 6000
# then type client user passwd
# it should be the same as ssh clientusername@clientIP
```

## Possible issues ##
1. Cannot login to the oracle (or other cloud) frp server. \
Check whether it is open to the public, like setting White list.\
Check if the ssh key is set correctly.

2. Cannot ping the cloud server IP. \
Check if ICMP is set to public.

3. frp client cannot connect to frp server. \
Make sure 7000 port is open on both Ingress Rules and firewall, and firewall is reloaded.

### References ###

1. https://serverok.in/allow-icmp-ping-in-oracle-cloud#:~:text=Oracle%20cloud%20do%20not%20allow,see%20%E2%80%9CVirtual%20Cloud%20Network%E2%80%9D.&text=To%20allow%20ICMP%2C%20you%20can%20add%20a%20new%20Rule%20for%20ICMP. 

2. https://cloud.tencent.com/developer/article/1622549 

3. https://blog.fengdis.com/2019/12/25/CentOS%E4%B8%8B%E9%80%9A%E8%BF%87frp%E5%81%9A%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F/ 

4. https://gabrieltanner.org/blog/port-forwarding-frp


# Flask Application Over HTTPS #
Use "Let's Encrypt" https://letsencrypt.org/getting-started/ for the HTTPS forwarding.
Nginx is used to forward HTTP to HTTPS

### References ###

1. https://www.jianshu.com/p/8f95fc005a47
2. https://blog.miguelgrinberg.com/post/running-your-flask-application-over-https
