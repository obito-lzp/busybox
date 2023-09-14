# BUSYBOX
1. 下载  
[源码下载](https://busybox.net/downloads/)
2. 解压
```
# tar xvf busybox-1.30.0.tar.bz2
```
3. 配置
```
# make menuconfig

Settings -->
    Build Options -->
        [*]Build BusyBox as a static binary (no shared libs)
```

4. 编译和安装
```
# make -j
# make install
```
默认安装路径：_install

5. 最小文件系统
```
# mkdir etc dev mnt
# mkdir -p proc sys tmp mnt
# mkdir -p etc/init.d/
# vim etc/fstab
proc        /proc           proc         defaults        0        0
tmpfs       /tmp            tmpfs    　　defaults        0        0
sysfs       /sys            sysfs        defaults        0        0
# vim etc/init.d/rcS
echo -e "Welcome to tinyLinux"
/bin/mount -a
echo -e "Remounting the root filesystem"
mount  -o  remount,rw  /
mkdir -p /dev/pts
mount -t devpts devpts /dev/pts
echo /sbin/mdev > /proc/sys/kernel/hotplug
mdev -s
# chmod 755 etc/init.d/rcS
# vim etc/inittab
::sysinit:/etc/init.d/rcS
::respawn:-/bin/sh
::askfirst:-/bin/sh
::ctrlaltdel:/bin/umount -a -r
# chmod 755 etc/inittab
# cd dev
# mknod console c 5 1
# mknod null c 1 3
# mknod tty1 c 4 1
```

6. 制作镜像  
思路：  
1.先制作一个空的镜像文件；  
2.然后把此镜像文件格式化为ext3格式；  
3.然后把此镜像文件挂载，并把根文件系统复制到挂载目录；  
4.卸载该镜像文件。  
5.打成gzip包。 
```
#!/bin/bash
rm -rf rootfs.ext3
rm -rf fs
dd if=/dev/zero of=./rootfs.ext3 bs=1M count=32
mkfs.ext3 rootfs.ext3
mkdir fs
mount -o loop rootfs.ext3 ./fs
cp -rf ./_install/* ./fs
umount ./fs
gzip --best -c rootfs.ext3 > rootfs.img.gz
```
