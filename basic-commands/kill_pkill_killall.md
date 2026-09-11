# 管理进程，15 请求进程停止，9 强制停止，1 挂起进程
## kill [选项] PID1,PID2 ...  停止/挂起进程 ；kill -l 显示kill命令可用的信号列表
## killall/pkill 进程名  终止多个同名进程 -u 用户名：指定用户拥有的进程
ps -u 用户名 #查看用户拥有的进程

[root@server3 ~]# sleep 300 &
[2] 6765
[1]   Done                    sleep 30
[root@server3 ~]# ps aux | grep sleep
root        6765  0.0  0.0 220952  1032 pts/1    S    14:01   0:00 sleep 300
root        6768  0.0  0.1 221664  2328 pts/1    S+   14:02   0:00 grep --color=auto sleep
[root@server3 ~]# kill 6765
[root@server3 ~]# ps aux | grep sleep
root        6770  0.0  0.1 221664  2324 pts/1    S+   14:02   0:00 grep --color=auto sleep
[2]+  Terminated              sleep 300
## 清理僵尸进程 ps aux | grep defunct  #检查是否有僵尸进程
**1. kill [-9] 父进程ID **
**2. kill -CHLD 父进程ID （让父进程回收子进程，父进程杀不死的情况下） **

# 进程优先级（nice值-20~19，数值越小优先级越高，普通用户只能降低正在运行进程的优先级）
## nice -n nice值 <命令> 以调整后的优先级启动进程，进程未运行
## renice -n 新nice值 -p PID 更改当前正在活动进程的优先级，进程已运行，renice按PID
