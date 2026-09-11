# cron调度任务，crond进程每分钟检查它的配置
/etc/crontab是主要的（受管理的)配置文件，不应改动，应将cron作业放在/etc/cron.d/下，同时还有/etc/cron.{hourly,daily,weekly,monthly}可以用作即插即用的脚本文件为需要定期需要安排的任务</br>
另一种创建cron作业的方式是crontab -e 打开编辑器，同时可以使用-u username 指定用户</br>
**使用cron时须遵循”分（0-59） 时（0-23） 日（1-31） 月（1-12） 周（0-6） 命令“的格式** </br>
eg: crontab -e -u linda 0 5 * * * logger greeting from linda  #每月每周每天五点 greeting from linda </br>
*/5 * * * 5 : 每周五的每五分钟做某件事；0 8 * *1：每周一八点做某件事 </br>

crontab -e #添加/修改任务 </br>
crontab -l #查看当前用户的定时任务列表 </br>
crontab -r #删除当前用户所有定时任务 </br>
查看系统级定时任务：检查/etc/crontab,/etc/cron.daily/,/etc/cron.weekly/ </br>


# at是用于在特定时间运行特定用途的工作，atd服务须运行
## at <time> #安排一个特定时间的任务，接下来在交互式shell输入一个或多个任务，使用ctrl+d关闭交互式shell
atq 或 at -l #查看当前已安排的工作列表 </br>
atrm ID 或 at -d ID #取消任务 </br>
at 相对时间（now+10minutes）/绝对时间（14：00）
