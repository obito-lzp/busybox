# 用户目录更换挂载
0. 查看当前情况
```
# df -h
# fdisk -l
# du -d 1 -h ./ 
```

1. 新建分区
```
# fdisk /dev/sdb
# mkfs -t ext4 /dev/sdb1
```
2. 同步文件
```
# mkdir /mnt/new
# mount /dev/sdb1 /mnt/new
# sync -aXS /home/'user'/ /mnt/new/
# umount /mnt/new 
```

3. 目录切换
```
# rm -r /home/'user'
# mkdir /home/'user'
# mount /dev/sdb1 /home/'user'
# blkid
    复制sdb1的UUID：85bcf566-3814-4df6-8939-4cfb81ba678c
# vim /etc/fstab
    在最后插入
    UUID="85bcf566-3814-4df6-8939-4cfb81ba678c" /home/'user' ext4 defaults 0 2
