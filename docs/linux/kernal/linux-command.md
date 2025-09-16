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

理解 `man` 命令和 GNU 选项的风格是成为 Linux 命令行高手的关键一步。

我会分两部分来解答：1) 快速理解 `man` 页的结构；2) 如何高效地从中获取知识，特别是关于**选项**的部分。

---

### 第一部分：`man` 命令页面包含哪些内容？（结构解析）

`man` 手册页不是胡乱堆砌的，它有一个非常标准的结构。你不需要每次都从头读到尾，而是应该学会“按图索骥”，直接跳到你关心的部分。

一个典型的 `man` 页面通常包含以下**核心章节**（NAME 和 DESCRIPTION 是最重要的）：

| 章节标题 (NAME) | 中文含义 & 内容 |
| :--- | :--- |
| **NAME** | **名称**：命令名称和一行简要介绍。这就是 `whatis` 命令显示的内容。 |
| **SYNOPSIS** | **概要**：**极其重要！** 展示命令的**语法格式**，包括所有选项和参数的用法。 |
| **DESCRIPTION** | **描述**：**核心内容！** 详细描述命令的功能以及每个选项的**具体含义**。 |
| **OPTIONS** | **选项**： (有时合并于 DESCRIPTION 中) 详细解释每个选项（`-a`, `--all` 等）的作用。 |
| **EXAMPLES** | **示例**： (不是所有命令都有) **最佳学习材料！** 展示常用的命令示例。 |
| **EXIT STATUS** | **退出状态**： 命令执行成功后或失败后返回的代码及其含义。 |
| **SEE ALSO** | **参见**： **非常有价值！** 列出与当前命令相关的其他命令或文档。 |
| **AUTHOR, BUGS, COPYRIGHT** | **作者、已知缺陷、版权**： 这些是补充信息。 |

---

### 第二部分：如何快速获取知识（特别是GNU选项）

GNU 风格的选项通常以双连字符 `--` 开头，例如 `--help`, `--all`。它们比单字母选项（如 `-a`）更直观易记。

#### 技巧 1：直接查看 SYNOPSIS 和 DESCRIPTION

这是最标准的方法。

1.  **打开 `man` 页**：例如 `man ls`。
2.  **直接翻到 OPTIONS 部分**：
    *   按下 `/` 键，进入搜索模式。
    *   输入 `/^OPTIONS` 然后按回车。`^` 表示匹配行首，这样能精准定位到 OPTIONS 章节。
3.  **在 DESCRIPTION 中搜索**：
    *   同样按下 `/`，然后输入你要找的选项名，例如 `/--all`。
    *   按 `n` 键跳到下一个匹配项，`N` 键跳到上一个。

#### 技巧 2：使用 `--help` 选项（最快最常用！）

绝大多数 GNU 命令都支持这个选项。它能直接打印出**语法格式和选项的简要说明**，比 `man` 更简洁、更快速。

**用法：** `[命令] --help`

**例如：**
```bash
ls --help
```
输出会立即显示所有选项的列表和简短解释，非常适合快速查阅。
```
...
  -a, --all                  不隐藏任何以 . 开始的项目
  -A, --almost-all           列出除 . 及 .. 以外的任何项目
  -l                         使用较长格式列出信息
...
```

#### 技巧 3：在 man 页内使用搜索 (`/`)

正如前面提到的，这是导航长篇幅 `man` 页的利器。
*   `/PATTERN`： 向前搜索 “PATTERN”（如 `/--help`）
*   `?PATTERN`： 向后搜索 “PATTERN”
*   `n`： 跳到下一个匹配项
*   `N`： 跳到上一个匹配项

#### 技巧 4：使用 `apropos` 或 `man -k` 寻找命令

**This one not working in rockylinux**

如果你**连用什么命令都不知道**，只知道你想**做什么**，可以用这个命令。

它会在所有 `man` 页的 **NAME** 部分中进行搜索。

**例如：** 你想找一个“解压”的命令，但不知道是 `tar` 还是 `unzip`。
```bash
apropos "extract"
man -k "extract"
```
系统会列出所有简介中包含 “extract” 的命令。

---

### 总结与快速指南

| 你的需求 | 应该使用的命令 | 为什么 |
| :--- | :--- | :--- |
| **快速查看某个命令的所有选项和简要说明** | `ls --help` | **最快**，输出简洁，一目了然。 |
| **深入了解某个命令的详细用法、原理和示例** | `man ls` | **最详细**，包含所有背景知识和技术细节。 |
| **在详细的 `man` 页中快速定位（如选项）** | 进入 `man` 后按 `/^OPTIONS` | **高效导航**，直接跳到核心章节。 |
| **查找一个不知道名字的命令** | `apropos "关键词"` | **基于功能搜索**，帮助你发现命令。 |

**最终建议：**
*   日常使用中，**`[command] --help` 是你的首选工具**，因为它速度极快。
*   当 `--help` 无法满足（例如找不到某个选项的具体行为或想了解原理）时，再进入 **`man`** 手册进行深度阅读。
*   熟练使用 **`/`** 搜索来在 `man` 页中快速定位。


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


## pwd
pwd — Print Working Directory

## ls

`ls` — List directory contents

### Most Useful Parameters

#### Basic Options
```bash
# List all files including hidden ones (starting with .)
ls -a

# List in long format with detailed information
ls -l

# Combine -a and -l for detailed listing of all files
ls -la

# List in human-readable format (file sizes in KB, MB, GB)
ls -lh

# List all files with human-readable sizes
ls -lah
```

#### Sorting and Formatting
```bash
# Sort by modification time (newest first)
ls -lt

# Sort by modification time (oldest first)
ls -ltr

# Sort by file size (largest first)
ls -lS

# Sort by file size (smallest first)
ls -lSr

# Sort by name (case-insensitive)
ls -l

# Sort by name (case-sensitive)
ls -lU
```

#### Display Options
```bash

# Show directory contents recursively
ls -R

# Show only directories
ls -d */
```

#### Understanding `ls -d */`

The `ls -d */` command is a powerful combination that shows **only directories** in the current location. Let's break it down:

**Components:**
- `ls` - The list command
- `-d` - Directory option (treats directories as files, don't list their contents)
- `*/` - Shell glob pattern that matches only directories(anything ending with /)

**How it works:**
```bash
# Basic usage - shows only directory names
ls -d */

# Example output:
# dir1/  dir2/  dir3/

# Compare with regular ls (shows everything):
ls
# file1.txt  file2.log  dir1/  dir2/  dir3/

# Compare with ls -l (shows detailed info for everything):
ls -l
# -rw-r--r-- 1 user user 1024 Jan 1 12:00 file1.txt
# drwxr-xr-x 2 user user 4096 Jan 1 12:00 dir1/
# drwxr-xr-x 2 user user 4096 Jan 1 12:00 dir2/
```

**Why use `-d`?**
Without `-d`, `ls */` would try to list the **contents** of each directory:
```bash
# Without -d (shows contents of each directory)
ls */
# dir1:
# file1.txt  file2.txt
# 
# dir2:
# subdir1/  file3.txt

# With -d (shows only directory names)
ls -d */
# dir1/  dir2/
```

**Useful variations:**
```bash
# Show directories with detailed info
ls -ld */

# Show only directories (alternative method)
ls -l | grep "^d"

# Show directories with specific pattern
ls -d */ | grep "test"
```

#### Advanced Options
```bash
# Show file timestamps in ISO format
ls -l --time-style=iso

# Show only files (not directories)
ls -p | grep -v /$

# Show files with specific permissions
ls -l | grep "^d"  # Show only directories
ls -l | grep "^-"  # Show only regular files

# Show files modified in last 24 hours
ls -lt | head -10

# Show files with specific extensions
ls *.txt
ls *.log
```

#### Common Combinations
```bash
# Most commonly used combination
ls -lah

# Show recent files with details
ls -lath

# Show files sorted by size with human-readable format
ls -lahS

# Show hidden files with details and type indicators
ls -laF

# Show directory tree structure (if tree command not available)
ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'
```

#### Useful Aliases
Add these to your `~/.bashrc` or `~/.zshrc`:
```bash
alias ll='ls -lah'
alias la='ls -A'
alias l='ls -CF'
alias lt='ls -lath'
alias lsize='ls -lahS'
```

#### Adding Aliases to .bashrc

**Method 1: Using echo (simple)**
```bash
echo "alias ll='ls -lah'" >> ~/.bashrc
```

**Method 2: Using printf (more reliable)**
```bash
printf "alias ll='ls -lah'\n" >> ~/.bashrc
```

**Method 3: Add multiple aliases at once**
```bash
cat >> ~/.bashrc << 'EOF'
alias ll='ls -lah'
alias la='ls -A'
alias l='ls -CF'
alias lt='ls -lath'
alias lsize='ls -lahS'
EOF
```

**Method 4: Check if alias exists before adding**
```bash
grep -q "alias ll=" ~/.bashrc || echo "alias ll='ls -lah'" >> ~/.bashrc
```

**Apply changes immediately:**
```bash
# Reload .bashrc in current session
source ~/.bashrc

# Or use the shorthand
. ~/.bashrc
```

**Manage aliases:**
```bash
# List all current aliases
alias

# Remove an alias (temporarily)
unalias ll

# Check if .bashrc exists
ls -la ~/.bashrc

# Create .bashrc if it doesn't exist
touch ~/.bashrc

# Edit .bashrc with your preferred editor
nano ~/.bashrc
# or
vim ~/.bashrc
```