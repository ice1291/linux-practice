# tar的基本用途是归档和提取
## 归档：把多个文件/文件夹打包成一个总文件（不减小体积，仅合并）
## 压缩：基于归档文件，用算法减小占用空间
-c: 创建新归档/压缩包（打包）  
-x: 解压/提取包内文件（解包）  
-t: 查看压缩包中的内容，不解压  
-z: gzip压缩，后缀“.tar.gz”  
-j: bzip2压缩，后缀".tar.bz2"
-J: xz压缩，后缀".tar.xz"
-f: 指定包文件名（必须放到所有参数最后)
-v: 显示详细执行过程  
-C: 指定解压到哪个目录  
## 打包文件夹：tar -cvf test.tar /testdir
## 打包并压缩：tar -czvf test.tar.gz /testdir
## 解压：tar -xzvf test.tar.gz (-C 目录)
## **bz2,xz格式的压缩与解压将上述命令中的z选项替换成对应选项即可**


[root@server3 testdir]# ll   </br>
total 4    </br>
-rw-r--r--. 1 root root 1009 Sep  8 17:10 access.log
drwxr-xr-x. 2 root root  113 Sep 10 13:41 dir
[root@server3 testdir]# tar -cvf dir.tar dir/
dir/
dir/2.txt
dir/3.txt
dir/5.txt
dir/6.txt
dir/7.txt
dir/8.txt
dir/9.txt
dir/test.txt
[root@server3 testdir]# ll
total 16
-rw-r--r--. 1 root root  1009 Sep  8 17:10 access.log
drwxr-xr-x. 2 root root   113 Sep 10 13:41 dir
-rw-r--r--. 1 root root 10240 Sep 10 13:42 dir.tar
[root@server3 testdir]# tar -czvf dir.tar.gz dir
dir/     dir.tar  
[root@server3 testdir]# tar -czvf dir.tar.gz dir/
dir/
dir/2.txt
dir/3.txt
dir/5.txt
dir/6.txt
dir/7.txt
dir/8.txt
dir/9.txt
dir/test.txt
[root@server3 testdir]# ll
total 20
-rw-r--r--. 1 root root  1009 Sep  8 17:10 access.log
drwxr-xr-x. 2 root root   113 Sep 10 13:41 dir
-rw-r--r--. 1 root root 10240 Sep 10 13:42 dir.tar
-rw-r--r--. 1 root root   243 Sep 10 13:43 dir.tar.gz
[root@server3 testdir]# mkdir test
[root@server3 testdir]# tar -xzvf dir.tar.gz -C test/
dir/
dir/2.txt
dir/3.txt
dir/5.txt
dir/6.txt
dir/7.txt
dir/8.txt
dir/9.txt
dir/test.txt
[root@server3 testdir]# tree test/
test/
└── dir
    ├── 2.txt
    ├── 3.txt
    ├── 5.txt
    ├── 6.txt
    ├── 7.txt
    ├── 8.txt
    ├── 9.txt
    └── test.txt

1 directory, 8 files


