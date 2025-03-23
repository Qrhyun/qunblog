---
title: linux后台服务和配置文件的辨析
date: 2025-03-23 22:20:49
tags:
---

# linux后台服务和配置文件的辨析

---

## 1. 后台服务

### 1.1 有与用户会话相关的工具

#### 1.1.1 nohup
**nohup**，可以使运行的命令忽略 `SIGHUP` 信号。因此，即使退出登录，程序仍旧会继续执行。通常情况下，在 `nohup` 命令尾部加上 `&` 字符，才能将命令放至后台执行。
在命令前加上 `nohup`，末尾加上 `&`，可以看到 `nohup` 输出了一行信息，再按 `Enter` 键就跳回了 Shell 命令行，此时命令已经在后台执行了，`nohup` 将命令的输出重定向至当前目录的 `nohup.out` 文件中。同时，`nohup` 会将对应程序的 PID 输出，PID 可用于需要中断进程时结束进程。

说明: 也可以使用重定向将程序的输出改为自己想要的文件名，如下所示，则程序的输出就会写到 `hello.log` 文件中。
```bash
nohup bash hello.sh >hello.log &
```

#### 1.1.2 screen
**screen** 是一个终端复用器，可以创建多个终端会话，并在关闭终端后继续运行。

1. 创建 screen 窗口：
   ```bash
   screen -S mysession
   ```
2. 在 screen 窗口中运行命令：
   ```bash
   screen -ls
   ```
3. 退出 screen 窗口：
   ```bash
   Ctrl+a+d
   ```
   你会看到类似以下的提示：
   > [detached from 12345.my_session]  
   这表示会话已经被分离，脚本仍在后台运行。
4. 恢复 screen 窗口：
   ```bash
   screen -r mysession
   ```
5. 删除 screen 窗口：
   ```bash
   exit
   ```

#### 1.1.3 tmux
**tmux** 是一个非常强大的终端复用器，它允许你在单个终端窗口中创建多个会话、窗口和窗格，并且可以在关闭终端后继续运行。  
`tmux` 比 `screen` 更加强大，支持多个窗格。

1. 创建一个新的 tmux 会话：
   ```bash
   tmux new -s mysession
   ```
2. 在 tmux 窗口中运行命令：
   ```bash
   tmux ls
   ```
3. 退出 tmux 窗口：
   ```bash
   Ctrl+b+d
   ```
   你会看到类似以下的提示：
   > [detached from 12345:my_session]  
   这表示会话已经被分离，程序仍在后台运行。
4. 恢复 tmux 窗口：
   ```bash
   tmux attach -t mysession
   ```
5. 删除 tmux 窗口：
   ```bash
   exit
   ```

#### 1.1.4 底层原理
**nohup**、**screen** 和 **tmux** 的底层原理都是通过 `fork` 创建子进程来实现，通过 `exec` 来执行命令。它们的主要区别在于如何处理父进程和子进程之间的关系。

- **nohup**：在父进程退出时，将 `SIGHUP` 信号发送给子进程。子进程会忽略 `SIGHUP` 信号，因此即使父进程退出，子进程仍会继续运行。
- **screen**：在父进程退出时，将 `SIGHUP` 信号发送给所有子进程。子进程会忽略 `SIGHUP` 信号，因此即使父进程退出，子进程仍会继续运行。
- **tmux**：在父进程退出时，将 `SIGHUP` 信号发送给所有子进程。子进程会忽略 `SIGHUP` 信号，因此即使父进程退出，子进程仍会继续运行。

---

### 1.2 系统环境中（独立于用户会话）的工具

#### 1.2.1 systemd
**systemd** 是 Linux 系统的初始化系统和服务管理器，它是一个系统级的进程管理器，负责启动和管理系统中的各种服务。

1. 例如：**MySQL 后台服务**  
   注意：MySQL 服务通常是以守护进程（daemon）形式运行的，即使关闭终端连接，MySQL 服务也会继续运行。同时 MySQL 服务通常默认配置为系统服务，因此可以直接使用 `systemctl` 命令来管理 MySQL 服务。

   使用 `systemctl` 命令：
   ```bash
   sudo systemctl start mysql 
   # 启动 MySQL 服务
   sudo systemctl enable mysql
   # 设置 MySQL 服务在系统启动时自动启动
   ``` 
   使用 `service` 命令：
   ```bash
   sudo service mysql start
   # 启动 MySQL 服务
   ```

2. 例如：**Redis 后台服务**  
   注意：Redis 通常不默认以守护进程运行，因此默认 Redis 不会随系统启动而启动。需要在配置文件中设置为守护进程运行。同时 Redis 通常默认没有配置为系统服务，因此需要手动配置为系统服务。

   使用 `systemctl` 命令（需要手动配置为系统服务）：
   ```bash
   sudo systemctl start redis
   # 启动 Redis 服务
   sudo systemctl enable redis
   # 设置 Redis 服务在系统启动时自动启动
   ```
   指定守护进程：
   ```bash
   redis-server --daemonize yes
   # 使用 --daemonize yes 参数启动 Redis 服务
   ```
   如果 Redis 配置文件中已经设置了 `daemonize yes`，直接运行 `redis-server` 即可。

---

## 2. 配置文件

### 2.1 全局配置与用户配置
1. **全局配置**：
   - `/etc/profile` 和 `/etc/profile.d/`：设置全局环境变量和启动脚本，适用于所有用户（普通用户和 root 用户）。
   - `/etc/environment`：设置全局环境变量。
2. **用户配置**（特定普通用户）：
   - `~/.bashrc`：用户个人配置文件，适用于用户启动的非登录 shell（即为执行 `zsh` 或者 `bash` 命令时加载）。
   - `~/.bash_profile` 或 `~/.profile`：用户个人配置文件，适用于用户登录时的登录 shell（即为 `ssh` 登录时加载）。

---

## 3. 后台服务和配置文件的区别 ，用户和系统的区别

### 3.1 tmux 和 screen
**用途**：主要用于终端复用和会话管理。  
**运行环境**：运行在用户环境中，与用户会话相关。  
**特点**：  
- 允许用户在终端中运行多个会话。  
- 可以将会话分离到后台，即使关闭终端，会话仍然继续运行。  
- 用户友好，提供丰富的窗口管理功能。

---

### 3.2 systemctl
**用途**：用于管理系统服务和系统资源。  
**运行环境**：运行在系统环境中，独立于用户会话。  
**特点**：  
- 功能强大，支持并发启动、依赖关系管理、资源管理等。  
- 使用 `.service` 文件配置服务，通过 `systemctl` 命令管理服务。  
- 适用于需要统一管理服务的场景。
