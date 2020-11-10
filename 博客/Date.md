```
//sql
String str = yesterdayDate.toString()
new java.sql.Date(utilDate.getTime()-24*60*60*1000)



```





### tips

- 数据库查询的时候mapper文件写timestamp



### Long & Timestamp

```
//long转换成timeStamp
Long qualityTime = detailPO.getQualityTime();
Timestamp.valueOf(String.valueOf(qualityTime))

//timeStamp转换成long
Timestamp startTime = msg.getStartTime();
Timestamp endTime = msg.getEndTime();
long time = endTime.getTime() - startTime.getTime();
```



### String & Timestamp

```
//string转timestamp
//string 变成 timestamp ,string 必须为 “yyyy-MM-dd HH:mm:ss”
//timestamp打印出来为“2020-07-02 12:33:11.0”
//String.valueOf(time)是“2020-07-02 12:33:11.0”
//time.getTime()打印出来是long
String str = "2020-07-02 12:33:11";
Timestamp time = Timestamp.valueOf(str);

//timestamp转string
Timestamp time = new Timestamp();
String str = String.valueOf(time);
```



### Date 转 Timestamp

```
//date转timestamp
Date d = new Date();
Timestamp t = new Timestamp(d.getTime());

Timestamp nowTime = new Timestamp(new Date().getTime());
```



### Date转String

```
//获取此时日期
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");
String date = df.format(new java.util.Date());
```

RPC调用失败:(路由信息:/api/themis/wrongVerifyNote/getWrongVerifyNoteListByPage),<br>(错误信息:Internal error processing getWrongVerifyNoteListByFlagByPage)





### Date转Long

```
Date date = new Date();
long dateTime = date.getTime();


long dateTime = 14830682769461;
Date date = new Date(dateTime);
System.out.println(date.toString());
```

