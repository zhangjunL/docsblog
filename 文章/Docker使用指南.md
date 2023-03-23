## 安装
### 1.安装Docker
```bash
sudo apt update
```

### 2.安装必备软件包
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
### 3.然后将官方 Docker 版本库的 GPG 密钥添加到系统中：
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
### 4.Docker版本库添加到APT源
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
### 5.升级更新
```bash
sudo apt update
```
### 6.确保要从 Docker 版本库，而不是默认的 Ubuntu 版本库进行安装
```bash
apt-cache policy docker-ce
```
### 7.最后，安装 Docker ：
```bash
sudo apt install docker-ce
```
### 8.检查运行情况
```bash
  sudo systemctl status docker
```

## Docker 常用命令

### 0.查看docker版本
```bash
docker version
```
### 1.下载镜像
```bash
docker pull 镜像名称
```

  你可以使用公共镜像或者构建自己的镜像

### 2.构建镜像
```bash
docker build -t 镜像名称:tag .  (.不能省略)
```


### 3.运行镜像
```bash
docker run -d -p 宿主机端口:docker端口 --name 容器名称  镜像名称：tag(tag应递增)
```

### 4.查看镜像
```bash
docker images
```
### 5.查看容器
```bash
docker ps -a
```
### 6.删除镜像
```bash
docker rmi 镜像id
```
### 7.删除容器
```bash
docker rm 容器id
```
### 8.启动容器
```bash
docker start 容器id
```
### 9.停止容器
```bash
docker stop 容器id
```
### 10.进入容器
```bash
docker exec -it 容器id /bin/bash
```
### 11.退出容器
```bash
exit
```
### 12.查看容器日志
```bash
docker logs 容器id
```
### 13.查看容器内进程
```bash
docker top 容器id
```
