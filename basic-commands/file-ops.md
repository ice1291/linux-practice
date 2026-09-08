[root@server3 basic-commands]# pwd
/root/projects/linux-practice/basic-commands
[root@server3 basic-commands]# cd ~
[root@server3 ~]# pwd
/root
[root@server3 ~]# cd ..
[root@server3 /]# pwd
/
[root@server3 /]# mkdir /testdir
[root@server3 /]# cd /testdir
[root@server3 testdir]# ls
[root@server3 testdir]# touch test.txt
[root@server3 testdir]# ls
test.txt
[root@server3 testdir]# ll
total 0
-rw-r--r--. 1 root root 0 Sep  8 12:02 test.txt
[root@server3 testdir]# echo "hello linux" > test.txt
[root@server3 testdir]# cat test.txt
hello linux
[root@server3 testdir]# echo "hello rhel" >> test.txt
[root@server3 testdir]# cat test.txt
hello linux
hello rhel
[root@server3 testdir]# head -1 test.txt 
hello linux
[root@server3 testdir]# tail -1 test.txt
hello rhel
[root@server3 testdir]#
