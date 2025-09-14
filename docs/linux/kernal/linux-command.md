# Linux command
## help

1. man
```shell
man man
>...
The table below shows the section numbers of the manual followed by the
       types of pages they contain.

       1   Executable programs or shell commands
       2   System calls (functions provided by the kernel)
       3   Library calls (functions within program libraries)
       4   Special files (usually found in /dev)
       5   File formats and conventions, e.g. /etc/passwd
       6   Games
       7   Miscellaneous (including  macro  packages  and  conventions),  e.g.
           man(7), groff(7)
       8   System administration commands (usually only for root)
       9   Kernel routines [Non standard]

       A manual page consists of several sections.

man passwd

man 5 passwd

man -a passwd

# to tell a command is builtin or external command
[root@9f43d8bad94f /]# type help
help is a shell builtin
[root@9f43d8bad94f /]# type ls
ls is /usr/bin/ls

```

2. help
```shell
# following both work in rockylinux (with man installed)
cd --help
ls --help
```

3. info

info should also be installed in rockylinux, but as I thought, `man` is great enough

## top
当然可以！`top` 命令是 Linux 系统中最重要的性能监控工具之一。它的输出可以分为两个主要部分：**摘要信息**和**进程列表**。

我会详细解释每一部分的含义。

---

### 第一部分：系统摘要信息（前 5-7 行）

#### 第 1 行：系统概览 (top...)
```
top - 18:20:15 up 45 days,  8:30,  2 users,  load average: 0.05, 0.10, 0.15
```
*   `18:20:15`：当前系统时间。
*   `up 45 days, 8:30`：系统已运行时间（uptime）。这里表示系统已经连续运行了45天8小时30分钟。
*   `2 users`：当前登录到系统的用户数量。
*   `load average: 0.05, 0.10, 0.15`：**系统平均负载**，这是非常关键的指标。
    *   三个数字分别代表过去 **1分钟**、**5分钟**、**15分钟** 的平均负载。
    *   **如何理解负载？**：对于单核CPU，1.00表示CPU刚好满负荷。如果超过1.00，表示有进程在排队等待CPU。
    *   **多核CPU**：需要将负载除以CPU核心数来评估。例如，一个4核CPU的系统，负载为4.00才算满负荷。如果负载持续高于CPU核心数，说明系统过载。

#### 第 2 行：任务信息 (Tasks...)
```
Tasks: 215 total,   1 running, 214 sleeping,   0 stopped,   0 zombie
```
*   `total`：当前系统中的进程总数。
*   `running`：正在运行或在运行队列中等待运行的进程数。
*   `sleeping`：处于睡眠状态的进程数（等待某个事件发生，如I/O操作完成）。
*   `stopped`：被停止的进程数（例如，通过 `Ctrl+Z` 暂停）。
*   `zombie`：**僵尸进程**数。这是已终止但其父进程尚未回收其资源的进程。如果这个数字不为0且持续增加，可能表示有程序存在bug。

#### 第 3 行：CPU 使用率 (%Cpu(s)...)
```
%Cpu(s):  1.2 us,  0.5 sy,  0.0 ni, 98.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```
这里显示了CPU时间的分配百分比。所有值的总和为100%。
*   `us` (user)：用户空间进程占用CPU的百分比。
*   `sy` (system)：内核空间进程占用CPU的百分比。
*   `ni` (nice)：被调整过优先级的用户进程（niced）占用CPU的百分比。
*   `id` (idle)：CPU空闲时间的百分比。**这个值越高，说明CPU越空闲**。
*   `wa` (I/O wait)：CPU等待I/O操作完成的时间百分比。**这个值过高通常表示磁盘或网络I/O存在瓶颈**。
*   `hi` (hardware interrupts)：处理硬件中断所花费的CPU时间。
*   `si` (software interrupts)：处理软件中断所花费的CPU时间。
*   `st` (steal time)：在虚拟化环境中，被宿主机（Hypervisor）“偷走”的CPU时间。如果你的虚拟机感觉慢，这个值过高可能说明宿主机资源紧张。

#### 第 4 行：物理内存使用 (MiB Mem...)
```
MiB Mem :  15927.0 total,    500.2 free,   8000.0 used,   7426.8 buff/cache
```
（单位可以是 KiB, MiB, GiB）
*   `total`：总物理内存大小。
*   `free`：完全未被使用的内存大小。
*   `used`：已被使用的内存大小。
*   `buff/cache`：被用作**内核缓冲区**和**页面缓存**的内存大小。**Linux会利用空闲内存来缓存数据以提高性能，当应用程序需要时，这部分内存可以被快速回收。所以，即使 `used` 看起来很高，如果 `buff/cache` 占了大部分，也未必是坏事。**

#### 第 5 行：交换分区使用 (MiB Swap...)
```
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.  15000.0 avail Mem
```
*   `total`：总交换分区（Swap）大小。
*   `free`：空闲的交换分区大小。
*   `used`：已使用的交换分区大小。**如果这个值持续大于0，说明物理内存曾经不足，系统使用了交换分区，这可能会导致性能下降（因为磁盘比内存慢得多）**。
*   `avail Mem` (Available Memory)：**这是评估可用内存的更好指标**。它估算在不发生交换的情况下，可供应用程序使用的内存大小。

---

### 第二部分：进程列表

进程列表默认按CPU使用率降序排列。

| 列名        | 含义                                                                                                                              |
| :---------- | :-------------------------------------------------------------------------------------------------------------------------------- |
| **PID**     | 进程的唯一ID。                                                                                                                    |
| **USER**    | 进程所有者的用户名。                                                                                                              |
| **PR**      | 进程的优先级（Priority）。内核根据这个值来决定调度顺序。                                                                          |
| **NI**      | Nice值。用户可调整的优先级修正值（范围-20到19）。**值越小，优先级越高**。                                                           |
| **VIRT**    | 进程使用的虚拟内存总量（Virtual Memory Size）。包括代码、数据和共享库等。                                                         |
| **RES**     | **常驻内存**（Resident Memory Size）。进程当前使用的、未被换出的物理内存大小。**这个值乘以4KB通常就是该进程实际占用的物理内存大小**。 |
| **SHR**     | 共享内存（Shared Memory）。可能被其他进程共享的内存大小（如共享库）。                                                             |
| **S**       | 进程状态（Status）。<br> - **R**：运行中（Running）或可运行（Runnable）<br> - **S**：睡眠中（Sleeping）<br> - **D**：不可中断的睡眠（通常是I/O操作）<br> - **Z**：僵尸进程（Zombie）<br> - **T**：被跟踪（Traced）或停止（Stopped） |
| **%CPU**    | 进程自上次更新以来所使用的**CPU时间百分比**。注意：在多核CPU上，这个值可能超过100%（例如，一个进程完全占用了2个核心，就会显示200%）。 |
| **%MEM**    | 进程使用的**物理内存百分比**（相对于总物理内存）。                                                                                |
| **TIME+**   | 进程自启动以来所使用的总CPU时间。                                                                                                 |
| **COMMAND** | 启动此进程的命令名称。                                                                                                            |

---

### 常用交互命令（在 `top` 运行时按键）

*   **`h`**：显示帮助。
*   **`P`** (大写)：按CPU使用率排序（默认）。
*   **`M`** (大写)：按内存使用率（%MEM）排序。
*   **`T`** (大写)：按TIME+（CPU时间）排序。
*   **`N`** (大写)：按PID排序。
*   **`k`**：杀死一个进程（会提示输入PID）。
*   **`r`**： renice一个进程（调整优先级，会提示输入PID和新的nice值）。
*   **`1`** (数字)：显示所有CPU核心的单独使用情况（再按一次切换回汇总视图）。
*   **`z`**：切换彩色/黑白显示。
*   **`q`**：退出 `top`。
