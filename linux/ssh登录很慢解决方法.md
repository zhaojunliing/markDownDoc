## ssh登录很慢解决方法

使用ssh客户端（如：putty）连接Linux服务器，可能会等待10-30秒才有提示输入密码。严重影响工作效率。登录很慢，登录上去后速度正常，这种情况主要有两种可能的原因：

#### 1. DNS反向解析问题
OpenSSH在用户登录的时候会验证IP，它根据用户的IP使用反向DNS找到主机名，再使用DNS找到IP地址，最后匹配一下登录的IP是否合法。如果客户机的IP没有域名，或者DNS服务器很慢或不通，那么登录就会很花时间。

##### 解决办法：在目标服务器上修改sshd服务器端配置,并重启sshd

```shell
vi /etc/ssh/sshd_config
UseDNS no
```

#### 2. 关闭ssh的gssapi认证
用ssh -v user@server 可以看到登录时有如下信息：

```shell
debug1: Next authentication method: gssapi-with-mic
debug1: Unspecified GSS failure. Minor code may provide more information 
```
注：ssh -vvv user@server 可以看到更细的debug信息

##### 解决办法：修改sshd服务器端配置

```shell
vi /etc/ssh/ssh_config
GSSAPIAuthentication no
```

可以使用ssh -o GSSAPIAuthentication=no user@server登录

GSSAPI ( Generic Security Services Application Programming Interface) 是一套类似Kerberos 5的通用网络安全系统接口。该接口是对各种不同的客户端服务器安全机制的封装，以消除安全接口的不同，降低编程难度。但该接口在目标机器无域名解析时会有问题

使用strace查看后发现，ssh在验证完key之后，进行authentication gssapi-with-mic，此时先去连接DNS服务器，在这之后会进行其他操作

```shell
[root@192-168-3-40 ~]# ssh -vvv root@192.168.3.44  
OpenSSH_5.3p1, OpenSSL 1.0.1e-fips 11 Feb 2013  
debug1: Reading configuration data /etc/ssh/ssh_config  
debug1: Applying options for *  
debug2: ssh_connect: needpriv 0  
debug1: Connecting to 192.168.3.44 [192.168.3.44] port 22.  
debug1: Connection established.  
debug1: permanently_set_uid: 0/0  
debug1: identity file /root/.ssh/identity type -1  
debug1: identity file /root/.ssh/identity-cert type -1  
debug1: identity file /root/.ssh/id_rsa type -1  
debug1: identity file /root/.ssh/id_rsa-cert type -1  
debug1: identity file /root/.ssh/id_dsa type -1  
debug1: identity file /root/.ssh/id_dsa-cert type -1  
debug1: identity file /root/.ssh/id_ecdsa type -1  
debug1: identity file /root/.ssh/id_ecdsa-cert type -1  
debug1: Remote protocol version 2.0, remote software version OpenSSH_5.3  
debug1: match: OpenSSH_5.3 pat OpenSSH*  
debug1: Enabling compatibility mode for protocol 2.0  
debug1: Local version string SSH-2.0-OpenSSH_5.3  
debug2: fd 3 setting O_NONBLOCK  
debug1: SSH2_MSG_KEXINIT sent  
debug3: Wrote 960 bytes for a total of 981  
debug1: SSH2_MSG_KEXINIT received  
debug2: kex_parse_kexinit: diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1  
debug2: kex_parse_kexinit: ssh-rsa-cert-v01@openssh.com,ssh-dss-cert-v01@openssh.com,ssh-rsa-cert-v00@openssh.com,ssh-dss-cert-v00@openssh.com,ssh-rsa,ssh-dss  
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se  
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se  
debug2: kex_parse_kexinit: hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96  
debug2: kex_parse_kexinit: hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96  
debug2: kex_parse_kexinit: none,zlib@openssh.com,zlib  
debug2: kex_parse_kexinit: none,zlib@openssh.com,zlib  
debug2: kex_parse_kexinit:  
debug2: kex_parse_kexinit:  
debug2: kex_parse_kexinit: first_kex_follows 0  
debug2: kex_parse_kexinit: reserved 0  
debug2: kex_parse_kexinit: diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1  
debug2: kex_parse_kexinit: ssh-rsa,ssh-dss  
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se  
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se  
debug2: kex_parse_kexinit: hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96  
debug2: kex_parse_kexinit: hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96  
debug2: kex_parse_kexinit: none,zlib@openssh.com  
debug2: kex_parse_kexinit: none,zlib@openssh.com  
debug2: kex_parse_kexinit:  
debug2: kex_parse_kexinit:  
debug2: kex_parse_kexinit: first_kex_follows 0  
debug2: kex_parse_kexinit: reserved 0  
debug2: mac_setup: found hmac-md5  
debug1: kex: server->client aes128-ctr hmac-md5 none  
debug2: mac_setup: found hmac-md5  
debug1: kex: client->server aes128-ctr hmac-md5 none  
debug1: SSH2_MSG_KEX_DH_GEX_REQUEST(1024<1024<8192) sent  
debug1: expecting SSH2_MSG_KEX_DH_GEX_GROUP  
debug3: Wrote 24 bytes for a total of 1005  
debug2: dh_gen_key: priv key bits set: 120/256  
debug2: bits set: 506/1024  
debug1: SSH2_MSG_KEX_DH_GEX_INIT sent  
debug1: expecting SSH2_MSG_KEX_DH_GEX_REPLY  
debug3: Wrote 144 bytes for a total of 1149  
debug3: check_host_in_hostfile: host 192.168.3.44 filename /root/.ssh/known_hosts  
debug3: check_host_in_hostfile: host 192.168.3.44 filename /root/.ssh/known_hosts  
debug3: check_host_in_hostfile: match line 8  
debug1: Host '192.168.3.44' is known and matches the RSA host key.  
debug1: Found key in /root/.ssh/known_hosts:8  
debug2: bits set: 527/1024  
debug1: ssh_rsa_verify: signature correct  
debug2: kex_derive_keys  
debug2: set_newkeys: mode 1  
debug1: SSH2_MSG_NEWKEYS sent  
debug1: expecting SSH2_MSG_NEWKEYS  
debug3: Wrote 16 bytes for a total of 1165  
debug2: set_newkeys: mode 0  
debug1: SSH2_MSG_NEWKEYS received  
debug1: SSH2_MSG_SERVICE_REQUEST sent  
debug3: Wrote 48 bytes for a total of 1213  
debug2: service_accept: ssh-userauth  
debug1: SSH2_MSG_SERVICE_ACCEPT received  
debug2: key: /root/.ssh/identity ((nil))  
debug2: key: /root/.ssh/id_rsa ((nil))  
debug2: key: /root/.ssh/id_dsa ((nil))  
debug2: key: /root/.ssh/id_ecdsa ((nil))  
debug3: Wrote 64 bytes for a total of 1277  
debug1: Authentications that can continue: publickey,gssapi-keyex,gssapi-with-mic,password  
debug3: start over, passed a different list publickey,gssapi-keyex,gssapi-with-mic,password  
debug3: preferred gssapi-keyex,gssapi-with-mic,publickey,keyboard-interactive,password  
debug3: authmethod_lookup gssapi-keyex  
debug3: remaining preferred: gssapi-with-mic,publickey,keyboard-interactive,password  
debug3: authmethod_is_enabled gssapi-keyex  
debug1: Next authentication method: gssapi-keyex  
debug1: No valid Key exchange context  
debug2: we did not send a packet, disable method  
debug3: authmethod_lookup gssapi-with-mic  
debug3: remaining preferred: publickey,keyboard-interactive,password  
debug3: authmethod_is_enabled gssapi-with-mic  
debug1: Next authentication method: gssapi-with-mic  
debug3: Trying to reverse map address 192.168.3.44.  
debug1: Unspecified GSS failure.  Minor code may provide more information  
Cannot determine realm for numeric host address  
  
debug1: Unspecified GSS failure.  Minor code may provide more information  
Cannot determine realm for numeric host address  
  
debug1: Unspecified GSS failure.  Minor code may provide more information  
```


​      
```shell
debug1: Unspecified GSS failure.  Minor code may provide more information  
Cannot determine realm for numeric host address  
  
debug2: we did not send a packet, disable method  
debug3: authmethod_lookup publickey  
debug3: remaining preferred: keyboard-interactive,password  
debug3: authmethod_is_enabled publickey  
debug1: Next authentication method: publickey  
debug1: Trying private key: /root/.ssh/identity  
debug3: no such identity: /root/.ssh/identity  
debug1: Trying private key: /root/.ssh/id_rsa  
debug3: no such identity: /root/.ssh/id_rsa  
debug1: Trying private key: /root/.ssh/id_dsa  
debug3: no such identity: /root/.ssh/id_dsa  
debug1: Trying private key: /root/.ssh/id_ecdsa  
debug3: no such identity: /root/.ssh/id_ecdsa  
debug2: we did not send a packet, disable method  
debug3: authmethod_lookup password  
debug3: remaining preferred: ,password  
debug3: authmethod_is_enabled password  
debug1: Next authentication method: password  
root@192.168.3.44's password:  
```