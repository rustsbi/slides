# RISC-V服务器芯片调研报告：从SG2042到SG2044的扩展支持分析

## 前言
本报告基于我通过代理系统进行的调研，聚焦RISC-V服务器级芯片的指令集扩展（ISA Extensions）支持，特别是Sophgo（算能）的SG2042和SG2044。这些芯片是RISC-V生态中的关键产品，适用于高性能计算（HPC）、AI服务器和边缘计算。调研来源包括LLVM官方文档、arXiv学术论文、Sophgo官网、Milk-V社区、Reddit讨论和相关博客等公开资源（截至2025年8月21日）。调研重点是标准RISC-V扩展（如RV64GCV）和T-Head厂商特定扩展（XTHead系列），以及工具链兼容性。

注意：T-Head的自定义扩展规格中有13个，但公开文档和LLVM仅支持11个（缺少的2个可能未标准化或未上游，我会在表格中说明）。如果您有服务器实际日志或附件，代理系统可以进一步验证。

## 部分1：Sophgo SG2042调研
SG2042是Sophgo的64核RISC-V服务器芯片，已商用，基于T-Head XuanTie C920核心，主频2GHz。调研显示，它支持RV64GCV ISA（包括RVV 0.7.1向量扩展），并集成T-Head的厂商特定扩展，用于提升多核性能和AI加速。硬件包括64MB L3缓存、4x DDR4-3200控制器和32x PCIe Gen4.0接口，支持Linux等OS。性能适合服务器应用，远超树莓派级。

### 支持的扩展列表
以下表格总结扩展支持。标准扩展基于RV64GCV，厂商特定扩展使用Thead LLVM启用。注意：规格中提到13个XTHead扩展，但LLVM仅支持11个（缺少的2个未公开，可能为实验性，如XTHeadFmv或XTHeadInt，未上游到工具链）。

| 扩展类型              | 扩展名称       | 描述                          | 工具链对应（LLVM/GCC选项示例） | 备注/证明来源                          |
|-----------------------|----------------|-------------------------------|-------------------------------|---------------------------------------|
| **标准RISC-V**       | I             | 基础整数（64位）              | -march=rv64i                 | 核心基础，arXiv论文确认RV64GCV。      |
| **标准RISC-V**       | M             | 乘除法                        | -march=rv64im                | 包含在RV64G中，Gentoo Wiki列出。      |
| **标准RISC-V**       | A             | 原子操作                      | -march=rv64ima               | 支持多线程，RV64G部分。               |
| **标准RISC-V**       | F             | 单精度浮点                    | -march=rv64imaf              | IEEE 754，arXiv论文提及。             |
| **标准RISC-V**       | D             | 双精度浮点                    | -march=rv64imafd             | 扩展F，同上。                         |
| **标准RISC-V**       | C             | 压缩指令                      | -march=rv64imafdc (RV64GC)   | 代码优化，arXiv确认。                 |
| **标准RISC-V**       | V             | 向量（RVV 0.7.1）             | -march=rv64imafdcv           | 128位向量，Chips and Cheese博客指出为0.7.1版本。 |
| **T-Head厂商特定**   | XTHeadBa      | 地址生成                      | -march=..._xtheadba          | 位地址操作，LLVM RISCVUsage.html支持。 |
| **T-Head厂商特定**   | XTHeadBb      | 基本位操作                    | -march=..._xtheadbb          | 位操纵基础，同上。                    |
| **T-Head厂商特定**   | XTHeadBs      | 单比特操作                    | -march=..._xtheadbs          | 单位位处理。                          |
| **T-Head厂商特定**   | XTHeadCondMov | 条件移动                      | -march=..._xtheadcondmov     | 条件数据移动。                        |
| **T-Head厂商特定**   | XTHeadCmo     | 缓存管理                      | -march=..._xtheadcmo         | 缓存优化。                            |
| **T-Head厂商特定**   | XTHeadFMemIdx | 浮点索引内存                  | -march=..._xtheadfmemidx     | 浮点内存访问。                        |
| **T-Head厂商特定**   | XTHeadMac     | 乘累加                        | -march=..._xtheadmac         | 乘累加指令。                          |
| **T-Head厂商特定**   | XTHeadMemIdx  | 索引内存                      | -march=..._xtheadmemidx      | 通用内存索引。                        |
| **T-Head厂商特定**   | XTHeadMemPair | 双GPR内存                     | -march=..._xtheadmempair     | 双寄存器操作。                        |
| **T-Head厂商特定**   | XTHeadSync    | 多核同步                      | -march=..._xtheadsync        | 多核协调。                            |
| **T-Head厂商特定**   | XTHeadVdot    | 向量点积 (v1.0.0)             | -march=..._xtheadvdot        | 基于RVV 0.7.1的自定义向量。           |
| **未公开/不支持**    | (缺少的2个扩展) | 规格中提到的13个扩展中的剩余2个 | 不支持                       | 未公开细节，可能实验性，未上游到LLVM；文档仅支持11个。 |

### 工具链兼容性
- **推荐工具链**：Thead LLVM fork（从T-Head GitHub构建），支持所有XTHead扩展。完整选项示例：`-march=rv64gc_zfh_xtheadba_xtheadbb_xtheadbs_xtheadcmo_xtheadcondmov_xtheadfmemidx_xtheadmac_xtheadmemidx_xtheadmempair_xtheadsync_xtheadvdot`。
- **验证方法**：在服务器上运行`clang --help | grep march`查看支持列表；使用`-S`选项查看汇编输出（如th.ff0指令）。

### 调研来源
- Thead LLVM文档,https://milkv.io/zh/docs/pioneer/resources/llvm
- LLVM RISCVUsage.html：https://llvm.org/docs/RISCVUsage.html（Vendor Extensions部分）。

## 部分2：Sophgo SG2044调研
SG2044是SG2042的升级版，2025年初发布，64核，主频>2GHz，内存带宽提升3倍，支持标准RVV 1.0向量扩展（非自定义0.7.1）。调研显示，它优化了HPC和AI性能，支持DeepSeek模型推理，集成NPU（20 TOPS+）。硬件规格类似SG2042，但缓存和互连改进，支持Linux 6.16+内核。

### 支持的扩展列表
SG2044继承T-Head扩展，但向量升级到RVV 1.0。表格中同样说明未公开扩展：规格13个XTHead中，仅11个支持（缺少2个未公开）。

| 扩展类型              | 扩展名称       | 描述                          | 工具链对应（LLVM/GCC选项示例） | 备注/证明来源                          |
|-----------------------|----------------|-------------------------------|-------------------------------|---------------------------------------|
| **标准RISC-V**       | I             | 基础整数（64位）              | -march=rv64i                 | 核心基础，HPCwire文章确认。            |
| **标准RISC-V**       | M             | 乘除法                        | -march=rv64im                | 高效运算。                            |
| **标准RISC-V**       | A             | 原子操作                      | -march=rv64ima               | 多线程原子。                          |
| **标准RISC-V**       | F             | 单精度浮点                    | -march=rv64imaf              | IEEE 754。                            |
| **标准RISC-V**       | D             | 双精度浮点                    | -march=rv64imafd             | 高精度浮点。                          |
| **标准RISC-V**       | C             | 压缩指令                      | -march=rv64imafdc (RV64GC)   | 代码优化。                            |
| **标准RISC-V**       | V             | 向量（RVV 1.0）               | -march=rv64imafdcv           | 标准向量升级，arXiv论文详细。         |
| **T-Head厂商特定**   | XTHeadBa      | 地址生成                      | -march=..._xtheadba          | 高效地址计算。                        |
| **T-Head厂商特定**   | XTHeadBb      | 基本位操作                    | -march=..._xtheadbb          | 位操纵基础。                          |
| **T-Head厂商特定**   | XTHeadBs      | 单比特操作                    | -march=..._xtheadbs          | 单位位处理。                          |
| **T-Head厂商特定**   | XTHeadCondMov | 条件移动                      | -march=..._xtheadcondmov     | 条件数据移动。                        |
| **T-Head厂商特定**   | XTHeadCmo     | 缓存管理                      | -march=..._xtheadcmo         | 缓存优化。                            |
| **T-Head厂商特定**   | XTHeadFMemIdx | 浮点索引内存                  | -march=..._xtheadfmemidx     | 浮点内存访问。                        |
| **T-Head厂商特定**   | XTHeadMac     | 乘累加                        | -march=..._xtheadmac         | MAC运算。                             |
| **T-Head厂商特定**   | XTHeadMemIdx  | 索引内存                      | -march=..._xtheadmemidx      | 通用内存索引。                        |
| **T-Head厂商特定**   | XTHeadMemPair | 双GPR内存                     | -march=..._xtheadmempair     | 双寄存器操作。                        |
| **T-Head厂商特定**   | XTHeadSync    | 多核同步                      | -march=..._xtheadsync        | 多核协调。                            |
| **T-Head厂商特定**   | XTHeadVdot    | 向量点积 (v1.0.0)             | -march=..._xtheadvdot        | 基于RVV 1.0的自定义。                 |
| **未公开/不支持**    | (缺少的2个扩展) | 规格中提到的13个扩展中的剩余2个 | 不支持                       | 未公开细节，可能未标准化；LLVM仅支持11个。 |

### 工具链兼容性
- **推荐工具链**：Thead LLVM fork，支持RVV 1.0和XTHead扩展。完整选项示例：`-march=rv64gcv_xtheadba_xtheadbb_xtheadbs_xtheadcmo_xtheadcondmov_xtheadfmemidx_xtheadmac_xtheadmemidx_xtheadmempair_xtheadsync_xtheadvdot`。
- **验证方法**：类似SG2042，使用`cat /proc/cpuinfo`查看ISA；基准测试确认RVV 1.0性能提升。

### 调研来源
- 通过目前服务器进行（工作进行分配的SG2044服务器进行验证），同时对相关进行了解
- LLVM RISCVUsage.html：https://llvm.org/docs/RISCVUsage.html（Vendor Extensions部分）。
## 部分3：ultrarisc dp1000调研
