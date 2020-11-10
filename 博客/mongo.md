# mongo



### 本地起mongod服务

```
//后台起mongo服务
mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork

//查看后台有没有起mongo服务
ps aux | grep -v grep | grep mongod
```



### 起mongo

```
在任意地方输入mongo即可
因为在~/.bash_profile已经配置过了
```

