# DecTask

## 介绍

`DecTask`是一款简单高效、基于敏捷开发的项目管理工具，以事项驱动和敏捷开发最佳实践作为设计思想，适用于互联网团队进行高效协作和敏捷开发，交付极致卓越的产品。

官方网站 [https://dectask.com](https://dectask.com)  

原masterlab源代码 位于 masterlab分支 或下载tag包 https://github.com/gopeak/masterlab/releases/tag/v3.3.11





## 技术架构
服务器端：go语言的gin框架
前端：Vite、React、Typescript、Tailwindcss、Antd搭建，支持Websocket    
API：用Gin快速搭建restful风格API  
数据库：建议使用MySql8.0，未来支持 PostgreSQL, MariaDB, and TiDB  
数据库驱动：使用gorm实现对数据库的基本操作  
缓存：使用Redis  
异步：使用Go语言内置的workerpool实现异步处理
API文档：使用Swagger构建自动化文档  
配置文件：支持json,yaml,toml格式的配置文件。
日志：使用zap实现日志记录

##  普通安装

### 1.权限
```
    # 以下目录及子目录赋予读写权限
    ./log
    ./public/attachment
    ./public/document
    ./config.yaml
    
    # 赋予 ./dectask 执行权限
    chmod +x ./dectask

```
### 2.安装Mysql8.0数据库并创建数据库 `dectask` 以及用户 `dectask_user`
```sql
 -- 创建数据库示例
 CREATE DATABASE dectask CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
        
 -- 创建用户dectask,密码
 CREATE USER 'dectask_user'@'%' IDENTIFIED BY 'dectask_password';   
        
 -- 赋予用户权限示例(根据你的用户名调整)       
 grant all privileges on dectask.* to 'dectask_user'@'localhost';
 flush privileges;                               

```
数据库需要解除严格模式
```sql
set global sql_mode='NO_ENGINE_SUBSTITUTION';

```

### 3.安装Redis-server(可选)


### 4.修改配置文件 `./config.yaml`中的域名、数据库、Redis配置

### 5.在命令行终端中执行安装命令
```shell
  # 进入dectask目录 , 赋予 ./dectask 执行权限
  # 安装数据库表及基础必要数据, 默认安装的是英文语言
 ./dectask install zh_cn
 
  # 安装成功后会显示管理账号密码，请牢记保存

```
注： 安装成功后在当前目录下生成包含管理员账号和密码的account.log ，请牢记保存！！！！


### 6.运行服务
```shell
  
  # 进入dectask目录
  
 ./dectask 
 
 # 后台运行
./dectask  start -d

```

### 7.浏览器中访问配置域名



## 纯 docker 方式安装
安装docker后，设置加速镜像为国内镜像源。
如果是非root用户，请自行修改Dockerfile文件进行配置

docker 和 docker-compose安装示例
```shell
# centos7.9 安装docker 示例, 其他系统请自行安装docker
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
sudo docker version
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [ "https://08e906c87080263b0f0ec00f19ed3560.mirror.swr.myhuaweicloud.com" ]
}
EOF
sudo systemctl start docker
sudo systemctl enable docker

sudo curl -L "http://download.dectask.com/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
#sudo curl -L "https://github.com/docker/compose/releases/download/v2.39.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

```

```shell
docker build -t dectask-server .
```
修改 config.yaml 文件进行端口和数据库等配置项,运行
```shell
docker run -d \
  -p 8888:7777 \
  --name dectask \
  --network=host  \
  dectask-server
  
```
docker启动后会自动启动安装和初始化服务，并生成管理员账号和密码
```shell
 # 查看管理员账号和密码
docker exec dectask cat /app/log/account.log
```
常用的docker命令
```shell
 # 常用的命令
docker ps -a
docker stop dectask
docker start dectask
docker rm -f dectask
docker exec -it dectask /bin/sh
docker logs -f dectask
docker images  
 
```
支持环境变量方式启动，环境变量的值优先级高于config.yaml的配置项目

可用环境变量有
```shell
# 绑定域名 可以设置为 0.0.0.0
DECTASK_SYSTEM_DOMAIN=localhost  
# 绑定端口
DECTASK_SYSTEM_PORT=8888
# 生产环境 prod 开发环境 dev
DECTASK_SYSTEM_ENV=prod
# 加密密钥,自行修改
DECTASK_SYSTEM_ENCRYPT_KEY=DECTASK_SYSTEM_ENCRYPT_KEY
# 数据库类型,目前仅支持mysql
DECTASK_SYSTEM_DB_TYPE=mysql
# 数据库连接地址, 更改为实际的
DECTASK_DB_PATH=localhost:3306
# 数据库名称, 更改为实际的
DECTASK_DB_DBNAME=dectask
# 数据库用户, 更改为实际的
DECTASK_DB_USER=dev_user
# 数据库密码, 更改为实际的密码
DECTASK_DB_PASSWORD=123456
# 缓存开关, 默认关闭
DECTASK_SYSTEM_ENABLE_CACHE=false
# 缓存类型, 目前仅支持redis, 设置redis的地址和端口
DECTASK_REDIS_ADDR=127.0.0.1:6379
# 缓存密码, 默认为空
DECTASK_REDIS_PASSWORD=
# jwt密钥, 可修改
DECTASK_JWT_SIGNING_KEY=qmPlusfdfsdfdsffcxxsdfssfdsg
# 日志级别
DECTASK_ZAP_LEVEL=info
# 附件保存路径
DECTASK_ATTACHMENT_PATH=attachment/file

````
docker 设置环境变量启动示例
``` shell
  # 可传入环境变量
 docker run -d \
  -p 8888:7777 \
  --name dectask \
  -e DECTASK_DB_PATH=127.0.0.1:3306 \
  -e DECTASK_DB_DBNAME=dectask_docker \
  -e DECTASK_DB_USER=dectask_docker \
  -e DECTASK_DB_PASSWORD=FiSx2GfbS8882EMb \
   --network=host  \
  dectask-server  
  

 # 查看管理员账号和密码
docker exec dectask cat /app/log/account.log
 
```   



## docker-compose 方式安装，推荐使用此安装方式
如果docker-compose.yml
相关安装命令
```
# 赋予执行权限
chmod +x ./dectask
# 构建镜像
docker build -t dectask-server .
# 启动服务
docker-compose up -d
# 查看容器
docker-compose ps -a
# 如果没有启动成功，手动安装初始化数据库
docker-compose exec dectask sh
rm -f ./install.lock
./dectask install zh_cn -y
exit
# 查看管理员账号和密码
docker-compose exec dectask cat /app/log/account.log
# 重新启动
docker-compose stop dectask
docker-compose start dectask

# 如果已经启动，重新部署，则先执行以下命令
docker-compose down -v

# 查看容器
docker-compose ps -a

# 查看日志
docker-compose logs -f dectask

# 进入容器
docker-compose exec dectask sh
docker-compose exec mysql sh
docker-compose exec redis sh
# 进入容器后退出
exit

  
 ``` 
