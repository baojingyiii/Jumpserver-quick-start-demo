# 🏰 JumpServer 开源堡垒机学习指南

## 📖 概述
JumpServer 是一款广受欢迎的开源堡垒机系统，提供安全、高效的资产管理和运维审计能力。本文将详细介绍 JumpServer 的核心组件、架构演变和快速部署方法。

------

## 🧩 核心组件架构

### 组件详解表

| 组件           | 英文全称                      | 核心功能                                    | 备注                     |
| -------------- | ---------------------------- | ------------------------------------------- | ------------------------ |
| **Core**       | JumpServer Core              | 核心后端，提供 API 接口、用户认证、权限管理    | 相当于后端服务           |
| **Web**        | JumpServer Web               | Nginx + Lina + Luna 三合一容器              | 新版本集成组件           |
| **Koko**       | Character Protocol Connector | SSH/Telnet 协议连接器                       | SSH 会话代理与录屏       |
| **Lion**       | Graphical Protocol Connector | RDP/VNC 图形协议连接器                      | 仅需连接 Windows 时启用  |
| **Chen**       | JumpServer Web DB            | Web 数据库                                  | 部分版本内置             |
| **PostgreSQL** | -                            | 主数据库                                    | 存储用户、资产、权限数据 |
| **Redis**      | -                            | 缓存与消息队列                              | 会话管理和缓存服务       |

------

### 组件详解表(旧版)
| Component  | Description | 备注 |
|------------|-------------|------|
| **Core**   | JumpServer Core | 相当于后端 |
| **Lina**   | JumpServer Web UI | 相当于前端 |
| **Luna**   | JumpServer Web Terminal | 终端 |
| **KoKo**   | JumpServer Character Protocol Connector（字符协议连接器） | ssh连接器 |
| **Lion**   | JumpServer Graphical Protocol Connector（图形协议连接器） | 专门处理Windows远程桌面(RDP)和VNC连接 |
| **Chen**   | JumpServer Web DB | 数据库 |

------

## 🔄 架构演进对比
> 新版的jumpserver使用webWeb容器集成Nginx + Lina(前端界面) + Luna(Web终端)
### 传统架构 (v2.x 版本):
```
├── jms_nginx (Nginx反向代理)
├── jms_lina  (Vue前端界面)
├── jms_luna  (Web终端)
└── jms_core  (后端API)
```
### 现代架构 (v4.x 版本):
```
├── jms_web   (Nginx + Lina + Luna 三合一)
├── jms_core  (后端API)
└── 其他连接器
```

------

## 🚀 快速部署指南
### 1. 环境准备
```bash
# 系统要求
- CentOS 7+
- Docker 20.10+
- 4GB+ 内存
- 50GB+ 磁盘空间
```
### 2. 离线安装部署
从飞致云社区 **[下载最新的 linux/amd64 离线包](https://community.fit2cloud.com/#/products/jumpserver/downloads)** , 并上传到部署服务器的 /opt 目录

```bash
cd /opt
tar -xf jumpserver-ce-v4.10.15-x86_64.tar.gz
cd jumpserver-ce-v4.10.15-x86_64
```

```bash
# 查看配置文件模板（可选）
cat config-example.txt

# 执行安装
./jmsctl.sh install

# 启动服务
./jmsctl.sh start

# 查看服务状态
./jmsctl.sh status
```
> 安装完成后 JumpServer 配置文件路径为： /opt/jumpserver/config/config.txt

### 3. 关键配置说明(添加了注解)
```bash
# 根据需要修改配置文件模板,(可以跳过修改)
cat config-example.txt
```

```bash
# JumpServer configuration file example.
#
# If you don't understand the purpose, you can skip modifying this configuration file, the system will automatically fill in
# Complete parameter documentation https://docs.jumpserver.org/zh/v3/guide/env/

################################# Image Configuration 镜像配置#################################
#
# The connection to docker.io in China will timeout or the download speed will be slow, enable this option to use Huawei Cloud image acceleration
# Replace the old version DOCKER_IMAGE_PREFIX
#
# DOCKER_IMAGE_MIRROR=1 (启用此选项可使用华为云镜像加速)

# Image pull policy Always, IfNotPresent
# Always means that the latest image will be pulled every time, IfNotPresent means that the image will be pulled only if it does not exist locally
#
# IMAGE_PULL_POLICY=Always (镜像拉取策略)

############################## Installation Configuration 安装配置#############################
#
# JumpServer database persistence directory, by default, recordings, task logs are in this directory
# Please modify according to the actual situation, the database file (.sql) and configuration file backed up during the upgrade will also be saved to this directory
#
VOLUME_DIR=/data/jumpserver (数据库持久化目录)

# Encryption key, please ensure that SECRET_KEY is consistent with the old environment when migrating, do not use special strings
# (*) Warning: Keep this value secret.
# (*) Do not disclose SECRET_KEY to anyone
#
SECRET_KEY= (加密密钥)

# The token used by the component to register with core, please keep BOOTSTRAP_TOKEN consistent with the old environment when migrating,
# Do not use special strings
# (*) Warning: Keep this value secret.
# (*) Do not disclose BOOTSTRAP_TOKEN to anyone
#
BOOTSTRAP_TOKEN=

# Log level INFO, WARN, ERROR
#
LOG_LEVEL=ERROR (日志级别)

# The network segment used by the JumpServer container, please do not conflict with the existing network, modify according to the actual situation
#
DOCKER_SUBNET=192.168.250.0/24 (JumpServer 容器使用的网段)

# ipv6 nat, no need to enable under normal circumstances
# If the host does not support ipv6, enabling this option will prevent the real client ip address from being obtained
#
USE_IPV6=0
DOCKER_SUBNET_IPV6=fc00:1010:1111:200::/64 (IPv6网段)

################################# DB Configuration 数据库配置####################################
# For external databases, you need to enter the correct database information, the system will automatically handle the built-in database
# (*) The password part must not contain single quotes and double quotes
#
DB_ENGINE=postgresql
DB_HOST=postgresql
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=
DB_NAME=jumpserver

# If external MySQL needs to enable TLS/SSL connection, refer to https://docs.jumpserver.org/zh/v3/installation/security_setup/mysql_ssl/
#
# DB_USE_SSL=true

################################# Redis Configuration Redis 配置#################################
# For external Redis, please enter the correct Redis information, the system will automatically handle the built-in Redis
# (*) The password part must not contain single quotes and double quotes
#
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=

# If you are using external Redis Sentinel, please manually fill in the following content
#
# REDIS_SENTINEL_HOSTS=mymaster/192.168.100.1:26379,192.168.100.1:26380,192.168.100.1:26381
# REDIS_SENTINEL_PASSWORD=your_sentinel_password
# REDIS_PASSWORD=your_redis_password
# REDIS_SENTINEL_SOCKET_TIMEOUT=5

# If external Redis needs to enable TLS/SSL connection, refer to https://docs.jumpserver.org/zh/v3/installation/security_setup/redis_ssl/
#
# REDIS_USE_SSL=true

################################# Access Configuration 访问配置################################
# The service port provided to the outside, if it conflicts with the existing service, please modify it yourself
#
HTTP_PORT=80 (提供给外部的服务端口)

################################# HTTPS Configuration HTTPS 配置#################################
# Refer to https://docs.jumpserver.org/zh/v3/installation/proxy/ for configuration
#
# HTTPS_PORT=443
# SERVER_NAME=your_domain_name
# SSL_CERTIFICATE=your_cert
# SSL_CERTIFICATE_KEY=your_cert_key
#

# Nginx file upload and download size limit
#
CLIENT_MAX_BODY_SIZE=4096m (Nginx 文件上传和下载大小限制)

################################# Component Configuration 组件配置#############################
# Component registration use, by default, register to the core container, the cluster environment needs to be modified to the cluster vip address
#
CORE_HOST=http://core:8080 (组件注册使用，默认注册到核心容器)
PERIOD_TASK_ENABLED=true

# Core Session definition,
# SESSION_COOKIE_AGE indicates how many seconds the session expires after idling,
# SESSION_EXPIRE_AT_BROWSER_CLOSE=true means that the session expires as soon as the browser is closed
#
# SESSION_COOKIE_AGE=86400 (会话在空闲后过期的秒数)
SESSION_EXPIRE_AT_BROWSER_CLOSE=false (浏览器关闭后true则会话立即过期)

# Trusted DOMAINS definition,
# Define the trusted access IP, please modify according to the actual situation, if it is a public IP, please change to the corresponding public IP,
# DOMAINS="demo.jumpserver.org:443"
# DOMAINS="172.17.200.191:80"
# DOMAINS="demo.jumpserver.org:443,172.17.200.191:80"
DOMAINS= (定义受信任的访问 IP)

# Configure the components that do not need to be started, by default all components will be started, if you do not need a certain component, you can set {component name}_ENABLED to 0 to turn it off (默认情况下所有组件都会启动，可以将 {组件名称}_ENABLED 设置为 0 来将其关闭。)
# CORE_ENABLED=0
# CELERY_ENABLED=0
# KOKO_ENABLED=0
# LION_ENABLED=0
# CHEN_ENABLED=0
# WEB_ENABLED=0

# Lion enables font smoothing to optimize the experience
#
JUMPSERVER_ENABLE_FONT_SMOOTHING=true (Lion 系统启用字体平滑以优化用户体验)

################################# XPack Configuration #################################
# XPack package, invalid setting in open source version
#
SSH_PORT=2222
RDP_PORT=3389
XRDP_PORT=3390
MAGNUS_MYSQL_PORT=33061
MAGNUS_MARIADB_PORT=33062
MAGNUS_REDIS_PORT=63790
MAGNUS_POSTGRESQL_PORT=54320
MAGNUS_SQLSERVER_PORT=14330
MAGNUS_ORACLE_PORTS=30000-30030

################################## Other Configuration 其他配置################################
# The terminal uses the host HOSTNAME as the identifier, automatically generated during the first installation
#
SERVER_HOSTNAME=${HOSTNAME}

# Use built-in SLB, if the client IP address obtained by the Web page is not correct, please set USE_LB to 0
# When USE_LB is set to 1, use the configuration proxy_set_header X-Forwarded-For $remote_addr
# When USE_LB is set to 0, use the configuration proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for
USE_LB=1

# The current running version number of JumpServer, automatically generated after installation and upgrade
#
TZ=Asia/Shanghai
CURRENT_VERSION=
```

### 4. 环境访问
安装成功后，通过浏览器访问登录 JumpServer
```
地址: http://<JumpServer服务器IP地址>:<服务运行端口>
用户名: admin
密码: ChangeMe
```
**⚠️ 安全提醒：首次登录后请立即修改默认密码！**

## 📊 效果截图
![jumpserver-login](./images/jumpserver-login)
![jumpserver-console](./images/jumpserver-console)

------

## 🔗 实用资源
- **官方文档**: <https://docs.jumpserver.org/>
- **GitHub 仓库**: <https://github.com/jumpserver/jumpserver>
- **社区支持**: <https://community.fit2cloud.com/>
- **在线体验**: <https://demo.jumpserver.org/>













