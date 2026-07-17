# RISC-V CPU 设计（硬件综合训练课程设计）

本科《硬件综合训练》课程设计，基于 **Logisim** 从零搭建的 32 位 **RISC-V（RV32）CPU**，
涵盖单周期、流水线、动态分支预测与中断处理，并配套 RISC-V 汇编测试用例。

> 本仓库为课程设计的整理归档版：目录已重新分类、汇编源文件统一转为 UTF-8 编码。
> 第三方软件（Logisim、RARS）与官方手册体积较大且可自行下载，未纳入版本库，见下方链接。

## 功能特性

| 类别 | 实现内容 |
| --- | --- |
| 单周期 CPU | 24 条 / 24+4 条指令的单周期数据通路与硬布线控制器 |
| 中断 | 单级中断、多级中断（EPC 硬件堆栈 / 内存堆栈两种现场保护方式） |
| 流水线 | 理想流水线、气泡（阻塞）流水线、重定向（前递）流水线、流水线 + 中断 |
| 分支预测 | 动态分支预测（BHT 表载入、LRU 淘汰策略） |
| 其他 | LED 走马灯显示、团队任务（Rapid-Roll 游戏版单周期 CPU） |

## 目录结构

```
.
├── circuits/                 # Logisim 电路（核心作品）
│   ├── CPU完整设计.circ       #   整合全部子电路的总设计文件
│   ├── 单周期/                #   单周期 CPU（24 / 24+4 指令、单级/多级中断）
│   ├── 流水线/                #   理想 / 气泡 / 重定向流水线、流水线中断、动态分支预测
│   ├── 中断信号仿真/          #   中断按键信号产生与测试模拟电路
│   ├── 其他/                  #   LED 显示、团队任务(Rapid-Roll)、storage 存储库
│   └── 正确封装图.png         #   子电路封装正确性参考图
├── tests/                    # RISC-V 汇编测试用例（.asm 源码 + .hex 机器码）
│   ├── 单指令测试/            #   单条指令验证：AUIPC/BGE/LB/MUL/SRA/... 等
│   ├── 功能测试/              #   排序、走马灯、移位、B 指令、JMP 等综合程序
│   ├── 流水线与分支预测/      #   理想流水线、分支预测专项测试
│   ├── 中断测试/              #   单级 / 多级中断测试程序
│   └── benchmark/            #   ccab 基准程序、Rapid-Roll
├── libs/                     # 打开电路所需的 Logisim 库（必需）
│   ├── cs3410.jar            #   Cornell CS3410 组件库（寄存器堆/RAM/ROM 等）
│   └── riscv-probe.jar       #   HUST 课程自定义组件库（hust2020.Components）
├── docs/                     # 设计文档
│   ├── 设计表格/              #   指令编码 / 控制信号 / 中断设计表（xlsx）
│   └── CCAB输出汇总.docx      #   benchmark 期望输出汇总
└── README.md
```

## 环境依赖（需自行下载）

| 工具 | 用途 | 获取方式 |
| --- | --- | --- |
| **Logisim-ITA 2.15**（汉化版） | 打开、仿真 `.circ` 电路 | 意大利分支：<http://logisim.altervista.org> |
| **RARS** | 编写/汇编 RISC-V 程序、生成 `.hex` | <https://github.com/TheThirdOne/rars/releases> |
| Java 8+ | 运行上述两者（jar / exe） | <https://adoptium.net> |

> 电路依赖 `libs/` 中的 `cs3410.jar` 与 `riscv-probe.jar`，这两个库已随仓库提供，
> 无需另行下载。建议使用 **Logisim-ITA 2.15**（电路的保存格式为该版本），
> 其他版本或 Logisim-Evolution 可能无法正确加载 jar 库。

## 使用方法

### 打开电路
1. 用 Logisim-ITA 打开 `circuits/` 下任一 `.circ` 文件。
2. 若提示缺少库，选择 `Project → Load Library → JAR Library`，
   分别加载 `libs/cs3410.jar` 与 `libs/riscv-probe.jar`。

### 加载测试程序
1. 在电路中右键指令存储器（ROM/RAM）→ `Load Image`，选择 `tests/` 下对应的 `.hex`。
2. 时钟置 0 后开始仿真（`Simulate → Ticks Enabled` 或手动打点）。

### 用 RARS 汇编生成 .hex
1. 用 RARS 打开对应 `.asm`。
2. **Settings → Memory Configuration 选择 `Compact, data at address 0`**（与本设计的取指/访存地址一致）。
3. 汇编后 `File → Dump Memory`，格式选 `Hexadecimal Text`，导出 `.hex` 供 Logisim 加载。

## 测试用例说明

- **单指令测试**：逐条验证算术/逻辑/访存/分支/乘除指令的正确性。
- **功能测试**：`risc-v-排序测试`（0–15 降序排序）、`risc-v-走马灯测试`（LED 走马灯）、
  移位、分支、跳转等综合程序。
- **流水线与分支预测**：`理想流水线测试`（17 条无相关指令，验证 5 段流水线周期数）、
  `分支预测测试`（BHT 载入 / LRU 淘汰 / 预测命中率）。
- **中断测试**：单级中断，以及多级中断的两种现场保护方式（EPC 硬件堆栈 / 内存堆栈）。
- **benchmark**：`ccab` 系列基准程序，期望输出见 `docs/CCAB输出汇总.docx`。

## 说明

- 本仓库为课程设计的个人学习归档，欢迎参考交流。
- `libs/` 中的 jar 及 `circuits/其他/storage.circ` 源自课程提供的实验工具包
  （Cornell CS3410、HUST 课程库），版权归原作者所有，仅为复现方便附带。
- RISC-V 指令集手册、课程任务书等第三方文档未纳入本仓库。
  指令集规范可参考官方：<https://riscv.org/technical/specifications/>。
