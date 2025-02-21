# CPU 指令集

---

### 1. **CISC（复杂指令集计算机）**
   - 代表架构：**x86, x86-64（AMD64, Intel64）**
   - 主要特点：
     - 指令复杂、长度不固定
     - 支持内存直接寻址
     - 适用于通用计算，广泛用于 PC 和服务器

   - **常见 CISC 指令集**
     - **x86（IA-32）**：32 位架构，Intel 8086 及后续 CPU
     - **x86-64（AMD64/Intel64）**：64 位扩展版本
     - **MMX/SSE/AVX**：SIMD 指令集扩展，用于加速多媒体和科学计算
     - **VT-x/AMD-V**：虚拟化指令集

---

### 2. **RISC（精简指令集计算机）**
   - 代表架构：**ARM、RISC-V、MIPS、PowerPC、SPARC**
   - 主要特点：
     - 指令固定长度，解码简单
     - 依赖寄存器操作，内存访问通常仅限于`load/store`
     - 适用于移动设备、嵌入式系统、服务器和超算

   - **常见 RISC 指令集**
     - **ARM（AArch32/AArch64）**：手机、嵌入式、Apple M 系列芯片
     - **RISC-V**：开源指令集，适用于嵌入式、服务器和超算
     - **MIPS**：早期嵌入式和网络设备常用，现已衰落
     - **PowerPC**：IBM 服务器、游戏机（PS3 等）
     - **SPARC**：Sun Microsystems（已衰落）

---

### 3. **专用指令集**
   - **DSP（数字信号处理器）**：
     - TI C6000, Qualcomm Hexagon
     - 主要用于音频、视频处理、无线通信
   - **GPU 指令集**：
     - **NVIDIA PTX**、**AMD GCN/RDNA**、**Intel Xe**
     - 适用于图形渲染、并行计算（CUDA、OpenCL）
   - **量子计算指令集**：
     - **QASM**（量子汇编语言）
     - 适用于量子计算机（IBM Q、Google Sycamore）

---

### 4. **VLIW（超长指令字架构）**
   - 代表架构：Intel Itanium（IA-64）、TI C6000
   - 特点：
     - 多条指令并行执行
     - 依赖编译器优化，硬件设计复杂
   - 目前 VLIW 主要用于 DSP 和 GPU（如 AMD 旧架构）

---

### 5. **超长指令字（SIMD 指令集扩展）**
   - **x86 SIMD**：
     - **MMX**（多媒体扩展）
     - **SSE（SSE2, SSE3, SSE4）**
     - **AVX（AVX, AVX2, AVX-512）**
   - **ARM SIMD**：
     - **NEON**（32/64 位 ARM 的 SIMD 扩展）
     - **SVE（Scalable Vector Extension）**：ARM 服务器用
   - **RISC-V SIMD**：
     - **RVV（RISC-V Vector Extension）**，用于 HPC 和 AI

# 查看CPU 指令集

## 1.查看命令

```bash

# cat /proc/cpuinfo | grep flags

flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw topoext ssbd ibrs ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 xsaves clzero wbnoinvd arat umip pku ospke vaes vpclmulqdq rdpid overflow_recov succor fsrm

```

## 2.flags解释

### **1. 通用指令集**
- `fpu`（浮点单元）：支持浮点计算
- `mmx`（MultiMedia eXtensions）：多媒体加速指令
- `sse, sse2, ssse3, sse4_1, sse4_2, sse4a`（Streaming SIMD Extensions）：SIMD 指令扩展，用于多媒体和向量计算
- `avx, avx2`（Advanced Vector Extensions）：更高级的 SIMD 指令集，提高浮点计算性能
- `fma`（Fused Multiply-Add）：浮点乘加运算指令，提高数学运算效率
- `bmi1, bmi2`（Bit Manipulation Instructions）：位操作优化
- `movbe`（Move Big-Endian）：支持大小端转换
- `popcnt`（Population Count）：计算二进制 `1` 的个数（Hamming Weight）

### **2. 加密指令**
- `aes`（Advanced Encryption Standard）：AES 硬件加速
- `pclmulqdq`（Carry-Less Multiplication）：用于加速加密运算（如 GCM）
- `sha_ni`（SHA Extensions）：加速 SHA-1 和 SHA-256 加密

### **3. 虚拟化**
- `hypervisor`：表明 CPU 运行在虚拟化环境中（如 KVM、VMware）
- `vmmcall`：虚拟机管理器调用指令
- `vmx`/`smx`（可能缺失）：如果是 Intel CPU，通常会有 VT-x 虚拟化支持

### **4. 安全相关**
- `nx`（No-eXecute bit）：防止代码执行恶意注入的内存区域（DEP 机制）
- `smep`（Supervisor Mode Execution Prevention）：内核态防止执行用户态代码
- `smap`（Supervisor Mode Access Prevention）：内核态防止访问用户态数据
- `ibrs, ibpb, ssbd`：Spectre 和 Meltdown 漏洞缓解
- `umip`（User-Mode Instruction Prevention）：防止用户态访问某些特权指令
- `pku, ospke`（Protection Keys for Userspace）：细粒度内存保护机制

### **5. 存储相关**
- `clflushopt, clwb`：高速缓存刷新优化（适用于持久化内存）
- `erms`（Enhanced REP MOVSB/STOSB）：加速 `rep movsb` 和 `rep stosb`
- `wbnoinvd`（Write-Back No Invalidate）：提高写缓存效率

### **6. 其他**
- `rdseed, rdrand`：硬件随机数生成器
- `xsave, xsaveopt, xsavec, xsaves`：扩展状态存储指令
- `arat`（Always Running APIC Timer）：定时器保持运行
- `fsrm`（Fast Short REP MOV）：加速 `rep movsb`
- `rdpid`：提高进程 ID 访问效率
- `clzero`：优化清除缓存操作
- `overflow_recov, succor`：RISC-V 相关指令，可能用于错误恢复

---

