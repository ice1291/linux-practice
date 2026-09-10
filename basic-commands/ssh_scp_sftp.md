# ssh远程连接
# ssh ip/主机名 #以当前用户连接 。ssh username@ip/主机名 #指定用户链接
**若要以主机名链接主机，须在本机/etc/hosts配置域名解析,格式为ip fqdn hostname**
[root@server3 testdir]# cat /etc/hosts
192.168.88.130 server1.example.com server1
127.0.0.1 a.com
127.0.0.1 b.com
127.0.0.1 c.com
127.0.0.1 d.com

# scp是ssh的一部分，与cp工作原理相似
## scp 本地文件 远程主机用户@远程主机ip/主机名:远程主机目标路径.-r 递归
scp /etc/hosts root@server1:/tmp/


# sftp 远程传输文件：sftp username@hostname. 
## get:从远程主机下载到本地,put:从本地上传到远程主机
### sftp> get 远程主机目录 本地主机目录； sftp> put 本地主机目录 远程主机目录
**在sftp>交互界面中的操作命令需要在首部加l，为本地操作，否则为远程主机操作。eg：lpwd->pwd**
