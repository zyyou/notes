## 安装与启动
```shell
sudo yum update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 启动
sudo systemctl start docker
# 执行测试镜像
sudo docker run hello-world
docker ps -a
```

## 配置文件

- 地址
```text
/usr/lib/systemd/system/docker.service
```

## 创建镜像

```shell
# Dockerfile 目录下执行
docker build . -t imagename
```

- Dockerfile参考
基于alinode，暴露10001端口，注意服务不能后台，否则容器会退出

```shell
FROM alinode
MAINTAINER zhengyy@9186.com
COPY eggtest /root/eggtest
WORKDIR /root/eggtest
EXPOSE 10001
CMD ["npm","start"]
```

## 搭建Harbor私有镜像仓
- 下载离线包 https://github.com/goharbor/harbor/releases
- 解压，修改配置文件，主要：hostname、harbor_admin_password、db_password
- 启动，install.sh
- 
```shell
# 修改启用非https push：/usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd --insecure-registry 192.168.*.*

systemctl  daemon-reload
service docker stop
service docker start
docker-compose start
```


## 常用命令
```shell
docker run -d -p 5001:10001 eggtest #启动后台运行容器，将宿主5001端口映射到容器10001端口
docker info # 列出信息
docker version  # 版本
docker ps -a   # 列出所有容器
docker rm    # 移除容器
docker port # 列出容器映射端口
```
[其他命令runoob](https://www.runoob.com/docker/docker-command-manual.html)