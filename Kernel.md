# Kernel
0. 依赖
```
apt install make pkg-config libncurses-dev flex bison libssl-dev libelf-dev
```
1. 下载  
[源码](https://mirrors.edge.kernel.org/pub/linux/kernel/)
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
6. 制作initrd.img
```
mkinitramfs 5.15.0-25-generic -o /boot/initrd.img  // "/lib/modules/5.15.0-25-generic"
```
