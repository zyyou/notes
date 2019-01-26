## 确认U盘路径
```
sudo fdisk -l       //例如  /dev/sdb
```

## 格式化
```
sudo mkfs.vfat /dev/sdb -I
```

## 制作
```
sudo dd bs=4M if=/home/zyy/Downloads/ubuntu-18.04.1-desktop-amd64.iso of=/dev/sdb
```

## 查看进度
另启一个终端，执行
```
sudo watch -n 5 killall -USR1 dd
```
