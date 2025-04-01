# Linux故障排查指南 🔍

> 最后更新时间：2025年4月1日
> 
> 版本：1.0.1

## 🗺️ 故障排查思维导图
```mermaid
mindmap
  root((故障排查))
    系统问题
      启动问题
        引导错误
        服务启动
        挂载失败
      性能问题
        CPU高负载
        内存不足
        磁盘IO高
      资源问题
        磁盘空间
        文件句柄
        进程数量
    网络问题
      连接问题
        DNS解析
        路由配置
        防火墙规则
      服务问题
        端口占用
        服务异常
        负载均衡
      性能问题
        带宽使用
        延迟高
        丢包率
    应用问题
      配置错误
        语法错误
        权限问题
        依赖缺失
      运行异常
        崩溃
        内存泄漏
        死锁
      日志分析
        错误日志
        访问日志
        系统日志
```

## 📈 故障排查流程图
```mermaid
flowchart TD
    A[发现问题] --> B[收集信息]
    B --> C[分析症状]
    C --> D[定位原因]
    D --> E{是否确认}
    E -- 是 --> F[制定方案]
    E -- 否 --> G[继续分析]
    G --> C
    F --> H[实施修复]
    H --> I{是否解决}
    I -- 是 --> J[记录总结]
    I -- 否 --> K[方案调整]
    K --> F
    J --> L[完成修复]
```

## 📋 目录
- [故障排查准备](#故障排查准备)
- [系统问题排查](#系统问题排查)
- [网络问题排查](#网络问题排查)
- [服务问题排查](#服务问题排查)
- [性能问题排查](#性能问题排查)
- [安全问题排查](#安全问题排查)

## ✅ 故障排查准备

### 工具准备清单
- [ ] 系统监控工具（top, htop, vmstat）
- [ ] 网络诊断工具（ping, traceroute, netstat）
- [ ] 日志分析工具（tail, grep, awk）

### 信息收集清单
- [ ] 系统基本信息（版本、更新状态）
- [ ] 错误信息和日志
- [ ] 问题发生时间和频率

### 环境检查清单
- [ ] 系统资源使用情况
- [ ] 网络连接状态
- [ ] 服务运行状态

## 系统问题排查

### 1. 启动问题
#### 常见症状
- 系统无法启动
- 启动过程卡住
- 启动报错

#### 排查步骤
```bash
# 1. 查看启动日志
journalctl -b

# 2. 检查系统日志
cat /var/log/syslog

# 3. 检查磁盘空间
df -h
du -sh /*

# 4. 检查文件系统
fsck /dev/sda1
```

#### 解决方案
1. 磁盘空间不足：
```bash
# 清理日志
sudo find /var/log -type f -delete

# 清理软件包缓存
sudo apt clean
sudo apt autoremove
```

2. 文件系统损坏：
```bash
# 修复文件系统
sudo fsck -y /dev/sda1

# 如果无法修复，考虑使用备份
```

### 2. 性能问题
#### 常见症状
- 系统响应慢
- CPU使用率高
- 内存占用大
- 磁盘IO高

#### 排查步骤
```bash
# 1. 查看系统负载
uptime
top

# 2. 检查CPU使用
mpstat 1
pidstat -u 1

# 3. 检查内存使用
free -h
vmstat 1

# 4. 检查IO情况
iostat -xz 1
iotop
```

#### 解决方案
1. CPU占用高：
```bash
# 找到占用CPU的进程
top -o %CPU

# 调整进程优先级
renice +10 -p PID
```

2. 内存不足：
```bash
# 清理缓存
sync; echo 3 > /proc/sys/vm/drop_caches

# 调整交换区使用
sudo sysctl vm.swappiness=10
```

## 网络问题排查

### 1. 连接问题
#### 常见症状
- 无法访问网络
- 连接超时
- DNS解析失败

#### 排查步骤
```bash
# 1. 检查网络接口
ip addr
ifconfig

# 2. 测试连接
ping 8.8.8.8
ping www.baidu.com

# 3. 检查DNS
cat /etc/resolv.conf
nslookup www.baidu.com

# 4. 检查路由
route -n
traceroute www.baidu.com
```

#### 解决方案
1. DNS问题：
```bash
# 修改DNS服务器
echo "nameserver 8.8.8.8" > /etc/resolv.conf

# 或者编辑网络配置
sudo vim /etc/netplan/01-netcfg.yaml
```

2. 网络接口问题：
```bash
# 重启网络接口
sudo ifdown eth0 && sudo ifup eth0

# 或者重启网络服务
sudo systemctl restart networking
```

## 服务问题排查

### 1. 服务启动失败
#### 常见症状
- 服务无法启动
- 服务异常退出
- 服务响应慢

#### 排查步骤
```bash
# 1. 查看服务状态
systemctl status 服务名

# 2. 查看服务日志
journalctl -u 服务名

# 3. 检查配置文件
nginx -t  # 以nginx为例
```

#### 解决方案
1. 配置文件错误：
```bash
# 备份配置文件
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak

# 修复配置
vim /etc/nginx/nginx.conf
```

2. 权限问题：
```bash
# 修复权限
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html
```

## 性能问题排查

### 1. 系统性能分析
#### 工具使用
```bash
# 1. top命令详解
top
# CPU列说明：
# us: 用户空间占用
# sy: 系统空间占用
# ni: 改变过优先级的进程占用
# id: 空闲CPU
# wa: IO等待占用

# 2. vmstat命令
vmstat 1
# r: 运行队列
# b: 阻塞的进程
# swpd: 虚拟内存使用
# free: 空闲物理内存
# buff: 缓冲
# cache: 缓存

# 3. iostat命令
iostat -xz 1
# %util: 设备利用率
# await: 平均等待时间
```

### 2. 性能优化方案
#### 系统层面
```bash
# 1. 调整系统参数
# 最大文件描述符
echo "fs.file-max = 65535" >> /etc/sysctl.conf

# TCP连接参数
echo "net.ipv4.tcp_max_syn_backlog = 8192" >> /etc/sysctl.conf

# 2. 优化内核参数
sysctl -p
```

#### 应用层面
```bash
# 1. 数据库优化
# MySQL配置优化
innodb_buffer_pool_size = 4G
innodb_log_file_size = 1G

# 2. Web服务器优化
# Nginx配置优化
worker_processes auto;
worker_connections 65535;
```

## 安全问题排查

### 1. 安全审计
#### 检查项目
```bash
# 1. 查看登录记录
last
lastb

# 2. 检查可疑进程
ps aux | grep -i "suspicious"

# 3. 检查网络连接
netstat -tulpn
lsof -i
```

#### 安全加固
```bash
# 1. 更新系统
sudo apt update
sudo apt upgrade

# 2. 配置防火墙
sudo ufw enable
sudo ufw default deny incoming
sudo ufw allow ssh

# 3. 加固SSH
sudo vim /etc/ssh/sshd_config
# 设置：
PermitRootLogin no
PasswordAuthentication no
```

## 常见问题速查表

### 1. 系统类
| 问题 | 排查命令 | 可能原因 |
|------|----------|----------|
| CPU高 | top, htop | 进程异常、负载高 |
| 内存不足 | free -h | 内存泄漏、应用占用 |
| 磁盘满 | df -h | 日志、临时文件 |

### 2. 网络类
| 问题 | 排查命令 | 可能原因 |
|------|----------|----------|
| 无法连接 | ping, telnet | 网络中断、防火墙 |
| DNS问题 | nslookup | DNS服务器、解析错误 |
| 端口占用 | netstat | 服务冲突、进程异常 |

### 3. 服务类
| 问题 | 排查命令 | 可能原因 |
|------|----------|----------|
| 启动失败 | systemctl status | 配置错误、依赖问题 |
| 响应慢 | curl -w "%{time_total}\n" | 负载高、网络延迟 |
| 异常退出 | journalctl | 内存不足、Bug |

## 故障排查流程

1. 信息收集
   - 查看系统日志
   - 收集错误信息
   - 确认问题范围

2. 初步分析
   - 确定问题类型
   - 复现问题
   - 查找相关日志

3. 深入排查
   - 使用专业工具
   - 分析根本原因
   - 测试可能解决方案

4. 解决问题
   - 实施解决方案
   - 验证问题解决
   - 记录解决过程

5. 后续跟进
   - 监控系统状态
   - 预防类似问题
   - 更新文档 