# 快速上手Salus, 一个用于 RISC-V 系统的微型管理程序

本教程使用 Debian 12 来构建salus环境，对于 Ubuntu 也同样适用。
原仓库地址：https://github.com/rivosinc/salus

## 构建 ( 使用 Bazel )

```bash
git clone https://github.com/rivosinc/salus.git
cd salus
git submodule update --init
bazel build //:salus-all
```

## 运行

### 环境配置

最新的已测试的版本：

| 项目 | 分支 |
| ------- | ------ |
| Bazelisk | v1.25.0-release(https://github.com/bazelbuild/bazelisk) |
| QEMU    | https://github.com/rivosinc/qemu/tree/salus-integration-10312022 |
| Riscv-gnu-toolchain | https://gitee.com/k_riscv/riscv-gnu-toolchain |
| Linux   | https://github.com/rivosinc/linux/tree/salus-integration-10312022 |
| Buildroot| https://github.com/rivosinc/buildroot/tree/salus-integration-2022.08.2 |

Bazelisk：
本项目采用了 bazelisk 来管理 `bazel` 的版本，bazelisk 是一个用于管理 `bazel` 版本的工具，它可以帮助我们安装和切换不同版本的 `bazel`。
- Mac OS 系统可使用 `brew` 包管理器安装到系统中。
```bash
brew install bazelisk
```
- Windows 系统可从 Github 上下载 bazelisk 的exe文件，或使用 `choco` 包管理器安装到系统中。
```bash
choco install bazelisk
```
- Debian 系系统可从 Github 上下载 bazelisk 的deb包，并使用apt包管理器安装到系统中。
```bash
wget https://github.com/bazelbuild/bazelisk/releases/download/v1.25.0/bazelisk-amd64.deb
sudo apt install ./bazelisk-amd64.deb
```
- Bazelisk 也发行到了 npm 上，也可以通过 npm 包管理器下载。
```bash
npm install -g @bazel/bazelisk
```
- 此外，还可以直接下载 bazelisk 的二进制文件，并将其添加到系统的 `PATH` 环境变量中。
```bash
wget https://github.com/bazelbuild/bazelisk/releases/download/v1.25.0/bazelisk-linux-amd64
chmod +x bazelisk-linux-amd64
sudo mv bazelisk-linux-amd64 /usr/local/bin/bazel
echo 'export PATH="$PATH:/usr/local/bin/bazel"' >> ~/.bashrc && source ~/.bashrc
```
- 检查 bazelisk 是否安装成功。
```bash
bazel version
bazelisk version
```

QEMU：
- 为 QEMU 安装 `libslirp-dev` 以构建 SLIRP 网络协议栈。
```bash
sudo apt install libslirp-dev
```
- 安装必需的软件包。
```bash
sudo apt install git libglib2.0-dev libfdt-dev libpixman-1-dev zlib1g-dev ninja-build
```
- 安装推荐的软件包。
```bash
sudo apt install git-email
sudo apt install libaio-dev libbluetooth-dev libcapstone-dev libbrlapi-dev libbz2-dev
sudo apt install libcap-ng-dev libcurl4-gnutls-dev libgtk-3-dev
sudo apt install libibverbs-dev libjpeg8-dev libncurses5-dev libnuma-dev
sudo apt install librbd-dev librdmacm-dev
sudo apt install libsasl2-dev libsdl2-dev libseccomp-dev libsnappy-dev libssh-dev
sudo apt install libvde-dev libvdeplug-dev libvte-2.91-dev libxen-dev liblzo2-dev
sudo apt install valgrind xfslibs-dev
```
- 对于版本较新的 Debian 系系统，可能需要安装以下软件包。
```bash
sudo apt install libnfs-dev libiscsi-dev
```
- 对于其他发行版，请参考https://wiki.qemu.org/Hosts/Linux。
- 获取源代码并构建 QEMU。
```bash
git clone -b salus-integration-10312022 https://github.com/rivosinc/qemu.git
cd qemu
./configure --target-list=riscv64-softmmu --enable-kvm --enable-debug --disable-werror 
make -j$(nproc)
sudo make install -j$(nproc)
```
- 检查 QEMU 是否安装成功。
```bash
qemu-system-riscv64 --version
```
- 记住 QEMU 的主目录所在位置，之后使用`run_tellus.sh`会用到。

Riscv-gnu-toolchain：
- [官方的工具链](https://github.com/riscv-collab/riscv-gnu-toolchain)的部分 submodules 目前无法拉取，issues 里面说执行以下代码可以解决，但并没有用。
```bash
git config --global protocol.version 2
```
- 故使用 gitee 镜像拉取源码。
```bash
git clone https://gitee.com/k_riscv/riscv-gnu-toolchain.git
cd riscv-gnu-toolchain
```
- 安装必需的软件包。
```bash
sudo apt install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
```
- 编译并加入到环境变量。
```bash
./configure --prefix=/opt/riscv64
make linux -j$(nproc)
echo 'export PATH="$PATH:/opt/riscv64/bin"' >> ~/.bashrc && source ~/.bashrc
```
- 检查工具链是否安装成功。
```bash
riscv64-unknown-linux-gnu-gcc -v
```

Linux 内核：
- 安装必须的软件包。
```bash
sudo apt install flex bison
```
- 获取源代码并编译内核。
```bash
git clone -b salus-integration-10312022 https://github.com/rivosinc/linux.git
cd linux
ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- make defconfig Image -j$(nproc)
```
- 记住 Linux 的主目录所在位置，之后使用`run_linux.sh`会用到。

Buildroot:
- 获取源代码并编译。
```bash
git clone -b salus-integration-2022.08.2 https://github.com/rivosinc/buildroot.git
cd buildroot
make qemu_riscv64_virt_defconfig -j$(nproc) && make -j$(nproc)
```
- 记住 Buildroot 的主目录所在位置，之后使用 `run_buildroot.sh` 会用到。

Debian:
- 下载编译好的镜像[riscv64-virt](https://gitlab.com/api/v4/projects/giomasce%2Fdqib/jobs/artifacts/master/download?job=convert_riscv64-virt)并解压到某一文件夹，记住文件夹所在位置，之后使用 `run_debian.sh` 会用到。

### 在 QEMU 下运行 Salus
本项目不使用 Make/Cargo build，而是使用脚本来运行。

在顶级目录下运行：
```bash
QEMU=<path-to-qemu-directory> scripts/run_tellus.sh
```

许多变量可以使用命令行指定。例如，要使用不同版本的 qemu 和 3 个内核，可以执行以下操作：
```bash
QEMU=/scratch/qemu-salus NCPU=3 scripts/run_tellus.sh
```

所有其他使用 linux 运行 salus 的 make 目标都类似地工作。

### Linux 虚拟机

`scripts/run_linux.sh` 脚本会启动一个裸 Linux 内核作为宿主虚拟机，但由于缺少根文件系统，该虚拟机在启动到 `init` 阶段时会发生内核崩溃。
```
  QEMU=<path-to-qemu-directory> \
  LINUX=<path-to-linux-tree> \
  DEBIAN=<path-to-pre-baked-image> \
  scripts/run_linux.sh
```

要启动功能更完善的 Linux 虚拟机，请使用 `scripts/run_debian.sh` 脚本。该脚本将通过预构建的 Debian 初始内存磁盘（initrd）和根文件系统（rootfs）镜像，启动一个带有模拟存储和网络设备的 Debian 虚拟机。
```
  QEMU=<path-to-qemu-directory> \
  LINUX=<path-to-linux-tree> \
  DEBIAN=<path-to-pre-baked-image> \
  scripts/run_debian.sh
```

要启动一个基于 Buildroot 构建、带有 BusyBox 根文件系统的快速功能型 Linux 虚拟机，请使用 `scripts/run_buildroot.sh` 脚本。需预先编译上述 Buildroot 项目以生成支持网络功能的根文件系统。
```
    QEMU=<path-to-qemu-directory> \
    LINUX=<path-to-linux-tree> \
    BUILDROOT=<path-to-buildroot repo>
    scripts/run_buildroot.sh
```

启动后，您可以通过 SSH 登录到 VM，使用 `root:root` 用户名和密码，地址为`localhost:7722`。

可通过 `EXTRA_QEMU_ARGS` Makefile 变量添加额外的模拟设备。注意：虚拟机仅能使用支持 MSI/MSI-X 中断的 PCI 设备。
`virtio-pci` 设备也可通过添加 `iommu_platform=on,disable-legacy=on` 参数启用。
```
   EXTRA_QEMU_ARGS="-device virtio-net-pci,iommu_platform=on,disable-legacy=on" \
   ... \
   scripts/run_debian.sh
```

### 测试虚拟机

测试用的一对虚拟机位于 `test-workloads` 目录中。

`tellus` 是一个通过 `bazel build //test-workloads:tellus_guestvm_rule` 构建的测试目标，它在 VS 模式下运行，并提供了向在 HS 模式下运行的 `salus` 发送测试 API 调用的能力。

`guestvm` 是一个测试机密客户机。它由 `tellus` 启动，用于测试 TSM API 的客户机端。

一旦构建完成，你可以使用以下命令来运行它。

```
    QEMU=<path-to-qemu-directory> \
    scripts/run_tellus.sh
```

这将使用指定的 QEMU 启动 salus、tellus 和 guestvm。

## 开发

### Bazel

Bazel 和 Cargo 最重要的一点区别是处理 crates 包依赖的方式。
如果更改了依赖项，Cargo 会自动拾取更改。但使用 Bazel，必须同步更改。
提供了一个脚本可以帮助你完成这项工作。要重新固定更改，只需运行 `scripts/repin.sh`。

# 概述 - 初始原型

```
  +---U-mode--+ +-----VS-mode-----+ +-VS-mode-+
  |           | |                 | |         |
  |           | | +---VU-mode---+ | |         |
  |   Salus   | | | VMM(crosvm) | | |  Guest  |
  | Delegated | | +-------------+ | |         |
  |   Tasks   | |                 | |         |
  |           | |    Host(linux)  | |         |
  +-----------+ +-----------------+ +---------+
        |                |               |
   TBD syscall   SBI (COVH-API)    SBI(COVG-API)
        |                |               |
  +-------------HS-mode-----------------------+
  |       Salus                               |
  +-------------------------------------------+
                         |
                        SBI
                         |
  +----------M-mode---------------------------+
  |       Firmware(OpenSBI)                   |
  +-------------------------------------------+
```

### 宿主（Host）

通常为运行在 VS 模式 的 Linux 系统，是设备的主要操作系统。

核心职责
- 任务调度
- 内存分配（引导阶段由固件和 Salus 保留的内存除外）  
- 通过 Salus 提供的 COVH-API 管理客机虚拟机（启动/停止/调度）  
- 设备驱动与权限委派 

#### 虚拟机管理器（VMM）
运行在宿主用户空间的虚拟机管理组件：
- 实现方案：`qemu/kvm` 或 `crosvm`
- 核心功能：
  - 为客机配置内存和设备
  - 运行全虚拟化或半虚拟化设备
  - 通过 `vcpu_run` 启动客机

### 客机（Guests）
由宿主启动的运行在 VS 模式的客机操作系统：
- 可运行机密计算或共享计算负载
- 使用宿主共享或捐赠的内存
- 由宿主调度执行
- 可启动嵌套子客机（Sub-guests）
- 机密客机通过 COVG-API 访问 Salus/宿主服务

### Salus
本仓库代码实现的HS模式虚拟机监视器（Hypervisor）：
- 核心功能：
  - 启动宿主和客机
  - 管理客机隔离所需的第二阶段地址转换（Stage-2 Translations）和 IOMMU 配置
  - 将部分任务（如远程证明）委派给用户态助手（`u-mode helpers`）
  - 由可信固件/信任根（RoT）进行度量

---

### 固件（Firmware）
M 模式代码：
- 当前使用 OpenSBI 从 QEMU 加载到内存 `0x80200000` 处启动 Salus，并向其传递设备树（Device Tree）
- 上述指令使用 QEMU 内置的 OpenSBI。若需从头编译 OpenSBI，需在 QEMU 命令行中使用 `-bios` 参数指定 `fw_dynamic` 版本

---

### 向量扩展（Vectors）
Salus 可检测 CPU 是否支持向量扩展（Vector Extension）：
- 同一二进制文件可兼容支持或不支持向量扩展的处理器
- 若检测到硬件支持向量扩展，将自动启用向量化代码执行