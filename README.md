# RISC-V CPU Design (Hardware Comprehensive Training Course Project)

My undergraduate course project for *Hardware Comprehensive Training*: a 32-bit **RISC-V (RV32) CPU** built from scratch in **Logisim**. It covers single-cycle and pipelined datapaths, dynamic branch prediction, and interrupt handling, along with a suite of RISC-V assembly test programs.

> This repository is a cleaned-up archive of the course project: directories have been reorganized and all assembly sources converted to UTF-8.
> Third-party software (Logisim, RARS) and the official manuals are large and freely downloadable, so they are not included. See the links below.
>
> Directory and file names are kept in Chinese as in the original project; English descriptions are given alongside.

## Features

| Category | Implementation |
| --- | --- |
| Single-cycle CPU | Single-cycle datapath and hardwired controller for 24 and 24+4 instructions |
| Interrupts | Single-level and multi-level interrupts (context saved via an EPC hardware stack or a memory stack) |
| Pipeline | Ideal pipeline, bubble (stall) pipeline, redirect (forwarding) pipeline, pipeline with interrupts |
| Branch prediction | Dynamic branch prediction (BHT loading, LRU eviction) |
| Other | LED marquee display, team task (Rapid-Roll game on the single-cycle CPU) |

## Project Structure

```
.
├── circuits/                                               # Logisim circuits (the core work)
│   ├── CPU完整设计.circ (Complete CPU Design)              #   Top-level design integrating all subcircuits
│   ├── 单周期/ (Single-Cycle)                              #   Single-cycle CPU (24 / 24+4 instructions, single-/multi-level interrupts)
│   ├── 流水线/ (Pipeline)                                  #   Ideal / bubble / redirect pipelines, pipeline interrupts, dynamic branch prediction
│   ├── 中断信号仿真/ (Interrupt Signal Simulation)         #   Interrupt button signal generation and test circuits
│   ├── 其他/ (Other)                                       #   LED display, team task (Rapid-Roll), storage library
│   └── 正确封装图.png (Correct Packaging Diagram)          #   Reference image for correct subcircuit packaging
├── tests/                                                  # RISC-V assembly tests (.asm source + .hex machine code)
│   ├── 单指令测试/ (Single-Instruction Tests)              #   Single-instruction tests: AUIPC/BGE/LB/MUL/SRA/...
│   ├── 功能测试/ (Functional Tests)                        #   Functional programs: sorting, marquee, shifts, B-type, JMP, etc.
│   ├── 流水线与分支预测/ (Pipeline and Branch Prediction)  #   Ideal pipeline and branch prediction tests
│   ├── 中断测试/ (Interrupt Tests)                         #   Single-/multi-level interrupt tests
│   └── benchmark/                                          #   ccab benchmark programs, Rapid-Roll
├── libs/                                                   # Logisim libraries required to open the circuits
│   ├── cs3410.jar                                          #   Cornell CS3410 component library (register file/RAM/ROM, etc.)
│   └── riscv-probe.jar                                     #   HUST course component library (hust2020.Components)
├── docs/                                                   # Design documents
│   ├── 设计表格/ (Design Tables)                           #   Instruction encoding / control signal / interrupt design tables (xlsx)
│   └── CCAB输出汇总.docx (CCAB Output Summary)             #   Expected benchmark outputs
└── README.md
```

## Dependencies (Download Separately)

| Tool | Purpose | Where to get it |
| --- | --- | --- |
| **Logisim-ITA 2.15** (Chinese-localized build) | Open and simulate the `.circ` circuits | Italian fork: <http://logisim.altervista.org> |
| **RARS** | Write/assemble RISC-V programs and generate `.hex` files | <https://github.com/TheThirdOne/rars/releases> |
| Java 8+ | Runs both tools (jar / exe) | <https://adoptium.net> |

> The circuits depend on `cs3410.jar` and `riscv-probe.jar` in `libs/`, which ship with the repository.
> **Logisim-ITA 2.15** is recommended, since the circuits were saved in that version's format.
> Other versions or Logisim-Evolution may fail to load the jar libraries.

## Usage

### Open a Circuit
1. Open any `.circ` file under `circuits/` in Logisim-ITA.
2. If a library is reported missing, choose `Project → Load Library → JAR Library`
   and load `libs/cs3410.jar` and `libs/riscv-probe.jar`.

### Load a Test Program
1. Right-click the instruction memory (ROM/RAM) in the circuit → `Load Image`, then pick the matching `.hex` under `tests/`.
2. Reset the clock to 0 and start the simulation (`Simulate → Ticks Enabled`, or tick manually).

### Generate a .hex with RARS
1. Open the `.asm` file in RARS.
2. Under **Settings → Memory Configuration**, choose **`Compact, data at address 0`** (this matches the design's fetch and memory addresses).
3. After assembling, use `File → Dump Memory` with the `Hexadecimal Text` format to export a `.hex` for Logisim.

## Test Programs

- **Single-instruction tests**: verify arithmetic, logic, memory, branch, and multiply/divide instructions one at a time.
- **Functional tests**: `risc-v-排序测试` (Sorting Test: sort 0–15 in descending order), `risc-v-走马灯测试` (Marquee Test: LED marquee),
  plus shift, branch, and jump programs.
- **Pipeline and branch prediction**: `risc-v-理想流水线测试` (Ideal Pipeline Test: 17 independent instructions to verify the 5-stage pipeline cycle count) and
  `risc-v-分支预测测试` (Branch Prediction Test: BHT loading, LRU eviction, prediction hit rate).
- **Interrupt tests**: single-level interrupts, and multi-level interrupts with both context-saving methods (EPC hardware stack / memory stack).
- **benchmark**: the `ccab` benchmark programs; expected outputs are in `docs/CCAB输出汇总.docx` (CCAB Output Summary).

## Notes

- This repository is a personal learning archive of the course project. Feel free to use it as a reference.
- The jar files in `libs/` and `circuits/其他/storage.circ` (其他 = Other) come from the course lab toolkit
  (Cornell CS3410 and the HUST course library). Copyright belongs to the original authors; they are included only to make the project reproducible.
- Third-party documents such as the RISC-V ISA manual and the course assignment are not included.
  See the official ISA specifications: <https://riscv.org/technical/specifications/>.
