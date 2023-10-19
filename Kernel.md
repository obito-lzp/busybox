Kernel
0. 依赖
```
apt install make pkg-config libncurses-dev flex bison libssl-dev libelf-dev
```
1. 下载  
[源码](https://mirrors.edge.kernel.org/pub/linux/kernel/)
2. *指定硬件架构**
```
export ARCH=x86
```
3. 配置
```
make xxx_defconfig
make menuconfig
```
4. 编译
```
make -j  // 内核路径：arch/x86_64/boot/bzImage
```
5. 模块安装
```
make INSTALL_MOD_STRIP=1 modules_install
```
6. 制作initrd.img
```
mkinitramfs 5.15.0-25-generic -o /boot/initrd.img  // "/lib/modules/5.15.0-25-generic"
```
7. 内核安装
```
make install
```
