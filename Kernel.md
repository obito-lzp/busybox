# Kernel
0. 依赖
```
apt install make pkg-config libncurses-dev flex bison libssl-dev libelf-dev
```
1. 下载  
 https://mirrors.edge.kernel.org/pub/linux/kernel/
2. 配置
```
make ARCH=x86 x86_64_defconfig
make menuconfig
```
3. 编译
```
make -j  // 内核路径：arch/x86_64/boot/bzImage
```
4. 模块安装
```
make INSTALL_MOD_STRIP=1 modules_install
```
5. 内核安装
```
make install
```
6.头文件安装
```
make headers_install
```
7. 制作initrd.img
```
mkinitramfs 5.15.0-25-generic -o initrd.img  // "/lib/modules/5.15.0-25-generic"
```

8. 制作deb包
```
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main universe
apt install kernel-package fakeroot
fakeroot make-kpkg --initrd --append-to-version=-xfusion kernel_image kernel_headers
```

## 提示
给ubuntu系统换内核，可在 https://kernel.ubuntu.com/mainline/ 下载对应版本的linux-modules-*  
解压deb文件,提取config文件去编译内核
```
dpkg-deb -x linux-headers-*.deb deb/
cp deb/boot/config* linux*/.config
make menuconfig
make -j100
```
