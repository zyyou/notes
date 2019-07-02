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

- 参考

## 常用命令
```shell
docker info # 列出信息
docker version  # 版本
docker ps -a   # 列出所有容器
docker rm    # 移除容器
docker port # 列出容器映射端口
```
[其他命令runoob](https://www.runoob.com/docker/docker-command-manual.html)