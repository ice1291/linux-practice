# 存储管理需先找到块设备
## lsblk 获取现有设备概览
GPT与MBR分区： </br>
MBR有4个分区，分为逻辑分区和扩展分区 </br>
GPT有更多的空间存储分区 </br>
## 创建分区：fdisk 设备路径
g：选择GPT类型分区 </br>
n：创建分区（MBR分区，若想创建GPT分区必须先g） </br>
p：打印所有分区 </br>
w: 保存退出</br>
q：不保存推出</br>
l：获取完整分区类型概览 </br>
t：改变分区类型（lvm，swap）</br>
m：查看帮助 </br>

## 创建新分区后需要设置文件系统类型:mkfs.xfs/ext4
## 在/etc/fstab挂载实现持久化：设备 挂载点 文件系统类型  defaults 0 0； mount -a 验证是否挂载成功

## 创建分区
1.增加磁盘 </br>
2.lsblk </br>
3.fdisk 该盘 </br>
4.g->n->...p->w </br>
5.mkfs.xfs/ext4 设备 </br>
6.lsblk </br>
7.(u)mount 设备 挂载点 </br>
8.vim /etc/fstab :设备 挂载点 文件系统类型  defaults 0 0 </br>
9.mount -a </br>

## 创建交换分区
1.创建分区并设置分区类型为swap </br>
2.将分区格式化为交换空间: mkswap </br>
3.free -m </br>
4.启用新分配的交换空间：swapon swap分区设备 </br>
5.free -m </br>
6.持久化：echo "该设备 none swap defaults 0 0">> /etc/fstab </br>
7.mount -a </br>

## 标签和UUID
