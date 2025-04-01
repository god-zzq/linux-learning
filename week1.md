# Linux学习指南 - 第一周：基础知识和环境搭建

> 最后更新时间：2024年3月
> 
> 版本：1.0.0

## 目录
- [环境配置完成情况](#环境配置完成情况)
- [Day 1-2: 认识Linux和环境搭建](#day-1-2-认识linux和环境搭建)
- [Day 3-4: 基本命令学习](#day-3-4-基本命令学习超详细版)
- [Day 5-7: 文本编辑和开发工具](#day-5-7-文本编辑和开发工具)

## 环境配置完成情况（✓表示已完成）
- [x] WSL安装：在Windows商店搜索"Ubuntu"并安装完成
- [x] 中文环境配置：系统显示中文（LANG=zh_CN.UTF-8）
- [x] 基础工具：git（代码管理）, python3（编程语言）, vim（文本编辑器）
- [x] 图形界面：可以看到类似Windows的界面（XFCE4和VcXsrv）

## Day 1-2: 认识Linux和环境搭建
### 1. WSL是什么？
- Windows Subsystem for Linux（Windows的Linux子系统）
- 简单理解：在Windows里运行的一个小型Linux系统
- 优点：不用重启电脑就能用Linux

### 2. 基础环境配置（已完成的步骤）
#### WSL安装和设置：
```bash
# 在Windows商店搜索"Ubuntu"并安装
# 第一次打开时设置：
用户名：zaki2005（已设置）
密码：（已设置，请记住你的密码）

# 更新系统（已执行）
sudo apt update        # 更新软件源信息
sudo apt upgrade -y    # 更新所有软件包
```

#### 中文环境设置：
```bash
# 安装中文支持（已完成）
sudo apt install language-pack-zh-hans
# 设置中文环境变量（已完成）
echo "export LANG=zh_CN.UTF-8" >> ~/.bashrc
```

#### 基础工具安装：
```bash
# 安装开发工具（已完成）
sudo apt install git python3 vim

# Git配置（已完成）
git config --global user.name "zaki2005"
git config --global user.email "1625947291@qq.com"
```

#### 图形界面设置：
```bash
# 安装XFCE4（已完成）
sudo apt install xfce4

# 启动图形界面方法：
1. 打开VcXsrv（在Windows上）
2. 在Ubuntu终端输入：xfce4-session
```

## Day 3-4: 基本命令学习（超详细版）
### 1. 理解文件系统
想象Linux系统是一个大箱子：
- `/home`：你的卧室，存放个人文件
  - 实际位置：`/home/zaki2005`（你的用户名）
- `/etc`：说明书柜，存放各种设置
- `/usr`：工具间，存放安装的软件
- `/var`：临时工作台，存放临时文件

### 2. 最常用命令（图解版）
```bash
# 查看文件和目录
ls                  # 查看当前目录下有什么
ls -l               # 查看详细信息（l表示long）
ls -a               # 查看隐藏文件（a表示all）
ls -la              # 组合使用，查看所有文件的详细信息

# 目录操作
pwd                 # 显示当前位置（Print Working Directory）
cd ~                # 回到家目录（你的卧室）
cd ..               # 返回上一级目录（往回走一步）
cd /                # 回到根目录（箱子的最顶层）

# 文件操作
mkdir 文件夹名       # 创建文件夹
mkdir -p a/b/c      # 创建多层文件夹（如果a和b不存在，自动创建）
touch 文件名        # 创建空文件
cp 源文件 目标文件   # 复制文件
cp -r 源目录 目标目录 # 复制整个目录
mv 源文件 目标位置   # 移动文件（也用来重命名）
rm 文件名           # 删除文件
rm -r 目录名        # 删除目录
rm -rf 目录名       # 强制删除目录（小心使用！）
```

### 3. 文件权限（超简单理解）
想象文件是你的日记本：
- r (read)：可以看内容
- w (write)：可以写内容
- x (execute)：可以执行这个文件

```bash
# 修改权限
chmod 755 文件名    # 常用权限设置（自己全部权限，其他人只读和执行）
chmod +x 文件名     # 添加执行权限（让文件可以运行）

# 权限数字含义：
# 7 = 4(读) + 2(写) + 1(执行)
# 5 = 4(读) + 0(不可写) + 1(执行)
```

## Day 5-7: 文本编辑和开发工具
### 1. Vim编辑器（新手指南）
#### 基本概念：
Vim就像一个带有多个模式的高级记事本
- 打开文件：`vim 文件名`
- 三个主要模式：
  1. 普通模式（打开文件时的默认模式）
  2. 插入模式（用来输入文字）
  3. 命令模式（用来保存退出）

#### 基础操作流程：
1. 打开文件：`vim test.txt`
2. 输入内容：
   - 按`i`键进入插入模式
   - 输入你要写的内容
   - 按`Esc`键返回普通模式
3. 保存文件：
   - 按`:`进入命令模式
   - 输入`wq`（write and quit）
   - 按回车确认

#### 常用命令：
```bash
:w      # 保存
:q      # 退出
:wq     # 保存并退出
:q!     # 不保存强制退出
/文本    # 搜索文本（按n查找下一个）
```

### 2. Git版本控制（新手入门）
#### 什么是Git？
- 想象Git是一个超级相册，可以保存文件的各个版本
- 每次保存都会记录是谁保存的、什么时候保存的

#### 基础操作：
```bash
# 1. 创建新的Git仓库
git init                    # 在当前目录创建Git仓库

# 2. 添加文件到暂存区
git add 文件名              # 添加单个文件
git add .                  # 添加所有文件

# 3. 保存修改
git commit -m "你的说明"    # 保存修改，并说明做了什么

# 4. 查看状态
git status                 # 查看当前文件状态
```

### 3. Python环境（详细设置）
#### 虚拟环境说明：
- 虚拟环境就像一个独立的小房间
- 每个房间可以安装不同版本的Python包
- 不同项目互不影响

#### 使用方法：
```bash
# 1. 创建虚拟环境
python3 -m venv myenv      # 创建名为myenv的虚拟环境

# 2. 激活虚拟环境
source myenv/bin/activate  # 进入虚拟环境
# 命令提示符前会出现(myenv)

# 3. 安装Python包（已完成）
pip install numpy pandas ipython

# 4. 退出虚拟环境
deactivate                 # 退出虚拟环境
```

## 配置备忘（已完成的设置）
- WSL：Ubuntu已安装并配置
- 中文：支持中文显示
- 开发工具：
  - Git: 2.43.0（已配置用户信息）
  - Python: 3.12.3（已安装基础包）
  - Vim: 已安装并可用
- 图形界面：XFCE4已配置
- Python虚拟环境：~/python_projects/myenv 