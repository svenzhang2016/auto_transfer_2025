# auto_transfer_2025

以下学习策略基于“80/20 法则”+“项目反向拆解”+“刻意练习”三原则设计，目标是在 90 天内让你从 C/C++ 高手蜕变为能独立交付自动化解决方案的工程师。整套方案分四阶段，每阶段 2–3 周，可并行 20 % 时间用于并行任务。每日净投入 3 h 即可，若可全职投入，可压缩到 45 天。

---

阶段 0　定位与装备（第 0 天，2 h）
1. 目标岗位画像

   关键词：工业自动化、运动控制、PLCopen、EtherCAT、CNC、机器人控制、边缘计算、实时 Linux、ROS 2、软 PLC（CODESYS）、IEC 61131-3、Functional Safety（ISO 13849）。  
2. 装备清单  
   - 硬件：二手 EtherCAT 总线伺服电机+驱动器一套（淘宝 600 元以内）、STM32F4/F7 板子一块、树莓派 4B、USB-CAN 分析仪。  
   - 软件：

     – 实时 Linux 镜像：PREEMPT-RT 或 Xenomai（已编译好的镜像 5 min 内烧录完成）。

     – 软 PLC：OpenPLC（开源）或 CODESYS Raspberry Pi SL（免费 2 h 运行）。

     – EtherCAT 主站：IgH EtherCAT Master（SOEM 轻量版即可）。

     – 仿真：Gazebo + ROS 2 控制插件（无需真机即可验证算法）。  

---

阶段 1　“把 C/C++ 变成 PLC 代码”——软 PLC + 实时编程（Week 1–2）
目标：用你最强的语言实现一个“伺服电机往返定位”程序，理解自动化核心闭环。

任务 1.1　OpenPLC Runtime 编译（1 晚）  
- 在树莓派上交叉编译 OpenPLC Runtime；把 .st 文件转成 C 代码，定位到 `generate_c.cpp`，单步跟踪，理解 IEC 61131-3 如何映射为 C 状态机。

输出：能在 STM32 上跑通闪烁 LED 的软 PLC 固件。

任务 1.2　实时线程与 EtherCAT 周期（2 晚）  
- 用 IgH 提供的 `ecrt_master_send()` 写 1 kHz 定时器线程；对比 PREEMPT-RT 与标准内核的抖动（`cyclictest` 测量）。

输出：打印实时位置、速度、力矩三闭环数据，CSV 格式，用 Python matplotlib 画曲线。

任务 1.3　反向拆解——把 PLCopen 运动控制 FB 用 C++ 重写（3 晚）  
- 只实现 `MC_MoveAbsolute` 与 `MC_Stop` 两个功能块，状态机严格照抄 PLCopen 规范。

输出：一份头文件 `plcopen_mc.hpp`，后续所有阶段直接复用。

里程碑：在示波器上看到 1 ms 周期抖动 < 50 µs，电机 0.1 mm 定位重复精度。

---

阶段 2　“总线即 API”——EtherCAT 与 CANopen 协议栈（Week 3–4）
目标：能在 30 min 内把一款陌生伺服驱动器跑起来。

任务 2.1　ESI 文件速读（30 min 练习）  
- 给 5 款不同品牌 ESI（XML），用脚本 `esi_extract.py` 自动输出映射表（索引、子索引、数据类型）。

输出：A4 纸打印“字典表”，现场面试可手写。

任务 2.2　SOEM 热插拔（2 晚）  
- 修改 `slaveinfo.c` 支持热插拔检测；拔掉电机再插上，10 ms 内恢复运行。

输出：演示视频 30 s，可直接放简历。

任务 2.3　CANopenNode 移植（2 晚）  
- 把 CANopenNode 移植到 STM32CubeIDE；用 NMT 心跳保护，掉线 100 ms 立即切断 PWM。

输出：安全相关经验，为后续功能安全铺路。

---

阶段 3　“控制算法落地”——运动学与 PID 自整定（Week 5–6）
目标：让机械臂画一个圆，圆度误差 < 0.5 mm。

任务 3.1　C++ 模板化运动学（2 晚）  
- 用 Eigen 写模板类 `SCARA_Kinematics<T>`，支持 float/double/fixed-point 三种类型，直接部署到 MCU。

输出：单元测试覆盖率 100 %，GitHub Actions 自动跑。

任务 3.2　PID 自整定（3 晚）  
- 实现 Relay Auto-tuner + Ziegler–Nichols 改进算法，自动输出 Kp/Ki/Kd；对比 MATLAB/Simulink 结果。

输出：一页 A4 参数表，面试可当场推导公式。

任务 3.3　前馈 + 模型预测（进阶，可选）  
- 把电机机电模型写成状态空间，用 ACADO 工具箱生成 C 代码，跑 1 ms MPC。

输出：伺服刚性提升 30 % 的实测曲线。

---

阶段 4　“项目包装 + 面试秒杀”（Week 7–10）
目标：简历出现“用 C++ 实现软 PLC + EtherCAT 总线 + 机器人控制”闭环项目，面试 5 min 内讲清系统架构。

任务 4.1　一个可展示的 Demo（3 天）  
- 树莓派 + 3D 打印小 SCARA，末端装笔，现场画公司 Logo。  
- 手机拍摄 60 s 竖屏视频，字幕加关键技术关键词。  

任务 4.2　技术博客“四部曲”（并行 2 周）  
   1) 《把 PLC 程序编译成 C++ 的 5 个步骤》  
   2) 《EtherCAT 主站 1 ms 周期抖动的 3 个隐藏杀手》  
   3) 《模板元编程写运动学：编译期求逆解》  
   4) 《PID 自整定公式推导与 C++ 实现》

输出：每篇 800 字 + 代码仓库链接，发布在知乎/简书/CSDN，SEO 关键词覆盖“C++ 自动化”“EtherCAT 实战”。

任务 4.3　简历“3 行法则”  
- 第 1 行：用 C++ 实现软 PLC，1 kHz 实时任务抖动 < 50 µs。  
- 第 2 行：自研 EtherCAT 主站，兼容 7 款伺服，热插拔恢复时间 10 ms。  
- 第 3 行：SCARA 机器人圆轨迹误差 0.3 mm，节拍提升 25 %。  

---

并行加速包（每天 30 min）
A. 刷题：LeetCode 只刷“位运算 + 内存池”两类题，保持 C/C++ 手感。

B. 速读：每天扫 5 篇 IEEE/arxiv 自动化论文摘要，用“电梯陈述”法复述 1 句。

C. 英语：跟读 YouTube “EtherCAT Technology Group”官方视频，模仿发音，面试可脱口而出。

---

常见陷阱与对策
1. 一头扎进 PLC 梯形图 → 先用自己的 C++ 优势打出“差异化”，再补梯形图（1 天即可）。  
2. 盲目买昂贵伺服 → 二手 400 W 伺服足够，总线一致即可。  
3. 忽略实时抖动 → 用 `ftrace` + `trace-cmd` 可视化，别凭感觉调。  
4. 不会讲故事 → 用“问题-量化-结果”模板，面试前录音自己 2 min 版本。

---

90 天里程碑日历（可打印贴墙）
Day 1–7　软 PLC 跑通 LED → 输出：Blinky 视频

Day 8–14　EtherCAT 伺服转一圈 → 输出：位置曲线 CSV

Day 15–30　SCARA 正逆解模板 → 输出：GitHub 仓库

Day 31–45　PID 自整定 + 画圆 → 输出：误差报告

Day 46–60　热插拔 + 安全停机 → 输出：演示视频

Day 61–75　博客四部曲 → 输出：外链集合

Day 76–90　简历 + 面试 mock → 输出：Offer

