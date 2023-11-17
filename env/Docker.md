# Docker

## 1.安装docker

### 1.1 centos

```bash
#  Linux 内核：官方建议 3.10 以上
uname -sa # 查看内核信息
yum update # 可以更新内核版本到该发行版的最新内核 注意：是该发行版最新内核不代表是最新内核。

# 卸载
yum remove docker*

# 安装 下面两个我都用了，都可行，暂时没踩坑
## 脚本安装
curl -sSL https://get.daocloud.io/docker | sh

## yum源安装 
### 2选1
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo（中央仓库）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里仓库）
### 查看版本
yum list docker-ce --showduplicates | sort -r
### 安装docker
yum install docker-ce-版本号
### 设置自动启动
systemctl start docker
systemctl enable docker
```

### 1.2 ubuntu

```xml
sudo apt-get remove docker docker-engine docker.io containerd runc

sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 添加 apt 源
echo \"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# 刷新源
sudo apt-get update
# 安装
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 2.docker命令

- 进入容器bash

```bash
docker exec -it mysql-slave[容器name] bash
```

- 复制文件

```bash
docker cp mysql-slave[容器name]:/etc/my.conf[文件地址] /root/
```

- Docker 版本号

```bash
docker -v
docker compose version
```

- 查看本地有的镜像

```bash
docker images
```

- 查看本地容器

```bash
docker ps -a
```

- 删除本地容器

```bash
docker stop 容器name|容器id
docker remove 容器name|容器id
```

- 拉取镜像

```bash
docker pull image_name:tag
```

- 运行镜像形成容器

```bash
docker run
-name=自定义容器名字
-it
-p 宿主机端口:容器端口
-e 参数名=值
-v 宿主机地址:容器内地址
-d (后台运行)
image_name:tag
```

- 停止容器

```bash
docker stop 容器名|容器id
```

- 开启容器

```bash
docker start 容器名|容器id
```

- 构建镜像

```bash
# 1. 先写好 Dockerfoile
docker build -t image_name:tag .
```

- 给镜像打tag

```bash
docker tag 【new_image_name:new_tag】[新的]  【image_name:tag】[旧的的] 
```

- 删除本地镜像

```bash
docker rmi images image_name:tag
```

1. Dockerfile

```Dockerfile
FROM image_name:tag  #基础镜像

ENV 

COPY

ADD
```
