# tuned是一个在不同配置文件中运行的systemd服务
## vm.swappiness参数范围：0-100 是控制系统使用swap分区的积极性的内核参数
0 尽量不使用swap，优先使用物理内存，内存耗尽才会用swap </br>
60(默认) 平衡模式，内存用满40%，就开始倾向使用swap </br>
100  积极使用swap，系统会优先把数据挪到swap，释放物理内存</br>
**数值越低，系统越抗拒使用swap，性能越好（因物理内存比swap快）**</br>
### tuned更换性能配置文件，必须运行tuned服务才能选择性能配置文件
dnf install -y tuned </br>
systemctl enable --now tuned </br>
tuned-adm list  #显示当前配置文件 </br>
tuned-adm active #显示当前配置 </br>
tuned-adm recommend  #查看推荐配置 </br>
tuned-adm profile 配置文件名 #切换配置文件 </br>
tuned-adm active #显示当前配置文件 </br>
tuned-adm verify #验证配置 </br>

### 不依赖tuned，直接修改内科参数（调整系统对swap使用的积极性）
dnf install -y tuned </br>
systemctl enable --now tuned </br>
tuned-adm list </br>
echo vm.swappiness=40/nn > /etc/sysctl.d/swapiness.conf </br>
sysctl -p /etc/sysctl.d/swappiness.conf  #加载该文件 </br>
sysctl -a | grep swappiness #查看系统当前所有内核参数并过滤swappiness参数值 </br>


### tuned自定义配置文件
1. 创建自定义tuned配置文件目录 </br>
mkdir /etc/tuned/myprofile(任起） </br>
2.写入tuned专属配置文件（必须叫tuned.conf）</br>
vim /etc/tuned/myprofile/tuned.conf </br>
[sysctl] </br>
vm.swappiness=nn </br>
3.查看自定义profile是否被识别 </br>
tuned-adm list </br>
4.切换配置文件 </br>
tuned-adm profile myprofile </br>
5.验证配置是否有效 </br>
tuned-adm active </br>
sysctl -a | grep swappiness </br>
tuned-adm verify </br>
**tuned.conf文件中的参数配置优先级高于/etc/sysctl,d/*.conf中的参数**



[root@server3 ~]# systemctl start tuned
[root@server3 ~]# tuned-adm active
Current active profile: virtual-guest
[root@server3 ~]# cd /etc/tuned/
[root@server3 tuned]# ls
active_profile  bootcmdline  myprofile  post_loaded_profile  profile_mode  recommend.d  tuned-main.conf
[root@server3 tuned]# cd myprofile/
[root@server3 myprofile]# ls
tuned.conf
[root@server3 myprofile]# cat tuned.conf 
[sysctl]
vm.swappiness=66
[root@server3 myprofile]# tuned-adm profile myprofile 
[root@server3 myprofile]# tuned-adm active
Current active profile: myprofile
[root@server3 myprofile]# sysctl -a | grep swappiness
vm.swappiness = 66
[root@server3 myprofile]# tuned-adm recommend
virtual-guest
[root@server3 myprofile]# tuned-adm profile virtual-guest 
[root@server3 myprofile]# sysctl -a | grep swappiness
vm.swappiness = 30
[root@server3 myprofile]# 

