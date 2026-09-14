# LVM底层逻辑：先把设备做成pv，将pv装进vg，vg像一个大硬盘资源池，最后从vg中切出空间创建lv）
## pv 是物理卷（可以是磁盘，分区(需要将分区改成lvm类型)，任何块设备，只需要将物理卷加入卷组中）
pvcreate 块设备/分区 #创建物理卷 </br>
pvremove 块设备/分区 #删除物理卷 </br>
pvs  #显示物理卷信息
pvmove -v  物理卷1  物理卷2  #将物理卷1上的数据转移到物理卷2上 </br>
[root@server3 ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 43130CCA-D46E-1C4C-90C1-8C4E3F313808

Device     Start     End Sectors Size Type
/dev/sdc1   2048 2099199 2097152   1G Linux filesystem

Command (m for help): t
Selected partition 1
Partition type or alias (type L to list all): lvm
Changed type of partition 'Linux filesystem' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 43130CCA-D46E-1C4C-90C1-8C4E3F313808

Device     Start     End Sectors Size Type
/dev/sdc1   2048 2099199 2097152   1G Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@server3 ~]# pvcreate /dev/sdc1
WARNING: xfs signature detected on /dev/sdc1 at offset 0. Wipe it? [y/n]: y
  Wiping xfs signature on /dev/sdc1.
  Physical volume "/dev/sdc1" successfully created.
[root@server3 ~]# pvs
  PV         VG      Fmt  Attr PSize  PFree 
  /dev/sdb3  vgfiles lvm2 a--  <4.00g <3.00g
  /dev/sdb4  vgswap  lvm2 a--  <5.00g     0 
  /dev/sdc1          lvm2 ---   1.00g  1.00g
[root@server3 ~]# pvremove /dev/sdc1
  Labels on physical volume "/dev/sdc1" successfully wiped.
[root@server3 ~]# pvs
  PV         VG      Fmt  Attr PSize  PFree 
  /dev/sdb3  vgfiles lvm2 a--  <4.00g <3.00g
  /dev/sdb4  vgswap  lvm2 a--  <5.00g     0 


# vg(卷组，是所有存储的核心，若需要更多存储空间只需添加新的物理卷）
vgcreate (-s 8M  #指定大小) vgname 物理卷（分区，块设备，磁盘） #创建vg,可直接将分区，设备直接转化成pv再创建为vg </br>
vgremove vgname  #删除vg   </br>
vgextend vgname 设备  #扩展vg </br>
vgreduce vgname 设备  #移除设备（缩减vg大小，xfs文件系统无法缩小，ext4可缩小） </br>
vgs  #显示所有vg信息 </br>



[root@server3 ~]# vgs
VG #PV #LV #SN Attr VSize VFree
vgfiles 1 1 0 wz--n- <4.00g <3.00g
vgswap 1 1 0 wz--n- <5.00g 0
[root@server3 ~]# vgcreate myvg /dev/sdc1
Physical volume "/dev/sdc1" successfully created.
Volume group "myvg" successfully created
[root@server3 ~]# vgs
VG #PV #LV #SN Attr VSize VFree
myvg 1 0 0 wz--n- 1020.00m 1020.00m
vgfiles 1 1 0 wz--n- <4.00g <3.00g
vgswap 1 1 0 wz--n- <5.00g 0
[root@server3 ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 43130CCA-D46E-1C4C-90C1-8C4E3F313808

Device Start End Sectors Size Type
/dev/sdc1 2048 2099199 2097152 1G Linux LVM

Command (m for help): n
Partition number (2-128, default 2):
First sector (2099200-41943006, default 2099200):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2099200-41943006, default 41943006): +1G

Created a new partition 2 of type 'Linux filesystem' and of size 1 GiB.

Command (m for help): t
Partition number (1,2, default 2):
Partition type or alias (type L to list all): lvm

Changed type of partition 'Linux filesystem' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@server3 ~]# mkfs.ext4 /dev/sdc2
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 08337153-b2a4-4ba6-8479-08d6a5c657a2
Superblock backups stored on blocks:
32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

[root@server3 ~]# vgextend myvg /dev/sdc2
WARNING: ext4 signature detected on /dev/sdc2 at offset 1080. Wipe it? [y/n]: y
Wiping ext4 signature on /dev/sdc2.
Physical volume "/dev/sdc2" successfully created.
Volume group "myvg" successfully extended
[root@server3 ~]# vgs
VG #PV #LV #SN Attr VSize VFree
myvg 2 0 0 wz--n- 1.99g 1.99g
vgfiles 1 1 0 wz--n- <4.00g <3.00g
vgswap 1 1 0 wz--n- <5.00g 0
[root@server3 ~]# vgreduce myvg /dev/sdc2
Removed "/dev/sdc2" from volume group "myvg"
[root@server3 ~]# vgs
VG #PV #LV #SN Attr VSize VFree
myvg 1 0 0 wz--n- 1020.00m 1020.00m
vgfiles 1 1 0 wz--n- <4.00g <3.00g
vgswap 1 1 0 wz--n- <5.00g 0
[root@server3 ~]# vgremove myvg
Volume group "myvg" successfully removed
[root@server3 ~]# vgs
VG #PV #LV #SN Attr VSize VFree
vgfiles 1 1 0 wz--n- <4.00g <3.00g
vgswap 1 1 0 wz--n- <5.00g 0


## lv(lv的空间由vg来，vg由pv来，pv初始化分区，用户使用的是lv）
lvcreate -n lvname -l/L 2000M/2G vgname #创建逻辑卷（-l后跟扩展块数量或百分比50%FREE，不能指定全部vg空间，因创建lv元数据也占据了vg的空间）</br>
lvremove /dev/vgname/lvname  #删除逻辑卷 </br>
lvextend/lvresize -L +1G -r /dev/vgname/lvname #扩展逻辑卷(加号必写，否则为指定空间大小），-r：调整其上使用的文件系统大小 </br>
lvextend/lvresize -r -l +15%FREE /dev/vgname/lvname #扩展逻辑卷 </br>
lvextend /dev/vgname/lvname /dev/sdc1 #将vgname中/dev/sdc1的全部空间扩展到lvname </br>
lvreduce -r -L -300M /dev/vgname/lvname #缩减lv </br>
lvs  #显示lv所有信息 </br>
  
# 创建逻辑卷 层级关系：磁盘分区->pv->vg->lv，步骤为：创建分区；创建pv，vg，lv；创建文件系统；持久化挂载
## 1.创建磁盘分区,并设置分区类型
[root@server3 ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk.


Command (m for help): p

Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 43130CCA-D46E-1C4C-90C1-8C4E3F313808

Device       Start     End Sectors Size Type
/dev/sdc1     2048 2099199 2097152   1G Linux LVM
/dev/sdc2  2099200 4196351 2097152   1G Linux LVM

Command (m for help): n
Partition number (3-128, default 3): 
First sector (4196352-41943006, default 4196352): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-41943006, default 41943006): +2G

Created a new partition 3 of type 'Linux filesystem' and of size 2 GiB.

Command (m for help): t
Partition number (1-3, default 3): 
Partition type or alias (type L to list all): lvm

Changed type of partition 'Linux filesystem' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Syncing disks.
## 2.创建pv
[root@server3 ~]# pvcreate /dev/sdc3
  Physical volume "/dev/sdc3" successfully created.
[root@server3 ~]# pvs
  PV         VG      Fmt  Attr PSize    PFree  
  /dev/sdb3  vgfiles lvm2 a--    <4.00g  <3.00g
  /dev/sdb4  vgswap  lvm2 a--    <5.00g      0 
  /dev/sdc1  myvg    lvm2 a--  1020.00m 860.00m
  /dev/sdc2  myvg    lvm2 a--  1020.00m 880.00m
  /dev/sdc3          lvm2 ---     2.00g   2.00g
## 3.创建vg
[root@server3 ~]# vgcreate test_vg /dev/sdc3
  Volume group "test_vg" successfully created
[root@server3 ~]# vgs
  VG      #PV #LV #SN Attr   VSize  VFree 
  myvg      2   1   0 wz--n-  1.99g <1.70g
  test_vg   1   0   0 wz--n- <2.00g <2.00g
  vgfiles   1   1   0 wz--n- <4.00g <3.00g
  vgswap    1   1   0 wz--n- <5.00g     0 
## 4.创建lv
[root@server3 ~]# lvcreate -n test_lv -L 500M test_vg
  Logical volume "test_lv" created.
[root@server3 ~]# lvs
  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv    myvg    -wi-a----- 300.00m                                                    
  test_lv test_vg -wi-a----- 500.00m                                                    
  myfiles vgfiles -wi-ao----   1.00g                                                    
  lvswap  vgswap  -wi-ao----  <5.00g
## 5.创建文件系统
[root@server3 ~]# mkfs.ext4 /dev/test_vg/test_lv 
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 512000 1k blocks and 128016 inodes
Filesystem UUID: 81a92e27-6d91-47ff-a3c9-8b17b67bb8db
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185, 401409

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done 
## 6.持久化挂载
echo "/dev/test_vg/test_lv /mount defaults 0 0" >> /etc/fstab
[root@server3 ~]# mount -a
[root@server3 ~]# lsblk
NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                   8:0    0   20G  0 disk 
├─sda1                8:1    0  600M  0 part /boot/efi
├─sda2                8:2    0    1G  0 part /boot
├─sda3                8:3    0    2G  0 part [SWAP]
└─sda4                8:4    0 16.4G  0 part /
sdb                   8:16   0   20G  0 disk 
├─sdb1                8:17   0  1.1G  0 part /mydata
├─sdb2                8:18   0    2G  0 part [SWAP]
├─sdb3                8:19   0    4G  0 part 
│ └─vgfiles-myfiles 253:1    0    1G  0 lvm  /myfiles
└─sdb4                8:20   0    5G  0 part 
  └─vgswap-lvswap   253:0    0    5G  0 lvm  [SWAP]
sdc                   8:32   0   20G  0 disk 
├─sdc1                8:33   0    1G  0 part 
│ └─myvg-mylv       253:2    0  300M  0 lvm  
├─sdc2                8:34   0    1G  0 part 
│ └─myvg-mylv       253:2    0  300M  0 lvm  
└─sdc3                8:35   0    2G  0 part 
  └─test_vg-test_lv 253:3    0  500M  0 lvm  /mount
sr0                  11:0    1  8.9G  0 rom  /rhel
## 7.扩展或缩减lv
[root@server3 ~]# lvs
  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv    myvg    -wi-a----- 300.00m                                                    
  test_lv test_vg -wi-ao---- 500.00m                                                    
  myfiles vgfiles -wi-ao----   1.00g                                                    
  lvswap  vgswap  -wi-ao----  <5.00g                                                    
[root@server3 ~]# vgs
  VG      #PV #LV #SN Attr   VSize  VFree 
  myvg      2   1   0 wz--n-  1.99g <1.70g
  test_vg   1   1   0 wz--n- <2.00g <1.51g
  vgfiles   1   1   0 wz--n- <4.00g <3.00g
  vgswap    1   1   0 wz--n- <5.00g     0 
[root@server3 ~]# lvextend -r -L +200M /dev/test_vg/test_lv 
  Size of logical volume test_vg/test_lv changed from 500.00 MiB (125 extents) to 700.00 MiB (175 extents).
  File system ext4 found on test_vg/test_lv mounted at /mount.
  Extending file system ext4 to 700.00 MiB (734003200 bytes) on test_vg/test_lv...
resize2fs /dev/test_vg/test_lv
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/test_vg/test_lv is mounted on /mount; on-line resizing required
old_desc_blocks = 4, new_desc_blocks = 6
The filesystem on /dev/test_vg/test_lv is now 716800 (1k) blocks long.

resize2fs done
  Extended file system ext4 on test_vg/test_lv.
  Logical volume test_vg/test_lv successfully resized.
[root@server3 ~]# lvs
  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv    myvg    -wi-a----- 300.00m                                                    
  test_lv test_vg -wi-ao---- 700.00m                                                    
  myfiles vgfiles -wi-ao----   1.00g                                                    
  lvswap  vgswap  -wi-ao----  <5.00g                                                    
[root@server3 ~]# lvreduce -r -L -200M /dev/test_vg/test_lv 
[root@server3 ~]# lvs
  LV      VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv    myvg    -wi-a----- 300.00m                                                    
  test_lv test_vg -wi-ao---- 500.00m                                                    
  myfiles vgfiles -wi-ao----   1.00g                                                    
  lvswap  vgswap  -wi-ao----  <5.00g  
## 8.扩展或缩减vg
[root@server3 ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk.


Command (m for help): n
Partition number (4-128, default 4): 
First sector (8390656-41943006, default 8390656): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-41943006, default 41943006): +500M

Created a new partition 4 of type 'Linux filesystem' and of size 500 MiB.

Command (m for help): w
The partition table has been altered.
Syncing disks.

[root@server3 ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk.


Command (m for help): t
Partition number (1-4, default 4): 
Partition type or alias (type L to list all): lvm

Changed type of partition 'Linux filesystem' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Syncing disks.

[root@server3 ~]# pvcreate /dev/sdc4
  Physical volume "/dev/sdc4" successfully created.
[root@server3 ~]# vgextend test_vg /dev/sdc4
  Volume group "test_vg" successfully extended
[root@server3 ~]# vgs
  VG      #PV #LV #SN Attr   VSize  VFree 
  myvg      2   1   0 wz--n-  1.99g <1.70g
  test_vg   2   1   0 wz--n-  2.48g  1.99g
  vgfiles   1   1   0 wz--n- <4.00g <3.00g
  vgswap    1   1   0 wz--n- <5.00g     0 
[root@server3 ~]# vgreduce test_vg /dev/sdc4
  Removed "/dev/sdc4" from volume group "test_vg"

