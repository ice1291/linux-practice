# 为获取RHEL发生的消息，有三种方法
1.使用journalctl命令从日志中获取详细信息 </br>
2.systemctl status <单元> 获取最新单元详情 </br>
3.监控/var/log目录中的文件 </br>
journalctl -p err 仅显示消息带有优先级错误和更高优先级的 </br>
journalctl -f  实时显示日志中最后10行 </br>
journalctl -u 服务名 只显示该特定单元的消息 </br>
journalctl -PID=1 --since 9:00:00 --until 15:00:00 查看PID为1的进程在上午9点到下午三点之间写入的所有消息 
