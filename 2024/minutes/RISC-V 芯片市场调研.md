
# RISC-V 芯片市场调研

**调研目前市面上已有的RISC-V芯片型号、厂商、发布年月、核 IP 的名称、典型板子和售价**

#### 1. 兆易创新 GD32VF103 系列
- **芯片型号**：GD32VF103 系列
- **厂商**：兆易创新 (GigaDevice)
- **发布年月**：未明确提及具体发布日期，但技术文档的最新更新时间为 2024-06-03
- **核 IP 名称**：Bumblebee RISC-V 处理器内核
- **典型板子**：未明确提及具体板子型号，但提供了多种封装选项，如 QFN36、LQFP48、LQFP64 和 LQFP100
- **售价**：价格大约不到 5 美元
    
**详细说明**： 兆易创新的 GD32VF103 系列 MCU 采用 Bumblebee RISC-V 处理器内核，主频高达 108MHz，支持高达 128KB Flash 和 32KB SRAM。该系列 MCU 集成了丰富的外设资源，如 USART+UART、I2C、SPI、CAN2.0B、USB FS、I2S、EXMC（外部存储器控制器）等。提供了多种封装选项，以适应不同的应用需求。该系列 MCU 适用于工业控制、消费电子、新兴 IoT、边缘计算、人工智能领域和一系列垂直市场应用。

#### 2. 乐鑫科技 ESP32-C3

- **芯片型号**：ESP32-C3
- **厂商**：乐鑫科技
- **发布年月**：2020 年 11 月
- **核 IP 名称**：RISC-V 32 位单核处理器
- **典型板子**
    - ESP32-C3-LCDKit：搭载 ESP32-C3-MINI-1 模组，支持 I2C 和 SPI 接口屏幕，具有旋转编码器、红外模块和音频播放功能 。
    - ESP32-C3-DevKit-RUST-1：基于 ESP32-C3-MINI-1 模组，支持 2.4 GHz Wi-Fi 和 Bluetooth 5 (LE)，板载 6DoF IMU、温湿度传感器和 USB (Type-C) 接口 。
- **售价**：ESP32-C3 的售价在不同平台有所不同，例如在深圳立创商城的总价金额为 0 元（需咨询客服），而在阿里巴巴上，ESP32-C3-Lyra 开发板售价为 210 元 。
    
**详细说明**： 乐鑫科技的 ESP32-C3 系列芯片是低功耗、高集成度的 MCU 系统级芯片 (SoC)，集成 2.4 GHz Wi-Fi 和低功耗蓝牙（BLE）双模无线通信。该系列芯片搭载乐鑫自研的 RISC-V 32 位单核处理器，时钟频率为 160 MHz。ESP32-C3 系列芯片具有低功耗性能和射频性能，支持多个外部 SPI、Dual SPI、Quad SPI、QPI flash，适用于智能家居、工业自动化、医疗保健、消费电子产品等多个领域。

#### 3. 全志科技 D1
- **芯片型号**：D1
- **厂商**：全志科技
- **发布年月**：2021 年 4 月 15 日
- **核 IP 名称**：平头哥玄铁 C906
- **典型板子**：D1-H 哪咤开发板，基于全志 D1-H 芯片，集成阿里平头哥 C906 RISC-V 核心，支持 64bit RISC-V 指令集和 Linux 系统 。
- **售价**：基于 D1-H 的开发板如哪咤开发板和 DevTerm R-01 的售价分别为 239 美元 。
    

**详细说明**： 全志科技的 D1 芯片采用平头哥玄铁 C906 64 位 RISC-V（RV64GCV）处理器内核，主频高达 1GHz。D1 是全志科技首款基于 RISC-V 指令集的芯片，集成了阿里平头哥 64 位 C906 核心，支持 RVV，可支持 Linux、RTOS 等系统。同时支持最高 4K 的 H.265/H.264 解码，内置一颗 HiFi4 DSP，最高可外接 2GB DDR3，可以应用于智慧城市、智能汽车、智能商显、智能家电、智能办公和科研教育等多个领域。

#### 4. 深圳中微 ANT32RV56xx

- **芯片型号**：ANT32RV56xx
- **厂商**：深圳中微半导体（CMSemicon）
- **发布年月**：2020 年 12 月
- **核 IP 名称**：芯来科技 N100 系列 RISC-V 处理器
- **典型板子**：未明确提及具体板子型号，但 ANT32RV56xx 是中微半导体推出的首款集成 RISC-V 内核的 32 位微控制器，适用于高算力、低功耗的消费电子应用 。
- **售价**：目前没有找到具体的深圳中微 ANT32RV56xx 的售价信息 。

**详细说明**： 深圳中微半导体的 ANT32RV56xx 系列芯片搭载芯来科技 N100 超低功耗 RISC-V 处理器内核，集成模拟外设并简化设计，可满足消费电子对高算力、低功耗的要求。该系列芯片秉承 CMSemicon 混合信号 SoC 设计理念，ANT32RV56xx 简化设计，在单一封装中集成丰富外设，内置 2 路运算放大器和 2 路比较器，2 路可编程增益放大器，可取代片外运放和比较器用于无线充电解码电路。

#### 5. 时擎科技 AT1000 系列

- **芯片型号**：AT1000 系列
- **厂商**：时擎科技
- **发布年月**：2022 年 6 月
- **核 IP 名称**：RISC-V 主控 TM500
- **典型板子**：AT1000 系列端侧智能芯片（AT16xx/18xx），提供 100G 高能效 AI 算力，适用于离线语音控制、麦克风阵列、入门级智能影像应用等 。
- **售价**：时擎科技 AT1000 开发板售价为 199 元人民币 。

**详细说明**： 时擎科技的 AT1000 系列端侧智能芯片搭载 TM500 主控处理器，主频 300MHz，拥有 400DMIPS 处理能力，支持单精度浮点运算。AT1000 搭载 Timesformer 智能处理器，支持 4 核心的 DSP 簇和专用的人工智能专用处理器，可提供 100GOPS 的高能效比算力。此外，AT1000 支持片上语音模拟/数字输入输出和处理，适用于智能家电、智能家居、智能照明、智能卫浴、智能机电、智能玩具等消费类智能硬件领域。

#### 6. 泰凌微 Tlsr 9 系列

- **芯片型号**：TLSR9 系列
- **厂商**：泰凌微电子
- **发布年月**：2020 年
- **核 IP 名称**：采用 RISC-V 内核
- **典型板子**：
    - B91 通用开发板：支持 OpenHarmony 主干版本，适用于多种智能终端产品 。
    - Mars_B91 开发板：由底板和子板组成，支持 ZigBee、BLE、Thread 等通讯协议，适用于 Matter 应用开发