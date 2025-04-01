# Linux学习指南 - 第二周：进阶操作和实践

> 最后更新时间：2024年3月
> 
> 版本：1.0.0

## 目录
- [Day 8-10: 系统管理和进程](#day-8-10-系统管理和进程)
- [Day 11-12: 网络和服务配置](#day-11-12-网络和服务配置)
- [Day 13-14: Shell脚本和自动化](#day-13-14-shell脚本和自动化)
- [进阶学习主题](#进阶学习主题)

## Day 8-10: 系统管理和进程
### 1. 用户和权限管理（深入理解）
#### 用户管理：
```bash
# 用户操作
sudo useradd -m 用户名    # 创建新用户（-m表示创建家目录）
sudo passwd 用户名       # 设置用户密码
sudo userdel -r 用户名   # 删除用户（-r表示连同家目录一起删除）

# 用户组操作
groups                  # 查看当前用户所在的组
sudo usermod -aG 组名 用户名  # 将用户添加到组
```

#### 权限管理进阶：
```bash
# 文件权限示例
ls -l
# 结果解释：
# -rw-r--r--  1 zaki2005 zaki2005  234 Mar 15 10:30 文件名
# |[-][-][--]  | 所有者   组名      大小  修改时间    文件名
# |[用户权限][组权限][其他人权限]
#  r:读 w:写 x:执行

# 修改所有者
sudo chown 用户名:组名 文件名
```

### 2. 进程管理（任务管理器）
#### 查看进程：
```bash
# 实时查看系统状态
top                     # 实时系统监控（按q退出）
htop                    # 更好用的top（如果没有就先安装：sudo apt install htop）

# 查看特定进程
ps aux | grep 程序名    # 查找特定程序的进程
pgrep 程序名           # 只显示进程ID

# 结束进程方法：
kill 进程ID            # 正常结束进程
kill -9 进程ID         # 强制结束进程（当进程无响应时使用）
pkill 程序名           # 结束指定名称的所有进程
```
 
#### 后台任务管理：
```bash
命令 &                 # 在后台运行命令
ctrl + z              # 暂停当前任务
bg                    # 让暂停的任务在后台继续运行
fg                    # 把后台任务调到前台
jobs                  # 查看后台任务列表
```

### 3. 系统监控和维护
#### 系统资源查看：
```bash
# 磁盘空间
df -h                 # 查看磁盘使用情况
du -sh 目录名         # 查看目录大小

# 内存使用
free -h               # 查看内存使用情况
vmstat                # 查看虚拟内存统计

# 系统信息
uname -a              # 查看系统信息
lsb_release -a        # 查看发行版信息
```

## Day 11-12: 网络和服务配置
### 1. 网络配置基础
#### 网络信息查看：
```bash
ip addr               # 查看网络接口和IP地址
ping 网址             # 测试网络连接
traceroute 网址       # 查看网络路由（需要安装：sudo apt install traceroute）
netstat -tuln         # 查看网络端口使用情况
```

#### 网络工具使用：
```bash
# 下载工具
wget 网址             # 下载文件
curl 网址             # 访问网页或API

# 远程连接
ssh 用户名@服务器IP    # SSH连接远程服务器
scp 本地文件 用户名@服务器IP:目标路径  # 复制文件到远程服务器
```

### 2. 防火墙配置
```bash
# UFW防火墙（Ubuntu默认防火墙）
sudo ufw status      # 查看防火墙状态
sudo ufw enable      # 启用防火墙
sudo ufw disable     # 禁用防火墙
sudo ufw allow 端口号 # 允许访问某个端口
sudo ufw deny 端口号  # 拒绝访问某个端口
```

## Day 13-14: Shell脚本和自动化
### 1. Shell脚本基础
#### 创建第一个脚本：
```bash
# 创建脚本文件
vim hello.sh

# 脚本内容示例
#!/bin/bash
echo "Hello, World!"
echo "当前时间是：$(date)"
echo "当前用户是：$USER"

# 设置执行权限
chmod +x hello.sh

# 运行脚本
./hello.sh
```

#### 基础语法：
```bash
# 变量
name="小明"
echo "你好，$name"

# 条件判断
if [ -f 文件名 ]; then
    echo "文件存在"
else
    echo "文件不存在"
fi

# 循环
for i in {1..5}; do
    echo "第$i次循环"
done
```

### 2. 实用脚本示例
#### 系统备份脚本：
```bash
#!/bin/bash
# 备份重要文件
backup_dir="/home/zaki2005/backup"
date_stamp=$(date +%Y%m%d)

# 创建备份目录
mkdir -p "$backup_dir/$date_stamp"

# 复制重要文件
cp ~/.bashrc "$backup_dir/$date_stamp/"
cp ~/.vimrc "$backup_dir/$date_stamp/"

echo "备份完成！"
```

#### 系统信息收集脚本：
```bash
#!/bin/bash
# 收集系统信息
echo "=== 系统信息 ==="
uname -a
echo "=== CPU信息 ==="
lscpu
echo "=== 内存信息 ==="
free -h
echo "=== 磁盘信息 ==="
df -h
```

## 进阶学习主题（选学）
### 1. 服务管理（systemd）：
```bash
systemctl status 服务名   # 查看服务状态
systemctl start 服务名    # 启动服务
systemctl stop 服务名     # 停止服务
systemctl restart 服务名  # 重启服务
```

### 2. 软件包管理进阶：
```bash
# APT高级用法
apt search 关键词        # 搜索软件包
apt show 包名           # 显示包信息
apt autoremove         # 清理不需要的依赖

# 软件源管理
sudo vim /etc/apt/sources.list  # 编辑软件源
```

### 3. 日志管理：
```bash
# 系统日志
tail -f /var/log/syslog  # 实时查看系统日志
journalctl              # 查看systemd日志
```

### 4. 定时任务：
```bash
crontab -e             # 编辑定时任务
# 格式：分 时 日 月 星期 命令
# 例如：每天凌晨2点备份
0 2 * * * /home/zaki2005/backup.sh
```

## 实用技巧补充
### 1. 命令行效率技巧：
- 使用Tab键自动补全命令和文件名
- 使用`history`查看命令历史
- `!!`重复执行上一条命令
- `ctrl + r`搜索历史命令

### 2. 文本处理工具：
```bash
grep 关键词 文件名     # 搜索文件内容
sed 's/旧/新/g' 文件名 # 替换文件内容
awk '{print $1}' 文件名 # 处理文本数据
```

### 3. 压缩和解压：
```bash
# 压缩
tar -czvf 压缩包.tar.gz 目录名/

# 解压
tar -xzvf 压缩包.tar.gz
```

### 4. 系统快照：
```bash
# 使用timeshift（需要先安装）
sudo apt install timeshift
sudo timeshift --create    # 创建快照
sudo timeshift --restore   # 恢复快照
```

## 常见问题解决方案
### 1. 磁盘空间不足：
```bash
# 查找大文件
sudo find / -type f -size +100M

# 清理缓存
sudo apt clean
sudo apt autoremove
```

### 2. 系统运行慢：
```bash
# 查看资源占用最多的进程
top -o %CPU     # 按CPU使用率排序
top -o %MEM     # 按内存使用率排序
```

### 3. 网络问题：
```bash
# 检查网络连接
ping 8.8.8.8    # 测试外网连接
nslookup 网址    # 检查DNS解析
``` 