## 安装
1. 启用或关闭windows功能
- 适用于linux的windows子系统
- 应用市场安装ubuntu

## SSH登录
- 重装ssh

```shell
sudo apt remove openssh-server
sudo apt install openssh-server
```

- 编辑sshd_config文件

```shell
sudo vi /etc/ssh/sshd_config

# 以下配置由no改为 yes
PasswordAuthentication yes
```

- 配置ssh公钥

```shell
# 公钥写入 authorized_keys

# 设置权限
chmod 600 authorized_keys
chmod 700 ~/.ssh

```

- 重启ssh服务

```shell
sudo service ssh restart
```

- 其他配置参照git

## 安装桌面

```shell
# 非必须不建议安装，开发可以vscode远程

# 基础软件环境
sudo apt install xorg

# 安装桌面
sudo apt install  xfce4

# 安装远程桌面服务
sudo apt install xrdp

# 修改端口：/etc/xrdp/xrdp.ini  默认与windows冲突

# 会话环境配置文件
sudo echo xfce4-session > ~/.xsession

# 重启服务
sudo /etc/init.d/xrdp restart 

```
