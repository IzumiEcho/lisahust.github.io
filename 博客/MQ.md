# MQ待解决问题

[toc]

### 1. 质检MQ缺少qualityStartTime

典型案例：msgId=AC140EDD0001626B2D4A6F0B251A8382 noteId=5f5da90c000000000101f965

错误原因：质检MQ没有qualityStartTime导致get的时候报错 `java.lang.NullPointerException`

解决方式：只能由上游解决，因为质检人员需要计算平均时间



### 2. 同一个SourceNoteIdHistoryId发了两次

典型案例：note_id：5f5b627e000000000101f8d3

错误原因：这条记录，发了两次二审mq，且二审审核人不一样。会导致这个错题的人会发现这道题与他无关但是在他的错题，因为二审人记录被覆盖了。但是质检mq的二审人发的是第一次的审核人结果和id

```
2020-09-14 05:15:46 first mid_normal 5f5b627e000000000101f8d3 134926992423714435 5c67fa28000000001200c55e
2020-09-14 05:15:51 second mid_normal 5f5b627e000000000101f8d3 134926992423714435 5948b1035e87e750a6fd2e19
2020-09-14 05:18:17 second mid_normal 5f5b627e000000000101f8d3 134926992423714435 5ec6afba00000000010061d8
2020-09-14 13:55:45 quality mid_normal 

质检mq
一审5c67fa28000000001200c55e
二审5948b1035e87e750a6fd2e19
{
"id": 0,
"taskType": "quality",
"source": "quality_mid_normal",
"noteId": "5f5b627e000000000101f8d3",
"historyId": 134926992423714430,
"firstProcessorId": "5c67fa28000000001200c55e",
"firstResult": "{\"level\":2,\"remarks\":\"\"}",
"secondProcessorId": "5948b1035e87e750a6fd2e19",
"secondResult": "{\"level\":2,\"remarks\":\"\"}",
"qualityHistoryId": 134926992423714430,
"qualityProcessorId": "5bcc774fb1e5b70001731b14",
"qualityResult": "{\"level\":5,\"remarks\":\"注明转载\"}",
"qualityStartTime": 1600062902000,
"qualityEndTime": 1600062945303,
"taskCreateTime": 1600062902000
}
```

解决方式：目前由下游控制导致记录不能被覆盖，但是无法判断质检发的mq的二审人信息是第一个二审人还是第二个二审人。所以上游还是检查一下为什么会发两个二审mq吧。



### 3. 二审mq在一审mq前导致该条笔记记录作废

典型案例：note_id：5f5eaf770000000001006073，这个二审的mq在一审的mq前面发

```
1.  2020-09-14 10:59:39  first  mid_normal  5f5eaf770000000001006073  134930621553600345
2.  2020-09-14 11:50:08  first  mid_normal  5f5eaf770000000001006073  134930815884107498
3.  2020-09-14 12:38:36  first  mid_normal  5f5eaf770000000001006073  134930888697246564
4.  2020-09-14 12:39:25  second mid_normal  5f5eaf770000000001006073  134930915540749673
4.  2020-09-14 12:40:29  first  mid_normal  5f5eaf770000000001006073  134930915540749673
5.  2020-09-14 12:40:57  first  mid_normal  5f5eaf770000000001006073  134930915540749673
6.  2020-09-17 10:07:26  quality  quality_mid_normal  5f5eaf770000000001006073  134930915540749673
```

