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

9. vim ~/.ssh/config \
```bash
Host cloud
    User opc # oracle username
    Hostname xxx.xxx.xxx.xxx #orale cloud machine IP
    IdentityFile ~/.ssh/id_rsa_cloud # you Key file
```

## Setup FRP ##

1. Login to the oracle server \
```bash
ssh cloud
```

2. 
