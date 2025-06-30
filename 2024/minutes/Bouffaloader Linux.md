# Linux 系统适配

## 背景

要在 RISC-V 架构下启动 Linux 内核，通常的方法是先启动 SBI 固件（运行在M态），再启动操作系统内核（运行在S态），运行过程中的权限变化如下：

```SQL
     [ User Mode ]
        |      ^
        |      |
      ecall   sret
ABI     |      |
        v      |
   [ Supervisor Mode ]
        |      ^
        |      |
      ecall    mret
SBI     |      |
        v      |
    [ Machine Mode ]
```

除了这个方法外，我们也可以将内核的 MMU 功能关闭，让内核直接运行在M态，在没有SBI固件的情况运行：

```SQL
     [ User Mode ]
        |      ^
        |      |
      ecall   mret
ABI     |      |
        v      |
    [ Machine Mode ]
```

Bouffaloder 作为一款零阶段引导程序，我们希望它能够在没有 SBI 固件的情况下完成引导，启动 Non-MMU Linux 内核。

## 适配目标

通过 bouffaloader 启动 BL808 Linux 内核，需要一个 zImage 格式的内核和相应的 dtb 设备树二进制文件

## 适配工作

首先来到官方的 BL808 定制 Linux Kernel 文档中：

[BL808 Custom Linux Kernel - OpenBouffalo](https://openbouffalo.org/index.php/BL808_Custom_Linux_Kernel#Relocate_the_SDK)

然后依照文档搭建 SDK 环境，从[Releases · openbouffalo/buildroot_bouffalo](https://github.com/openbouffalo/buildroot_bouffalo/releases)下载  `riscv64-buildroot-linux-gnu_sdk-buildroot.tar.gz` SDK 文件，或是选择从源码构建 SDK

### 从源码构建SDK

BL808 Buildroot：

```
mkdir buildroot_bouffalo && cd buildroot_bouffalo
git clone https://github.com/buildroot/buildroot
git clone https://github.com/openbouffalo/buildroot_bouffalo
export BR_BOUFFALO_OVERLAY_PATH=$(pwd)/buildroot_bouffalo
cd buildroot
make BR2_EXTERNAL=$BR_BOUFFALO_OVERLAY_PATH pine64_ox64_defconfig
make SDK
```

将 SDK 放至与`buildroot/`和`buildroot_bouffalo/`同级目录下，重定位 SDK：

```
cd ~/buildroot_bouffalo/
cp -r ~/buildroot_bouffalo/buildroot/output/host/ ~/buildroot_bouffalo/bl808-sdk/
cd bl808-sdk
./relocate-sdk.sh
```

[可选] 将环境名称添加至 $PS1，激活环境时显示前缀 (RISCV) ，在文件`environment-setup`中添加该行：

```
PS1="(riscv) $PS1"
```

激活环境（每次使用都需要激活环境）：

```
source environment-setup
```

### 从 releases 下载 SDK

首次安装，进入 SDK 目录，运行`./relocate-sdk.sh`

然后每次使用只需激活环境`source ./environment-setup`

### 编译 Non-MMU Linux

这里使用了指南中的原始仓库，切换分支为`bl808/all`：

```
cd ~/buildroot_bouffalo/
git clone https://github.com/openbouffalo/linux
cd linux
git checkout bl808/all
```

使用BL808的默认配置，从`arch/riscv/configs/`复制到`.config`文件：

```
cp arch/riscv/configs/bl808_defconfig .config
```

修改内核配置：

```
make menuconfig
```

对于 MMU 选项，menuconfig 中虽有显示但无法直接修改：

```
-*- MMU-based Paged Memory Management Support
```

需要在`arch/riscv/kconfig`中手动修改：

```
config PORTABLE
        bool
        default !NONPORTABLE
        #select EFI
        #select MMU
        select OF
```

重新修改内核配置发现 MMU 选项已经解锁，取消选中并保存，编译内核：

```
make
```

得到内核镜像文件和设备树二进制文件：

```
DTC     arch/riscv/boot/dts/bouffalolab/bl808-sipeed-m1s.dtb
DTC     arch/riscv/boot/dts/bouffalolab/bl808-pine64-ox64.dtb
LDS     scripts/module.lds
0BJCOPY arch/riscv/boot/Image
GZIP    arch/riscv/boot/Image.gz
Kernel: arch/riscv/boot/Image.gz is ready
```

### DTB 准备

在官方的 DTS 文件中，串口输出是通过 SBI 完成的。而我们的 Non-MMU Linux 不需要 SBI，需要修改`/arch/riscv/boot/dts/bouffalolab/bl808-sipeed-m1s.dts`为 uart3：

```
# bootargs = "console=ttyS0,2000000 loglevel=8 earlycon=sbi root=/dev/mmcblk0p2 rootwait rootfstype=ext4";

bootargs = "console=ttys0,2000000n8 earlycon=uart3,0x30002000 root=/dev/mmcblk0p2 rw rootwait quiet";
```

编译 dts 为 dtb：

```
make dtbs
```

编译后可得到 DTB 文件。另外在 Bouffaloder 中可以先将串口初始化确保 uart3 能正常输出：

```Rust
fn main(p: Peripherals, c: Clocks) -> ! {
    let tx: Pad<GLBv2,16,MmUart> = p.gpio.io16.into mm uart();
    let rx: Pad<GLBv2,17,MmUart>= p.gpio.io17.into mm uart();
    let config: Config = Uartconfig::default().set baudrate(2000000.Bd());
    let mut serial: Serial<UART3,(Pad<GLBv2,16, …>, …)> = p.uart3.freerun::<3>(config,(tx,rx)，&c);
    writeln!(serial, "Welcome to bouffaloader! from uart3").ok();
```

### 引导启动

编译 Bouffaloder 并烧录到开发板，Bouffaloder 正常运行，有预期的输出，但跳转到内核后，无任何输出

```
Welcome to bouffaloader🦀!
PSRAM initialization success
initializing sdcard...
sdcard initialized success:size = 119.21 GB
filesystem initialized success.
read config success: bootargs = "console=ttys0,2000000n8 earlycon=uart3,0x30002000 root=/dev/mmcblk0p2 rw rootwait quiet"
load dtb file success,size = 3874 bytes
load zImage file success,size = 7941208 bytes
load files from sdcard success.
```

### GDB调试

发现 Linux 内核卡在bc到cc指令中无限循环：

```
(gdb) x/10i 0x500023b8
   0x500023b8: j     0x50002328
   0x500023ba: ret
   0x500023bc: csrrw tp,mscratch,tp 
   0x500023c0: bnez  tp,0x500023cc
   0x500023c4: csrr  tp,mscratch
   0x500023c8: fld   fs1,0(sp)
   0x500023ca: lw    s0,32(s0)
=> 0x500023cc: unimp
   0x500023ce: unimp
   0x500023d0: .insn 4, 0x01023103
(gdb)
```

分析这部分汇编代码，并未发现明显问题。在内核入口处断点，单步调试发现异常跳转地址寄存器 mtvec 被设置为 0x500023bc，在该地址下断点，发现首个异常位置是 0x5050b058。内核在进入异常处理时，会尝试从 mscratch 读取需要恢复到的地址，但此时该地址为0，会再次进入异常处理，无限循环：

```
   0x500023bc:  csrrw   tp,mscratch,tp
=> 0x500023c0:  bnez    tp,0x500023cc
   0x500023c4:  csrr    tp,mscratch
   0x500023c8:  sd      sp,16(tp) # 0x10
   0x500023cc:  sd      sp,24(tp) # 0x18
   0x500023d0:  ld      sp,16(tp) # 0x10
   0x500023d4:  addi    sp,sp,-288
   0x500023d6:  sd      ra,8(sp)
   0x500023d8:  sd      gp,24(sp)
   0x500023da:  sd      t0,40(sp)
```

分析首个触发异常的位置 0x5050b058，该指令尝试读取内存，但 s1 寄存器为 0x80开头的无效地址。分析前后代码发现该地址与 pc 有关，可能是重定位相关代码：

```
0x5050b02c:  sw      a5,100(s1)
0x5050b02e:  auipc   s5,0x24e
0x5050b032:  addi    s5,s5,-86
0x5050b036:  auipc   s3,0x32
0x5050b03a:  addi    s3,s3,-502
0x5050b03e:  li      s2,0
0x5050b040:  auipc   s8,0x127
0x5050b044:  addi    s8,s8,-1488
0x5050b048:  li      s7,32
0x5050b04c:  auipc   s6,0x127
0x5050b050:  addi    s6,s6,-1428
0x5050b054:  ld      s1,0(s5)
0x5050b058:  ld      a3,0(s1) // 首个异常出现的位置
0x5050b05a:  ld      a4,32(s1)
0x5050b05c:  ld      a6,152(s1)
0x5050b060:  lw      a5,148(s1)
0x5050b064:  beqz    a3,0x5050b06e
0x5050b066:  beqz    a4,0x5050b06e
```

在编译内核时，也有 0x80 开头的地址相关参数，而我们的内核运行在`0x5000_0000`上，推测可能与这些参数有关，需要进一步调整并测试

## 后续工作

1. 尝试修正内核编译中基地址相关参数，调试运行：

`PHYS_RAM_BASE`是物理内存的起始地址，`PAGE_OFFSET`是Linux内核空间的虚拟起始地址：
```
config PAGE_OFFSET
        hex
        default 0xC0000000 if 32BIT && MMU
        default 0x80000000 if !MMU
        default 0xff60000000000000 if 64BIT
...
config PHYS_RAM_BASE
        hex "Platform Physical RAM address"
        depends on PHYS_RAM_BASE_FIXED 
        default "0x80000000"
        help
          This is the physical address of RAM in the system. It has to be
          explicitly specified to run early relocations of read-write data
          from flash to RAM.
```

内核命令行是启动 Linux 内核时传递的一组参数，用于配置内核行为和设置硬件参数，通常由引导程序传递给内核，如`console=ttyS0,115200`设置内核控制台设备表示使用串口设备 ttyS0 波特率为115200、`root=/dev/sda1`指定根文件系统所在设备表示根文件系统位于 /dev/sda1 设备上等：

```
Boot options --->
	() Built-in kernel command line // 发现处于未选中状态
```

2. 同时探索基于 SBI 固件的 Linux 内核启动方式
3. 尝试适配 RT-Thread

[RT-Thread-博流BL808三核编译运行上手指南RT-Thread问答社区 - RT-Thread](https://club.rt-thread.org/ask/article/80a1c03cbccc6ca7.html)

## 参考资料

[BL808 Custom Linux Kernel - OpenBouffalo](https://openbouffalo.org/index.php/BL808_Custom_Linux_Kernel)
[BL808 Buildroot - OpenBouffalo](https://openbouffalo.org/index.php/BL808_Buildroot)
[openbouffalo/buildroot_bouffalo: Linux Image for the BL808 CPU by Bouffalo Lab](https://github.com/openbouffalo/buildroot_bouffalo)
[openbouffalo/linux: Linux kernel source tree](https://github.com/openbouffalo/linux)
[bouffalolab/bl808_linux](https://github.com/bouffalolab/bl808_linux)