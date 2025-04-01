# Linux实战项目指南

> 最后更新时间：2024年3月
> 
> 版本：1.0.0

## 目录
- [项目一：文件管理系统](#项目一文件管理系统)
- [项目二：系统监控工具](#项目二系统监控工具)
- [项目三：Web服务器部署](#项目三web服务器部署)
- [项目四：数据库服务器](#项目四数据库服务器)
- [项目五：容器化应用](#项目五容器化应用)

## 项目一：文件管理系统

### 项目描述
开发一个文件管理系统，实现文件的自动分类、备份和清理功能。

### 技术要点
- Shell脚本编程
- 文件系统操作
- 定时任务
- 日志管理

### 实现步骤

1. 创建项目结构：
```bash
mkdir -p ~/projects/file-manager/{bin,conf,logs}
cd ~/projects/file-manager
```

2. 创建配置文件：
```bash
cat > conf/config.sh << 'EOF'
# 配置文件
WATCH_DIR="/path/to/watch"
BACKUP_DIR="/path/to/backup"
LOG_DIR="logs"
FILE_TYPES=("*.txt" "*.pdf" "*.doc")
BACKUP_DAYS=7
EOF
```

3. 创建主脚本：
```bash
cat > bin/file_manager.sh << 'EOF'
#!/bin/bash

# 导入配置
source ../conf/config.sh

# 日志函数
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" >> "$LOG_DIR/file_manager.log"
}

# 文件分类
classify_files() {
    for type in "${FILE_TYPES[@]}"; do
        find "$WATCH_DIR" -name "$type" -type f -mtime -1 | while read file; do
            dir_name=$(dirname "$file")
            mkdir -p "$dir_name/${type%.*}"
            mv "$file" "$dir_name/${type%.*}/"
            log "Moved $file to ${type%.*} directory"
        done
    done
}

# 文件备份
backup_files() {
    backup_name="backup_$(date +%Y%m%d)"
    tar -czf "$BACKUP_DIR/$backup_name.tar.gz" "$WATCH_DIR"
    log "Created backup: $backup_name.tar.gz"
}

# 清理旧备份
cleanup_backups() {
    find "$BACKUP_DIR" -name "backup_*.tar.gz" -type f -mtime +$BACKUP_DAYS -delete
    log "Cleaned up old backups"
}

# 主函数
main() {
    log "Starting file management process"
    classify_files
    backup_files
    cleanup_backups
    log "Completed file management process"
}

main
EOF

chmod +x bin/file_manager.sh
```

4. 设置定时任务：
```bash
crontab -e
# 添加以下行
0 0 * * * /home/user/projects/file-manager/bin/file_manager.sh
```

## 项目二：系统监控工具

### 项目描述
开发一个系统监控工具，监控系统资源使用情况并发送警报。

### 技术要点
- 系统资源监控
- 邮件通知
- 数据可视化
- Web界面

### 实现步骤

1. 创建项目结构：
```bash
mkdir -p ~/projects/system-monitor/{bin,conf,data,web}
cd ~/projects/system-monitor
```

2. 创建监控脚本：
```bash
cat > bin/monitor.py << 'EOF'
#!/usr/bin/env python3
import psutil
import time
import json
import smtplib
from email.mime.text import MIMEText
from datetime import datetime

def get_system_stats():
    return {
        'cpu_percent': psutil.cpu_percent(interval=1),
        'memory_percent': psutil.virtual_memory().percent,
        'disk_percent': psutil.disk_usage('/').percent,
        'timestamp': datetime.now().isoformat()
    }

def save_stats(stats):
    with open('data/stats.json', 'a') as f:
        json.dump(stats, f)
        f.write('\n')

def check_thresholds(stats):
    thresholds = {
        'cpu_percent': 80,
        'memory_percent': 80,
        'disk_percent': 90
    }
    
    alerts = []
    for key, value in stats.items():
        if key in thresholds and value > thresholds[key]:
            alerts.append(f'{key} is too high: {value}%')
    return alerts

def send_alert(alerts):
    msg = MIMEText('\n'.join(alerts))
    msg['Subject'] = 'System Alert'
    msg['From'] = 'monitor@example.com'
    msg['To'] = 'admin@example.com'
    
    with smtplib.SMTP('localhost') as s:
        s.send_message(msg)

def main():
    while True:
        stats = get_system_stats()
        save_stats(stats)
        
        alerts = check_thresholds(stats)
        if alerts:
            send_alert(alerts)
        
        time.sleep(60)

if __name__ == '__main__':
    main()
EOF

chmod +x bin/monitor.py
```

3. 创建Web界面：
```bash
cat > web/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>System Monitor</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .chart-container {
            width: 800px;
            margin: 20px auto;
        }
    </style>
</head>
<body>
    <div class="chart-container">
        <canvas id="statsChart"></canvas>
    </div>
    <script>
        async function updateChart() {
            const response = await fetch('/data/stats.json');
            const data = await response.json();
            
            const ctx = document.getElementById('statsChart').getContext('2d');
            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: data.map(d => d.timestamp),
                    datasets: [{
                        label: 'CPU Usage',
                        data: data.map(d => d.cpu_percent)
                    }, {
                        label: 'Memory Usage',
                        data: data.map(d => d.memory_percent)
                    }, {
                        label: 'Disk Usage',
                        data: data.map(d => d.disk_percent)
                    }]
                }
            });
        }
        
        setInterval(updateChart, 60000);
        updateChart();
    </script>
</body>
</html>
EOF
```

4. 配置Web服务器：
```bash
# Nginx配置
cat > /etc/nginx/conf.d/monitor.conf << 'EOF'
server {
    listen 80;
    server_name monitor.local;
    
    root /home/user/projects/system-monitor/web;
    index index.html;
    
    location /data/ {
        alias /home/user/projects/system-monitor/data/;
    }
}
EOF

# 重启Nginx
sudo systemctl restart nginx
```

## 项目三：Web服务器部署

### 项目描述
部署一个完整的Web应用，包括前端、后端和数据库。

### 技术要点
- Nginx配置
- Python Flask应用
- MySQL数据库
- SSL证书

### 实现步骤

1. 安装必要软件：
```bash
sudo apt update
sudo apt install nginx mysql-server python3-pip
pip3 install flask flask-sqlalchemy gunicorn
```

2. 创建Flask应用：
```bash
mkdir -p ~/projects/webapp/{app,static,templates}
cd ~/projects/webapp

# 创建应用文件
cat > app/__init__.py << 'EOF'
from flask import Flask, render_template
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://user:password@localhost/webapp'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

@app.route('/')
def index():
    users = User.query.all()
    return render_template('index.html', users=users)
EOF

# 创建模板
cat > templates/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>Web App</title>
</head>
<body>
    <h1>Users</h1>
    <ul>
    {% for user in users %}
        <li>{{ user.username }} ({{ user.email }})</li>
    {% endfor %}
    </ul>
</body>
</html>
EOF
```

3. 配置数据库：
```bash
sudo mysql -u root << 'EOF'
CREATE DATABASE webapp;
CREATE USER 'webuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON webapp.* TO 'webuser'@'localhost';
FLUSH PRIVILEGES;
EOF
```

4. 配置Nginx：
```bash
# 创建Nginx配置
sudo tee /etc/nginx/conf.d/webapp.conf << 'EOF'
server {
    listen 80;
    server_name webapp.local;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
EOF

# 重启Nginx
sudo systemctl restart nginx
```

5. 启动应用：
```bash
cd ~/projects/webapp
gunicorn -w 4 'app:app'
```

## 项目四：数据库服务器

### 项目描述
搭建一个高可用的MySQL数据库服务器。

### 技术要点
- MySQL主从复制
- 数据备份
- 性能优化
- 监控告警

### 实现步骤

1. 安装MySQL：
```bash
sudo apt update
sudo apt install mysql-server
```

2. 配置主服务器：
```bash
sudo tee /etc/mysql/mysql.conf.d/master.cnf << 'EOF'
[mysqld]
server-id = 1
log_bin = /var/log/mysql/mysql-bin.log
binlog_do_db = myapp
EOF

sudo systemctl restart mysql
```

3. 配置从服务器：
```bash
sudo tee /etc/mysql/mysql.conf.d/slave.cnf << 'EOF'
[mysqld]
server-id = 2
relay-log = /var/log/mysql/mysql-relay-bin.log
EOF

sudo systemctl restart mysql
```

4. 设置主从复制：
```bash
# 在主服务器上
mysql -u root -p << 'EOF'
CREATE USER 'repl'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
EOF

# 在从服务器上
mysql -u root -p << 'EOF'
CHANGE MASTER TO
    MASTER_HOST='master_ip',
    MASTER_USER='repl',
    MASTER_PASSWORD='password',
    MASTER_LOG_FILE='mysql-bin.000001',
    MASTER_LOG_POS=123;
START SLAVE;
EOF
```

## 项目五：容器化应用

### 项目描述
使用Docker容器化部署一个完整的应用栈。

### 技术要点
- Docker基础
- Docker Compose
- 容器编排
- 持续集成

### 实现步骤

1. 创建项目结构：
```bash
mkdir -p ~/projects/docker-app/{app,nginx,mysql}
cd ~/projects/docker-app
```

2. 创建Docker Compose配置：
```bash
cat > docker-compose.yml << 'EOF'
version: '3'
services:
  web:
    build: ./app
    ports:
      - "5000:5000"
    environment:
      - MYSQL_HOST=db
      - MYSQL_USER=root
      - MYSQL_PASSWORD=password
    depends_on:
      - db
  
  nginx:
    build: ./nginx
    ports:
      - "80:80"
    depends_on:
      - web
  
  db:
    image: mysql:8
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=myapp
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
EOF
```

3. 创建应用Dockerfile：
```bash
cat > app/Dockerfile << 'EOF'
FROM python:3.9
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
EOF
```

4. 创建Nginx配置：
```bash
cat > nginx/nginx.conf << 'EOF'
events {
    worker_connections 1024;
}

http {
    upstream flask_app {
        server web:5000;
    }

    server {
        listen 80;
        
        location / {
            proxy_pass http://flask_app;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
EOF
```

5. 构建和运行：
```bash
docker-compose up -d
```

## 项目进阶

### 持续集成/持续部署
1. 使用GitHub Actions自动化构建和测试
2. 实现自动部署到生产环境
3. 添加监控和告警系统

### 高可用架构
1. 实现服务器集群
2. 配置负载均衡
3. 实现自动故障转移

### 性能优化
1. 实现缓存层
2. 优化数据库查询
3. 实现CDN加速

### 安全加固
1. 实现WAF防护
2. 配置SSL证书
3. 实现安全审计 