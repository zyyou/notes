## 系统安装
- 下载完整镜像，避免网络不稳定造成的部分组建安装失败
- 选择一个界面，例如GNOME
- 配置网络

## 安装界面
如果使用完整镜像可以忽略该部分
```
yum groupinstall -y "X Window System"
yum groupinstall -y "GNOME Desktop"
systemctl set-default graphical.target  //设置成默认图形模式，multi-user.target命令行模式
```
## 多界面设置启动界面
登录界面点登录按钮左侧设置

## VMWare中安装vmtools
如果物理机安装忽略该部分
```
yum -y install perl gcc make kernel-headers kernel-devel        //重启后执行安装

//如果报错：The path "" is not a valid path to the 3.10.0-693.el7.x86_64 kernel headers.
执行：
yum install "kernel-devel-uname-r == $(uname -r)"
```

## 修改home下目录为英文
中文容易引起错误
```
export LANG=en_US //修改系统当前语言环境
xdg-user-dirs-gtk-update  //跳出对话框，提示是否更新成英文，同意即可
export LANG=zh_CN.UTF-8   //注意下次登录还要提示一次，选保留英文即可
```

## 安装VSCode参考
参考 https://code.visualstudio.com/docs/setup/linux

## 安装Nodejs
```
yum install -y epel-release
yum install -y nodejs

npm install n -g        //安装node版本管理工具
n stable        //更新node最新稳定版本，lts 最新长期维护版
```

## 安装git
- 下载解压进入目录git-***（注意不要放到中文目录）
```
yum install -y zlib-devel
yum install -y asciidoc
yum install -y xmlto
make configure
./configure --prefix=/usr/local/git --with-iconv=/usr/local/libiconv
make all doc
make install install-doc install-html
```
- 修改环境变量：/etc/profile，在最后一行添加 export PATH=/usr/local/git/bin:$PATH
- 保存生效：source /etc/profile
- 测试 git --version

## 开放端口
```
firewall-cmd --zone=public --add-port=3000/tcp --permanent  //permanent永久生效，没有此参数重启后失效
firewall-cmd --reload
firewall-cmd --zone=public --query-port=3000/tcp		//查看
firewall-cmd --zone=public --remove-port=3000/tcp --permanent
```

## SSH登录
- 参考git <a href="#/git?id=ssh-key配置">SSH-KEY配置</a> 生成秘钥 如id_rsa_dev，然后执行下面命令
- 如果没权限可以直接复制 id_rsa_dev.pub 的内容到对应服务器账号下，如/root/.ssh/下并改名为authorized_key（已存在就追加

```
ssh-copy-id -i ~/.ssh/id_rsa_dev.pub root@服务器IP    
ssh root@服务器IP   //测试，正常情况下此时已登录服务器不需要密码，报权限问题注意 SELinux

```
- 解决登录慢问题

```text
/etc/ssh/sshd_config
GSSAPIAuthentication no
UseDNS no

systemctl restart sshd
```

## mysql8 
修改认证方式
```
systemctl start mysqld.service      //启动
/var/log/mysqld.log       //启动后在此文件 查找默认密码
set global validate_password.policy=0 ;      //设置简单密码
set global validate_password.length=6;     //设置最小长度6
alter user user() identified by "密码";      //登陆后修改密码

//解决 Client does not support authentication protocol requested by server
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码';     
```

## sequelize
mysql 模型生成器
```
npm install -g sequelize-auto
npm install -g mysql
sequelize-auto -o "./define" -d zyytest -h localhost -u 数据库用户 -x 密码     //zyytest数据库模型生成到当前目录的define目录中
```