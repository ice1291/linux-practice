# grep：过滤工具
grep [选项] "匹配内容" 文件名
-n：显示匹配行号	-i：忽略大小写
-r/R：递归遍历目录	-E：使用正则表达式

[root@server3 testdir]# cat test.txt 
hello linux
hello rhel
[root@server3 testdir]# grep hello test.txt 
hello linux
hello rhel

# awk： 
awk [选项] '模式 {动作}' 文件或路径
变量	含义			示例
$0	所有行内容		awk '{print $0}' test.txt  打印所有行
$n	第n列内容		awk '{print $1}' test.txt  打印第一列
$NF	最后一列内容		awk '{print $NF}' test.txt 打印最后一列
NR	当前处理的行号		awk 'NR==1 {print $2}' test.txt  打印第一行第二列
NF	当前行的列数		awk '{print NF}' test.txt  打印当前行的列数
FS	输入分隔符（默认空格） 
OFS	输出分隔符（默认空格）

eg:
处理文件指定逗号为分隔符： awk -F ',' '{print $0}' test.txt  -F: 指定分隔符
打印第一，三列： awk '{print $1，$3}' txt
筛选第三列大于80的行： awk '$3>80 {print $3}' txt
筛选第二行： awk 'NR==2 {print}' 文件或路径
筛选包含Alice的行： awk '/Alice/ {print $0}' txt

# sed命令
sed [选项] '命令' 文件名
替换文本命令：
格式：'s/old/new/g'
sed 's/old/new/' txt  替换每行第一个匹配项
sed 's/old/new/g' txt 全局替换
删除命令：
sed '2d' txt  删除第二行
sed '3,5d' txt 删除三到五行
sed '/error/d' txt 删除包含error的行
sed '/^$/d' txt 删除空行
# sort命令
用于对文本文件或标准输入内容按行进行排序。-n：按数值大小排序；-r：逆序（降序）；-u：去重
# uniq命令
用于报告或删除文件中相邻且重复的行，通常配合sort使用。-c：在每行前面显示该行重复出现的次数；-d：仅
显示重复出现的行；-u：仅显示不重复的行；-i：不区分大小写
# 线上故障排查的实战演练。
## 场景背景
## 我们的 Nginx 访问日志（access.log）突然出现了异常，导致服务器 CPU 飙升。现在，你需要从这份日志中快速提取关键信息，协助开发定位问题

# 创建环境
[root@server3 testdir]# cat <<EOF > access.log
192.168.1.10 - - [08/Sep/2026:10:00:01 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:02 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28" 
192.168.1.10 - - [08/Sep/2026:10:00:03 +0800] "GET /api/users HTTP/1.1" 200 2048 "-" "Mozilla/5.0"
172.16.0.8 - - [08/Sep/2026:10:00:04 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "curl/7.68.0"
10.0.0.5 - - [08/Sep/2026:10:00:05 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28" 
192.168.1.10 - - [08/Sep/2026:10:00:06 +0800] "GET /api/users HTTP/1.1" 500 128 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:07 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28" 
172.16.0.8 - - [08/Sep/2026:10:00:08 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "curl/7.68.0"
192.168.1.10 - - [08/Sep/2026:10:00:09 +0800] "GET /api/users HTTP/1.1" 200 2048 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:10 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28" 
> EOF


### 任务 1：快速定位报错（grep）
开发说后端接口可能挂了，请写一个命令，从 access.log 中筛选出所有 HTTP 状态码为 500 的请求，并显示行号。

[root@server3 testdir]# grep -n 500 access.log 
6:192.168.1.10 - - [08/Sep/2026:10:00:06 +0800] "GET /api/users HTTP/1.1" 500 128 "-" "Mozilla/5.0"

### 任务 2：排查恶意请求（grep + 管道）
安全团队怀疑有脚本在暴力破解登录接口。请写一个命令，找出所有请求路径包含 /api/login 且状态码为 401 的行（即登录失败的请求）。

[root@server3 testdir]# grep /api/login access.log | grep 401 
10.0.0.5 - - [08/Sep/2026:10:00:02 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28"
10.0.0.5 - - [08/Sep/2026:10:00:05 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28"
10.0.0.5 - - [08/Sep/2026:10:00:07 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28"
10.0.0.5 - - [08/Sep/2026:10:00:10 +0800] "POST /api/login HTTP/1.1" 401 512 "-" "Python-Requests/2.28"

### 任务 3：提取攻击者 IP（awk）
为了封禁恶意 IP，请写一个命令，提取出所有 401 登录失败请求的客户端 IP 地址（即每行的第 1 列）。

[root@server3 testdir]# grep 401 access.log  | awk '{print $1}'
10.0.0.5
10.0.0.5
10.0.0.5
10.0.0.5

### 任务 4：统计高频访问接口（awk + sort + uniq）
运维主管想知道哪个接口被访问得最频繁。请写一个命令，提取出所有的请求路径（如 /index.html、/api/login，注意它是双引号里的第 2 个字段，也就是第 7 列），统计每个路径的访问次数，并按访问次数从大到小排序。

[root@server3 testdir]# awk '{print $7}' access.log | sort | uniq -c | sort -rn
      4 /api/login
      3 /index.html
      3 /api/users
      1 

### 任务 5：紧急清理日志（sed）
为了把日志发给外部审计，需要脱敏。请写一个命令，将 access.log 中所有的 Python-Requests/2.28 替换为 [REDACTED]，并将结果直接打印在屏幕上（绝对不能修改原文件！）。
## "\"为反义符，保护其后的一个字符不被扩展，严禁使用-i参数，他会直接破快原始日志

[root@server3 testdir]# sed 's/"Python-Requests\/2\.28"/\[REDACTED\]/g' access.log 
192.168.1.10 - - [08/Sep/2026:10:00:01 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:02 +0800] "POST /api/login HTTP/1.1" 401 512 "-" [REDACTED]
192.168.1.10 - - [08/Sep/2026:10:00:03 +0800] "GET /api/users HTTP/1.1" 200 2048 "-" "Mozilla/5.0"
172.16.0.8 - - [08/Sep/2026:10:00:04 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "curl/7.68.0"
10.0.0.5 - - [08/Sep/2026:10:00:05 +0800] "POST /api/login HTTP/1.1" 401 512 "-" [REDACTED]
192.168.1.10 - - [08/Sep/2026:10:00:06 +0800] "GET /api/users HTTP/1.1" 500 128 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:07 +0800] "POST /api/login HTTP/1.1" 401 512 "-" [REDACTED]
172.16.0.8 - - [08/Sep/2026:10:00:08 +0800] "GET /index.html HTTP/1.1" 200 1024 "-" "curl/7.68.0"
192.168.1.10 - - [08/Sep/2026:10:00:09 +0800] "GET /api/users HTTP/1.1" 200 2048 "-" "Mozilla/5.0"
10.0.0.5 - - [08/Sep/2026:10:00:10 +0800] "POST /api/login HTTP/1.1" 401 512 "-" [REDACTED]

