## SSH-KEY配置

生成SSH-KEY
```
ssh-keygen -t rsa -C "***@***.***"
Enter file in which to save the key (~/.ssh/id_rsa): 输入文件名，如：id_rsa_***
```
然后复制id_rsa_***.pub的内容，配置到对应的git服务器

## 配置ssh config
在.ssh文件夹新建一个名为config的文件，内容参考（注意账号和私钥文件）
```
Host code.aliyun.com
    HostName code.aliyun.com
    User aa@aa.com
    PreferredAuthentications publickey
    IdentityFile /Users/zyy/.ssh/id_rsa_aa
Host github.com
    HostName github.com
    User bb@bb.com
    PreferredAuthentications publickey
    IdentityFile /Users/zyy/.ssh/id_rsa_bb
```

## 添加到agent
把所有的id_rsa私钥文件添加到SSH-Agent，命令如下：
```
ssh-add id_rsa_**
```
如果 Could not open a connection to your authentication agent，则需要启动agent，
出现类似的 Agent pid ** 的字符,则说明启动成功。
以下命令之一：
```
eval $(ssh-agent -s)
eval `ssh-agent -s`
ssh-agent bash
```

## 配置用户名和邮箱
每个项目都要配置，先获取代码仓库，然后在仓库目录（有.git文件夹的）执行：
```
git config --local user.name "zyy"
git config --local user.email "***@**.**"
```

## 测试
```
ssh -T git@code.aliyun.com
```
