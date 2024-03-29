# slides

Repository for all slides and articles related to RustSBI

## Slides

Titles without a hyperlink indicates that slide of this speech is not public yet.

### [Technologies and Applications in RustSBI 0.3.0](2022/RustSBI%200.3.0%20发布会.pdf), Nov 2022

This speech is delivered on weekly project meeting of rCore community. It announces RustSBI
version 0.3.0 and shows important technologies and how RustSBI should be used. It includes
RISC-V hypervisor and emulator development with RustSBI, introduces fast trap procedure and
SBI testing framework, and shows how actual bootloader like Oreboot/LinuxBoot etc. should be
implemented. It also compares performance and latency data of RustSBI to its competitors.

### [Embedded Rust and RustSBI Firmware Design](2022/嵌入式Rust与RustSBI固件设计.pdf), Jul 2022

This talk is delivered on Rust China Conf 2022 hosted by Rustcc. It summerizes embedded Rust
development and its surrounding ecosystem. Then, it introduces firmware development on example
of RISC-V, and discusses on how to develop secure, reliable and robust firmware in Rust, as well as
firmware features like secure enclaves and firmware on asymmetic processors. Finally, it introduces
RustSBI and Oreboot, drawing an conclusion on how firmware would be developed under Rust ecosystem.

### [A Brief Guide to Secure Enclave Design in RustSBI Firmware](2022/安全孤岛RustSBI固件设计简明指南.pdf), Apr 2022

This speech is delivered on online meeting of Tsinghua University undergraduate projects.
It describes how RustSBI implementation would coexist with secure enclave RISC-V SBI extensions,
a possible way to detect exception within function while executing instruction, and a Rust way to
read supervisor memory from machine mode. It also introduces SBI implementations like Oreboot
and RustSBI-Unmatched, which can be used to develop secure enclave firmware projects.

### [RISC-V Bootloader Environment: Application and Standardization](2022/RISC-V引导程序环境：应用与规范.pdf), Apr 2022

Delivered online on discussion at open source RISC-V and Linux technology mettings. It includes
the concept of bootloader environment when it comes to RISC-V firmwares, describes scenearios
and necessarity of RISC-V firmware, how it should be developed using Rust langauge.
This speech introduces Oreboot and RustSBI bootloading firmware to community and companies,
and announces RustSBI's latest 0.2.2 version.

### [RustSBI Implementation under Asymmetric Multiprocessors](2022/非对称多核处理器的RustSBI实现.pdf), Jan 2022

This speech is initially delivered as a technology sharing in PLCT lab, Institute of Software,
Acadamy of Sciences in China. It includes a complete design of RustSBI under Freefom
U740 five-core asymmetric platform, how it cooperates with bootloader sequence,
and how this cooperated project contributes to bootloader projects and the Rust language itself.

### [HSM and SRST module on RustSBI](2021/RustSBI的多核管理和复位模块.pdf), Nov 2021

This slide is delivered on online weekly meeting of undergraduate projects in Tsinghua University.
This slide discusses how SBI v0.3 functions are implemented in RustSBI framework
and its trivial QEMU implementation. It also includes an initial concept of RustSBI
implementation under desktop processors with asymmetric cores.

### [Design and Implementation of RustSBI](2020/RustSBI的设计与实现.pdf), Dec 2020

Delivered in Beijing on a discussion preparation meeting of national operating system contest.
It includes a general introduction of RustSBI framework, announces RustSBI v0.1, and discusses
how RustSBI support backward compability to privileged specification v1.9 under K210 dual-core
platform.

### [The Rust Embedded System Development](2020/Rust语言与嵌入式开发.pdf), Dec 2020

This speech is delivered on Rust China Conf 2020 in Shenzhen. It summarizes the ecosystem,
existing infrastructure and future outlook of embedded Rust at that time, specially
how Rust language features, runtime designs and community empower embedded development.
It also announces the RustSBI project as a competitive bootloader module choice.

### [Operating Systems on Rust and RISC-V](2020/Rust语言与RISC-V操作系统.pdf), Aug 2020

This slide is delivered in Peng Cheng Laboratory in Shenzhen. It includes an overview of
software stack for educational RISC-V kernels, useful RISC-V hardware standard
for kernel development, and initial concept of RustSBI project which is a RISC-V SBI implemenatation.

## Blog article

###  [Rust in Embedded World](https://www.yuque.com/chaosbot/rust_magazine_2021/biydon), Jan 2021

This article is published on Vol.1, _Featured Rust_ in Jan 2021. It includes how Rust's
modular project design helps on embedded development, specially how important the procedure
macros and zero cost abstractions are in bare metal level. It also discusses how hardware to hardware
compability would be achieved under RustSBI software.

### [rCore Operating System Lab Final Report](https://github.com/luojia65/rcore-os-blog/blob/master/source/_posts/os-report-final-luojia65.md), Aug 2020

This report acts as a final summarize of research intern program at Peng Cheng Labortory.
It includes how RustSBI and its related projects would help in rCore, an unix like operating system
kernel written fully in Rust.
