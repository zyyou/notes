## 改阿里源
- 改配置

/etc/apt/sources.list 文件前加以下内容
```
#阿里源
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

- 更新
```
sudo apt update
sudo apt upgrade
```

## 类windows任务栏
- 安装 优化 工具
```
sudo apt install gnome-shell-extensions gnome-shell-extension-dash-to-panel gnome-tweaks adwaita-icon-theme-full
```
- 启用扩展 Dash to panel

## 安装搜狗输入法
- 安装依赖库
```
sudo apt install fcitx-bin
sudo apt install fcitx-table
```

- 官网下载，双击deb安装

- 启用fcitx
    - 设置/区域和语言/管理已安装的语言/键盘输入法系统 选fcitx
    - 右下角/小键盘/配置/输入法 选搜狗拼音

## deepin wine
- 安装deepin-wine
    - 下载或克隆 https://github.com/wszqkzqk/deepin-wine-ubuntu
    - 安装 sudo sh ./install.sh

- 软件容器下载
下载后直接点击deb安装
http://mirrors.aliyun.com/deepin/pool/non-free/d
