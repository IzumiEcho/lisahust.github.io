# Mars社区审核系统后台

[toc]

### 基本信息

- jdk1.8.0
- maven：3.6.3    /Users/miya2/.m2/setting.xml
- swagger
- mongodb



### User报错

heap太小了，在 `Build,Execution,Deployment > Compiler > Compiler ` 改成2048即可

sns：社交网络服务

jdk1.8.0

![image-20200709174648137](/Users/miya2/Library/Application Support/typora-user-images/image-20200709174648137.png)



### 本地跑所做的一些操作

1. maven配置
2. lombok下载
3. 堆变大
4. 更改启动项：VMOptions ： `-Denv=sit`



### 代码规范

- 文件不能以空行结束
- 包名只允许小写字母
- 每行不超过160个字符
- 方法不超过50行
- 方法参数个数不超过6个



### 框架

- java 1.8
- springboot
- maven-compiler ｜ maven checkstyle
- mybatis + mysql



### 依赖

- Guava：*Guava*是一种基于开源的Java库，其中包含谷歌正在由他们很多项目使用的很多核心库。

- jackson：处理JSON和XML格式化的类库

- Aho-Corasick：简称AC算法，通过将模式串预处理为确定有限状态自动机，扫描文本一遍就能结束。其复杂度为O(n)，即与模式串的数量和长度无关

- mybatis

- mysql

- Springfox-swagger2：Swagger是一系列用于Restful API开发的工具

  ```
      <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.8.0</version>
    </dependency>
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.8.0</version>
    </dependency>
  ```

- redission：Redis Java client with features of In-Memory Data Grid

- elasticsearch：搜索

- com.xiaohongshu.sns

- com.xiaohongshu.sns.thrift

- com.xiaohongshu.fls.thrift

- influxDB：*InfluxDB*(时序数据库),常用的一种使用场景:监控数据统计。


