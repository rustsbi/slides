会议主题：RustSBI 卫星安全固件情况交流

时间：2025年10月10日

地点：在线

参会人员:  [@luojia65](https://github.com/luojia65) [@13584452567](https://github.com/13584452567) [@woshiluo](https://github.com/woshiluo) [@Richardhongyu](https://github.com/Richardhongyu) [@Placebo27](https://github.com/Placebo27) [@jf-li00](https://github.com/jf-li00) [@soxsx](https://github.com/soxsx) [@NanaHigh](https://github.com/NanaHigh) [@Plucky923](https://github.com/Plucky923) [@wangyanyu1817](https://github.com/wangyanyu1817) [@hirameki34](https://github.com/hirameki34)

1. 讨论需要的时间线。下周一到周日联调，需要在下周一之前需要一个能够启动的镜像，下周日之前提供一个可以更换的镜像。最好在下周日之前固定固件，后续更新会很麻烦。第一次发射 10 月调试，第二次也许在 12 月调试。
2. 讨论现有的硬件模块。投票器是带抗辐射的（FPGA），其他部分并非抗辐射的。提到了 d1 没有 USB 引出。
3. 介绍了现在 RustSBI 的启动顺序。SyterKit -› RustSBI Prototyper -› (U-Boot ->) Linux / RROS -> 星务。同时介绍了可视化的烧写/编辑软件 EVBHelper。
4. 讨论在没有 usb 口的情况下如何烧写。一种可能性是在运行状态写以外设方式刷写。
5. 讨论 d1 板上的测试情况，以及先上普通 Linux 的可能性。
6. 介绍了 EVBHelper 的功能。fel 刷写，dtb 解析修改，image 的打包和刷写功能。
7. 讨论单个 flash 内多个固件以避免宇宙射线环境下复杂性的可能性。主要应在工程上。
8. 其他讨论。
