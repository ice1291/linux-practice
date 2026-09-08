#pwd：显示当前所在目录
[root@server3 basic-commands]# pwd
/root/projects/linux-practice/basic-commands
#cd: 切换路径，无参数时回到家目录，'..'表示上一级目录,'.'表示当前目录
[root@server3 basic-commands]# cd ~
[root@server3 ~]# pwd
/root
[root@server3 ~]# cd ..
[root@server3 /]# pwd
/
#mkdir: 创建目录，-p 创建多级目录
[root@server3 /]# mkdir /testdir
[root@server3 /]# cd /testdir
#ls: 列出目录下的文件及目录，-a 列出所有（包括隐藏的内容），-l 以列表的形式列出个文件及目录信息
[root@server3 testdir]# ls
#touch: 创建文件
[root@server3 testdir]# touch test.txt
[root@server3 testdir]# ls
test.txt
#ll: 等同于ls -l
[root@server3 testdir]# ll
total 0
-rw-r--r--. 1 root root 0 Sep  8 12:02 test.txt
#echo: 重定向 >覆盖写入  >>追加写入 2> 只输出错误信息 &> 输出所有信息
[root@server3 testdir]# echo "hello linux" > test.txt
[root@server3 testdir]# cat test.txt
hello linux
[root@server3 testdir]# echo "hello rhel" >> test.txt
#cat: 查看文件内容
[root@server3 testdir]# cat test.txt
hello linux
hello rhel
[root@server3 testdir]# head -1 test.txt 
hello linux
[root@server3 testdir]# tail -1 test.txt
hello rhel
[root@server3 testdir]#
