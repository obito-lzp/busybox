# Kernel
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
make -j
```

5. 编译完成  
内核路径：arch/x86_64/boot/bzImage

6. 替换内核
```
make INSTALL_MOD_STRIP=1 modules_install
make install
```
