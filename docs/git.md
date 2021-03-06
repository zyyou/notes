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

## config权限
```
sudo chmod 600 config
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

## 常用命令
```
git clone --depth 1 仓库地址    //clone最近1次提交的代码
git pull    //拉取并自动合并
git fetch   //拉取不自动合并
git branch dev  //创建dev分支
git push origin dev //提交到远程分支dev
git checkout dev    //切换到dev分支
git add -A  //暂存本地所有更改
git commit -m 备注  //提交本地暂存更改

git merge dev   //将dev分支合并到当前分支
git branch -d dev   //删除本地分支 dev
git push origin --delete dev    //删除远程分支dev


git checkout -- test.txt    //撤销未暂存的文件 test.txt
git reset HEAD test.txt    //移除暂存文件test.txt(保持编辑后内容)
git rm --cached test.txt    //从暂存区移除文件test.txt（状态为编辑后且未git add）

git commit --amend  //修复提交（修改提交备注、添加文件等）
git remote set-url origin 新地址    //更新远程仓库地址

```

## 常见问题
- There is no tracking information for the current branch.
```text
原因：本地分支没有和远程分支建立联系
git branch -vv 查看本地分支和远程分支的关联关系
本地dev关联到远程dev：
git branch --set-upstream-to=origin/dev dev
```

- 用错分支没commit
用错分支还写了很多代码的情况
```shell
git add -A
# 提交到暂存栈
git stash
git checkout 目标分支
# 提出代码s
git stash pop
```

- 用错分支并且已经提交
```shell
git reset HEAD~1 
git stash
# 后面同上
```

- 大小写重命名，不推荐修改配置为大小写敏感，默认true不敏感

```shell
# 不推荐
git config core.ignorecase false

# 推荐，执行两次 Aaa.txt-> aaa.txt
git mv Aaa.txt bbb.txt && git mv bbb.txt aaa.txt

# 重命名目录
git mv -f Aaa aaa1 && git mv -f aaa1 aaa
```

- 回退版本

```shell
# 备份当前分支
git branch dev-backup
# 回退
git reset --hard 49dd91252ac662de2e01a1415d2d95f931ac03ef
# 强制提交
git push -f -u origin dev 
```

