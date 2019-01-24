## 安装
下载后解压到 /usr/local/mongodb

## 配置文件
创建/etc/mongodb/mongodb.conf，内容参考
```
port=27017 #端口  
dbpath= /home/data/db #数据库存文件存放目录  
logpath= /home/logs/mongodb.log #日志文件存放路径  
logappend=true #使用追加的方式写日志  
fork=true #是否以守护程序的方式启用  
maxConns=100 #最大同时连接数  
#noauth=true #是否启用验证  
journal=false #每次写入会记录一条操作日志（通过journal可以重新构造出写入的数据）。
#即使宕机，启动时wiredtiger会先将数据恢复到最近一次的checkpoint点，然后重放后续的journal日志来恢复。
#storageEngine=wiredTiger  #存储引擎有mmapv1、wiretiger、mongorocks
#bind_ip = 127.0.0.1  #这样就可外部访问了，例如从win10中去连虚拟机中的MongoDB
```

## 设置PATH  
/etc/profile    最后增加一行，重启后生效
```
export PATH=/usr/local/mongodb/bin:$PATH
```

## 启动服务
```
mongod --config /etc/mongodb/mongodb.conf
```

## 客户端
```
mongo   //不启用验证模式
mongo -u zyy   //以zyy连接
```

## 增加用户
```
use admin
db.createUser(
  {
    user: "zyy", //用户名
    pwd: "密码", //密码
    roles: [ { role: "root", db: "admin" } ] //权限
  }
)

#角色授权
db.grantRolesToUser( "zyy" , [ { role: "dbOwner", db: "zyytest" } ])
```

## 停止
```
use admin;
db.shutdownServer();
```

## 新建、删除数据库
```
use zyytest      // show dbs不显示，需要写入数据后才显示
db.dropDatabase()
```

## 数据操作
```
db.testtb.insert({val1:1,val2:"aabb"})  //写入
db.testtb.find().pretty()     //查询， pretty()  非必须，格式化显示

show tables     //显示所有表
db.testtb.drop()    //删除表
db.testtb.update({'val1':1},{$set:{'val2':'MongoDBTest'}},{multi:true})   //更新，multi 是否执行多行，否只一行
```

