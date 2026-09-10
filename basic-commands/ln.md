# ln [-s] 源文件 链接文件
## ln 创建硬链接（同一设备，不能对目录进行链接，源文件消失，链接文件不会消失）
## ln -s 创建软连接（不同设备，可对目录进行连接，源文件消失，目标文件小时）


'''
[root@server3 testdir]# ln 4.txt hardlink
[root@server3 testdir]# ll
-rw-r--r--. 2 root root    0 Sep  8 16:58 4.txt
-rw-r--r--. 2 root root    0 Sep  8 16:58 hardlink
[root@server3 testdir]# ln -s 4.txt softlink
[root@server3 testdir]# ll
-rw-r--r--. 2 root root    0 Sep  8 16:58 4.txt
-rw-r--r--. 2 root root    0 Sep  8 16:58 hardlink
lrwxrwxrwx. 1 root root    5 Sep 10 13:16 softlink -> 4.txt
[root@server3 testdir]# echo "链接文件" >> 4.txt 
[root@server3 testdir]# cat 4.txt 
链接文件
[root@server3 testdir]# cat hardlink 
链接文件
[root@server3 testdir]# cat softlink 
链接文件
[root@server3 testdir]# rm -f 4.txt 
[root@server3 testdir]# ll
-rw-r--r--. 1 root root   13 Sep 10 13:20 hardlink
lrwxrwxrwx. 1 root root    5 Sep 10 13:16 softlink -> 4.txt
[root@server3 testdir]# cat hardlink 
链接文件
[root@server3 testdir]# cat softlink 
cat: softlink: No such file or directory
[root@server3 testdir]# 
'''
