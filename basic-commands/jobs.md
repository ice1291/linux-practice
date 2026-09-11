# shell作业

命令 &  #将该作业启动在后台 </br>
fg [ID]#将最后一个/ID在后台启动的作业移回前台 </br>
bg [ID]#将其作为后台作业继续运行 </br>
jobs  #查看运行的所有作业 </br>
ctrl+z #临时停止作业 </br>
ctrl+c #停止作业并将其从内存中移除 </br>
[root@server3 ~]# sleep 30
^Z
[1]+  Stopped                 sleep 30
[root@server3 ~]# bg
[1]+ sleep 30 &
[root@server3 ~]# jobs
[1]+  Running                 sleep 30 &
[root@server3 ~]# fg 1
sleep 30
^C
[root@server3 ~]# 

