# coremark

## 摘要
CoreMark是一个综合基准，用于测量嵌入式系统中使用的中央处理器(CPU)的性能。它是在2009由eembc的shay gal-on开发的，旨在成为一个行业标准，取代过时的dehrystone基准。代码用C编写，包含以下算法：列表处理(增删改查和排序)、矩阵操作(公共矩阵操作)、状态机(确定输入流是否包含有效数字)和CRC。用户可以自由的下载Coremark，并移植到自己的平台上运行，随后就可以看到分数。

## 测试项介绍

### 文件介绍

```
├── barebones	 --移植到裸机环境下需要修改的目录
│   ├── core_portme.c		--移植的目标平台配置信息
│   ├── core_portme.h		--计时以及板级初始化实现
│   ├── core_portme.mak		--该子目录的makefile
│   ├── cvt.c
│   └── ee_printf.c			--打印函数串口发送实现
├── core_list_join.c	--列表操作程序
├── core_main.c			--主程序
├── coremark.h			--项目配置与数据结构的定义头文件
├── coremark.md5
├── core_matrix.c		--矩阵运算程序
├── core_state.c		--状态机控制程序
├── core_util.c			--CRC计算程序
├── cygwin				--x86 cygwin和gcc 3.4（四核，双核和单核系统）的测试代码
│   ├── core_portme.c
│   ├── core_portme.h
│   └── core_portme.mak
├── freebsd				--以下同理，是在不同操作系统下的测试代码
│   ├── ...
├── LICENSE.md
├── linux
│   ├── ...
├── linux64
│   ├── ...
├── macos
│   ├── ...
├── Makefile
├── README.md			--自述文件，CoreMark项目的基本介绍
├── rtems
│   ├── ...
└── simple
    ├── ...
    └──
```

### make 目标

- run - 默认目标，创建 run1.log 和 run2.log
- run1.log - 用性能参数运行基准测试，并输出到 run1.log
- run2.log - 用验证参数运行基准测试，并输出到 run2.log
- run3.log - 用配置文件生成参数运行基准测试，并输出到 run3.log
- compile - 编译基准测试可执行文件
- link - 链接基准测试可执行文件
- check - 测试可能未修改的源文件的 MD5
- clean - 清理临时文件

### 编译/使用
根据 README 说法，只需要在 coremark 文件夹下执行 make 即可进行编译与测试。测试结果会出现在 Results 文件夹中，其中 run1.log 是测试结果。

## 测试计划
coremark 总体测试时间较短。
计划同时测试 10 次取平均数值。

## 操作步骤

```
$ git clone https://github.com/eembc/coremark.git
$ cd coremark
$ make
```

### 测试结果

```
2K performance run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 12915
Total time (secs): 12.915000
Iterations/Sec   : 8517.228029
Iterations       : 110000
Compiler version : GCC13.1.0
Compiler flags   : -O2 -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
			(e.g. code in flash, data on heap etc)
seedcrc          : 0xe9f5
[0]crclist       : 0xe714
[0]crcmatrix     : 0x1fd7
[0]crcstate      : 0x8e3a
[0]crcfinal      : 0x33ff
Correct operation validated. See README.md for run and reporting rules.
CoreMark 1.0 : 8517.228029 / GCC13.1.0 -O2 -DPERFORMANCE_RUN=1  -lrt / Heap

```

### GCC 10.4 + xtheadc

:::warning
由于 RevyOS 系统 GCC 版本已升级到 14，故目前 T-Head 优化 GCC 所使用的仓库 `c910v` 已
被弃用。**本节所述内容失效**，请等待后续更新。（[追踪链接 #124](https://github.com/revyos/revyos/issues/124)）
:::

```bash
make XCFLAGS="-march=rv64gv0p7_zfh_xtheadc -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns  -msignedness-cmpiv -fno-code-hoisting -mno-thread-jumps1 -mno-iv-adjust-addr-cost -mno-expand-split-imm"
```

```
2K performance run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 15129
Total time (secs): 15.129000
Iterations/Sec   : 13219.644392
Iterations       : 200000
Compiler version : GCC10.4.0
Compiler flags   : -O2 -march=rv64gv0p7_zfh_xtheadc -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns  -msignedness-cmpiv -fno-code-hoisting -mno-thread-jumps1 -mno-iv-adjust-addr-cost -mno-expand-split-imm -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
                        (e.g. code in flash, data on heap etc)
seedcrc          : 0xe9f5
[0]crclist       : 0xe714
[0]crcmatrix     : 0x1fd7
[0]crcstate      : 0x8e3a
[0]crcfinal      : 0x4983
Correct operation validated. See README.md for run and reporting rules.
CoreMark 1.0 : 13219.644392 / GCC10.4.0 -O2 -march=rv64gv0p7_zfh_xtheadc -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns  -msignedness-cmpiv -fno-code-hoisting -mno-thread-jumps1 -mno-iv-adjust-addr-cost -mno-expand-split-imm -DPERFORMANCE_RUN=1  -lrt / Heap
```

### GCC 14.2 + xthead matrix

```bash
make XCFLAGS="-march=rv64imafd_xtheadba_xtheadbb_xtheadbs_xtheadcmo_xtheadcondmov_xtheadfmemidx_xtheadfmv_xtheadint_xtheadmac_xtheadmemidx_xtheadmempair_xtheadsync -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns -fno-code-hoisting"
```

运行结果：

```
2K performance run parameters for coremark.
CoreMark Size    : 666
Total ticks      : 18353
Total time (secs): 18.353000
Iterations/Sec   : 10897.400970
Iterations       : 200000
Compiler version : GCC14.2.0
Compiler flags   : -O2 -march=rv64imafd_xtheadba_xtheadbb_xtheadbs_xtheadcmo_xtheadcondmov_xtheadfmemidx_xtheadfmv_xtheadint_xtheadmac_xtheadmemidx_xtheadmempair_xtheadsync -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns -fno-code-hoisting -DPERFORMANCE_RUN=1  -lrt
Memory location  : Please put data memory location here
                        (e.g. code in flash, data on heap etc)
seedcrc          : 0xe9f5
[0]crclist       : 0xe714
[0]crcmatrix     : 0x1fd7
[0]crcstate      : 0x8e3a
[0]crcfinal      : 0x4983
Correct operation validated. See README.md for run and reporting rules.
CoreMark 1.0 : 10897.400970 / GCC14.2.0 -O2 -march=rv64imafd_xtheadba_xtheadbb_xtheadbs_xtheadcmo_xtheadcondmov_xtheadfmemidx_xtheadfmv_xtheadint_xtheadmac_xtheadmemidx_xtheadmempair_xtheadsync -O3 -funroll-all-loops -finline-limit=500 -fgcse-sm -fno-schedule-insns -fno-code-hoisting -DPERFORMANCE_RUN=1  -lrt / Heap
```
