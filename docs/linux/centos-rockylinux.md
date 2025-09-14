# Q: docker run centos，现在推荐用什么版本，latest不能用
A: rockylinux:9

我了解你在运行 docker run centos 时发现 latest 标签无法使用，并想知道现在该用什么版本。由于 CentOS 传统的发行模式已经改变，官方推荐的 Docker CentOS 镜像版本和标签选择也发生了变化。下面我将为你说明原因和推荐选择。

🐧 传统 CentOS Linux 镜像的现状与替代方案

由于 CentOS Linux 传统的 latest 标签可能指向了已停止维护的版本（例如 CentOS 8 已在 2021 年底停止维护，CentOS 7 也于 2024 年 6 月结束生命周期），继续使用它们可能会存在安全风险和兼容性问题。

目前，在 Docker 中运行一个与 CentOS 类似、且稳定可靠的 Linux 发行版，主要有以下几种选择：

推荐镜像/项目名称 官方镜像标签示例 (Docker Hub) 特点简述 适合场景
Rocky Linux rockylinux:9 rockylinux:8 RHEL 1:1 二进制兼容，旨在继承传统 CentOS 生产环境首选，追求稳定性和长期支持
AlmaLinux almalinux:9 almalinux:8 RHEL 1:1 二进制兼容，社区驱动的继任者 生产环境首选，追求稳定性和长期支持
CentOS Stream centos:stream9 centos:stream8 RHEL 的上游开发分支，滚动发布 开发、测试环境，希望体验 RHEL 未来功能

💡 如何选择：

· 如果追求稳定性和长期支持，用于生产环境，建议选择 Rocky Linux 9 或 AlmaLinux 9。它们是 CentOS 的最佳替代品，并提供长期支持（例如 Rocky Linux 9 和 AlmaLinux 9 的生命周期计划到 2032 年）。
· 如果只是在开发或测试环境中需要一个类似 CentOS 的环境，并且希望体验最新的软件包，可以考虑 CentOS Stream 9。

🚀 如何运行推荐的镜像

选择好替代品后，你可以使用以下命令来运行容器（以 Rocky Linux 和 AlmaLinux 为例）：

```bash
# 运行最新版本的 Rocky Linux
docker run -it rockylinux:9

# 运行指定版本的 AlmaLinux
docker run -it almalinux:9

# 如果你想运行 CentOS Stream (例如 Stream 9)
docker run -it centos:stream9
```

⚠️ 运行 Docker 的系统要求

无论你选择哪个镜像，都需要确保你的宿主机系统满足运行 Docker 的要求：

· CentOS 7：要求系统为 64 位，内核版本为 3.10 以上。
· CentOS 6.5 或更高版本：要求系统为 64 位，内核版本为 2.6.32-431 或者更高版本。

你可以使用 uname -r 命令查看当前内核版本。

💎 总结

总而言之，不建议再使用 `centos:latest` 标签，因为它可能指向已停止维护的版本。

· 对于生产环境或追求稳定性，请转向 Rocky Linux 或 AlmaLinux 的 9 版本系列。
· 对于开发或测试，可以考虑 CentOS Stream。

希望这能帮助你选择合适的 Docker 镜像。如果你对特定发行版有更详细的需求，或者想了解如何迁移现有的 CentOS 环境，可以告诉我更多信息。

# rocklinux为什么不支持top命令
rockylinux（或任何Linux发行版）绝对是支持 `top` 命令的。如果你在 Rocky Linux 容器中无法使用 top 命令，那是因为你运行的基础镜像过于精简，没有包含这个工具。

这不是 Rocky Linux 的问题，而是所有 minimalist Docker 基础镜像（包括 centos, ubuntu, debian, almalinux 等）的共同设计理念。

为什么基础镜像没有 top 命令？

Docker 官方镜像为了追求最小化，默认只包含最核心的组件：

· ✅ 减小镜像体积（下载更快，占用磁盘更少）
· ✅ 减少安全漏洞的攻击面
· ✅ 让用户按需安装，保持环境干净

解决方案：安装 procps 包

top 命令包含在 procps 或 procps-ng 软件包中。你需要手动安装它：

方法1：在运行的容器中安装

```bash
# 1. 启动Rocky Linux容器
docker run -it rockylinux:9 /bin/bash

# 2. 在容器内更新并安装procps
dnf update -y
dnf install -y procps-ng

# 3. 现在就可以使用top命令了
top
```

方法2：创建包含top的Dockerfile

```dockerfile
FROM rockylinux:9

RUN dnf update -y && \
    dnf install -y procps-ng && \
    dnf clean all

CMD ["/bin/bash"]
```

然后构建和运行：

```bash
docker build -t rocky-with-tools .
docker run -it rocky-with-tools
```

方法3：一键运行带top的容器

```bash
# 单条命令完成安装并进入bash
docker run -it rockylinux:9 /bin/bash -c "dnf install -y procps-ng && exec /bin/bash"
```

验证安装

安装完成后，你可以测试：

```bash
# 检查top版本
top -v

# 运行top命令
top

# 查看进程信息（top的替代品，如果暂时不想安装）
ps aux
```

其他常用的诊断工具

你可能还需要安装其他工具：

```bash
# 安装常用网络工具
dnf install -y iproute net-tools curl wget

# 安装系统诊断工具集
dnf install -y procps-ng util-linux sysstat

# 安装完整的开发工具（较大，不推荐用于生产）
dnf groupinstall -y "Development Tools"
```

记住这个模式

对于任何 minimalist Docker 镜像：

· centos → 需要 yum install procps-ng
· rockylinux → 需要 dnf install procps-ng
· almalinux → 需要 dnf install procps-ng
· ubuntu → 需要 apt-get install procps
· debian → 需要 apt-get install procps

这不是bug，而是feature——Docker镜像的设计哲学就是"按需安装"，保持最小化基础环境。

现在试试安装 procps-ng，你的 top 命令就可以正常使用了！