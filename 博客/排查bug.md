# 线上排查bug

[toc]

### cat精确查询报错

1. [cat网站报错查询](https://cat.devops.xiaohongshu.com/cat/r/p?op=view&domain=sns-themis&date=2020072710)
2. 点击 `SampleLinks` 进行每个接口的报错日志查询，log每个字母代表一个log
3. 点击 `Graph` 可以更清楚的看到整个过程



### mars报错日志

1. [kibana日志报错查询](http://traefik-kibana.prod.xiaohongshu.com/app/kibana)
2. 点击 `discover` 
3. sns-themis   
4. 在条件查询框输入 `http_host:"mars.xiaohongshu.com"  AND request:"/api/themis" AND request_time:>6`
5. upstream_host指的是实例
6. 一般kibana里面的行数是idea的行数-4



### 发版报错日志

1. [kibana日志报错查询](http://traefik-kibana.prod.xiaohongshu.com/app/kibana)
2. 点击 `discover` 
3. sns-themis



### kong

可以了解一下kong

Kong-cvm-access

