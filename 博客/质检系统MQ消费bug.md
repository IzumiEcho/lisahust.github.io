# 质检MQ消费

[toc]



```
delete from task_danger_user
where user_id  in (select u_id from (select user_id as u_id  from task_danger_user where task_status != 'done'  group  by  user_id   having  count(user_id) > 1) as b)
  and id not in (select m_id from (select min(id) as m_id from  task_danger_user where task_status != 'done' group by user_id  having count(user_id )>1) as a)
  and task_status != 'done';
```



### 将错题时间改为审核时间

```
update wrong_verify_note_record
SET wrong_time = first_time
WHERE role = 1

update wrong_verify_note_record
SET wrong_time = second_time
WHERE role = 2

select * from wrong_verify_note_record
WHERE role = 2 and id = '117758'

update wrong_verify_note_record
SET role = 1
WHERE user_id = first_processor_id

update wrong_verify_note_record
SET role = 2
WHERE user_id = second_processor_id
```



### 将之前的数据增加role

```
//14547
select * from wrong_verify_note_record where role is null order by first_time desc limit 2 \G;

//一审没有role的 7032
select count(*) from wrong_verify_note_record
WHERE user_id = first_processor_id and role is null;

//更新一审
update wrong_verify_note_record
SET role = 1
WHERE user_id = first_processor_id and role is null;

select * from wrong_verify_note_record WHERE first_processor_id = second_processor_id and role is null;

//二审没有role的 7323
select count(*) from wrong_verify_note_record
WHERE user_id = second_processor_id and role is null;

//更新二审role
update wrong_verify_note_record
SET role = 2
WHERE user_id = second_processor_id and role is null;

//186
select * from wrong_verify_note_record
WHERE user_id != first_processor_id and user_id != second_processor_id and role is null limit 4\G;
```



### 删除qi重复错题

```
//3065
select count(resource_id) from (select resource_id,source from common_task_queue where source = 'ecology_review'
group by resource_id having count(resource_id) > 1) as a;

//3024个第一次
select m_id from (select min(id) as m_id from  common_task_queue group by resource_id having count(resource_id )>1) as b;

select hex(resource_id) from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id)>1) as b) and source = 'mid_video' ;

delete from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id)>1) as b) and source = 'mid_video';

delete from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'ecology_review' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'ecology_review' group by resource_id having count(resource_id)>1) as b) and source = 'ecology_review';
```



```
public Object getWrongNoteDetailByPageTurn(String source, String userId, String startTimeStr, String endTimeStr, String thisTimeStr, int role, int turnFlag, int starFlag, int appealFlag) {
    //start是小的 end是大的
    if (turnFlag == -1) {  //下一页
        endTimeStr = thisTimeStr;
    } else {  //上一页
        startTimeStr = thisTimeStr;
    }
    log.info("start::{}::end{}::", startTimeStr, endTimeStr);
    switch (role) {
        case 1 :
            List<TWrongVerifyNoteDTO> dtoList1 = processorUserService.getWrongVerifyNoteListByFlag(source, userId, startTimeStr, endTimeStr, starFlag, appealFlag);
            if (null == dtoList1 || dtoList1.size() == 0 || dtoList1.size() == 1) {
                return null;
            }
            TWrongVerifyNoteDTO dto1 = (turnFlag == -1) ? dtoList1.get(1) : dtoList1.get(dtoList1.size() - 2);
            return processorUserService.getVerifyWrongNoteDetail(dto1.getSource(), dto1.getNoteId(), dto1.getHistoryId(), userId);
        case 3 :
            List<TWrongVerifyNoteDTO> dtoList2 = qualityUserService.getQualityWrongNoteDTOListByFlag(source, userId, startTimeStr, endTimeStr, starFlag);
            if (null == dtoList2 || dtoList2.size() == 0 || dtoList2.size() == 1) {
                return null;
            }
            TWrongVerifyNoteDTO dto2 = turnFlag == -1 ? dtoList2.get(1) : dtoList2.get(dtoList2.size() - 2);
            return qualityUserService.getQualityWrongNoteDetail(dto2.getSource(), dto2.getNoteId(), dto2.getHistoryId(), userId);
        case 4 :
            List<TQualityAppealRecordDTO> dtoList3 = operationUserService.getOperationAppealNoteDTOListByFlag (source, userId, startTimeStr, endTimeStr, starFlag, appealFlag);
            if (null == dtoList3 || dtoList3.size() == 0 || dtoList3.size() == 1) {
                return null;
            }

            TQualityAppealRecordDTO dto3 = turnFlag == -1 ? dtoList3.get(1) : dtoList3.get(dtoList3.size() - 2);
            return operationUserService.getOperationWrongNoteDetail(dto3.getSource(), dto3.getNoteId(), dto3.getHistoryId(), userId);
        default :
            log.error("getWrongNoteDetailByPageTurn::role::{}::isWrong", role);
    }
    return null;
}
```





TQualityAppealRecordDTO(id:100024, source:mid_normal, noteId:5f64b6060000000000000400, historyId:134937246791896321, appealTime:1601284557000, processTime:0, isFirstProcessorRight:0, isSecondProcessorRight:0, isQualityProcessorRight:0, isRuleProblem:0, starFlag:0, appealFlag:0), 

TQualityAppealRecordDTO(id:100023, source:mid_normal, noteId:5f64b5a800000000000003fc, historyId:134937245214838011, appealTime:1601207698000, processTime:0, isFirstProcessorRight:0, isSecondProcessorRight:0, isQualityProcessorRight:0, isRuleProblem:0, starFlag:0, appealFlag:0), 

TQualityAppealRecordDTO(id:100022, source:mid_normal, noteId:5f64b59800000000000003fb, historyId:134937244946402554, appealTime:1601207694000, processTime:0, isFirstProcessorRight:0, isSecondProcessorRight:0, isQualityProcessorRight:0, isRuleProblem:0, starFlag:0, appealFlag:0), 

TQualityAppealRecordDTO(id:100021, source:mid_normal, noteId:5f64950900000000000003e4, historyId:134937105125084168, appealTime:1601175395000, processTime:0, isFirstProcessorRight:0, isSecondProcessorRight:0, isQualityProcessorRight:0, isRuleProblem:0, starFlag:0, appealFlag:0)]

### 删除重复背景图(task)



select source, sum(num) from (select resource_id,source,count(1) as num from common_task_queue group by resource_id, source having num>1) a group by source;



select resource_id,source,max(task_time), count(1) as num from common_task_queue where source='ecology_review' group by resource_id, source having num>1;



```
//3065
select count(resource_id) from (select resource_id,source from common_task_queue where source = 'ecology_review'
group by resource_id having count(resource_id) > 1) as a;

//3024个第一次
select m_id from (select min(id) as m_id from  common_task_queue group by resource_id having count(resource_id )>1) as b;

select hex(resource_id) from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id)>1) as b) and source = 'mid_video' ;

delete from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'mid_video' group by resource_id having count(resource_id)>1) as b) and source = 'mid_video';

delete from common_task_queue where resource_id in (select resource_id from (select resource_id from common_task_queue where source = 'ecology_review' group by resource_id having count(resource_id) > 1) as a ) and id not in (select m_id from (select min(id) as m_id from common_task_queue where source = 'ecology_review' group by resource_id having count(resource_id)>1) as b) and source = 'ecology_review';
```

select hex(resource_id),source from (select resource_id,source from common_task_queue  group by resource_id having count(resource_id) > 1) as a  where source = 'risk_note_video_first' group by source;



select count(resource_id),source from (select resource_id,source from common_task_queue  group by resource_id having count(resource_id) > 1) as a group by source;



select count(resource_id),source from (select resource_id,source from common_task_queue having count(resource_id) > 1) as a group by source

### 删除重复错题

//484条重复的 484*7

```
select note_id from (select note_id from wrong_verify_note_record 
group by source,note_id,history_id,user_id having count(note_id) > 1) as a;

select note_id,count from (select note_id,count(*) from wrong_verify_note_record group by  source,note_id,history_id,user_id having count(note_id) > 1) as a;
```

//484条最小的

```
select m_id from (select min(id) as m_id from  wrong_verify_note_record group by source,note_id,history_id,user_id  having count(note_id )>1) as b;
```

//2690条（484*6=2904条，但是有些不是7）

```
select count(*) from wrong_verify_note_record 
where note_id in (select note_id from (select note_id from wrong_verify_note_record group by  source,note_id,history_id,user_id having  count(note_id) > 1) as a) 
and id not in (select m_id from (select min(id) as m_id from wrong_verify_note_record group by source,note_id,history_id,user_id having count(note_id )>1) as b);

delete from wrong_verify_note_record where note_id in (select note_id from (select note_id from wrong_verify_note_record group  by  source,note_id,history_id,user_id having  count(note_id) > 1) as a) and id not in (select m_id from (select min(id) as m_id from  wrong_verify_note_record group by source,note_id,history_id,user_id  having count(note_id )>1) as b);


where 5a52cb874eacab6858e5cda8
```



### 删除重复错题



```
### 删除
select note_id, count(1) as num from wrong_verify_note_record group by source, note_id,user_id,history_id,role,appeal_flag having num>1;

select count(*) from wrong_verify_note_record 
where note_id in (select note_id from (select note_id, count(1) as num from wrong_verify_note_record where note_id = '5f3835e800000000010048b4' group by source, note_id,history_id,user_id,role,appeal_flag having num > 1) as a) 
and id not in (select m_id from (select min(id) as m_id,count(1) as num from wrong_verify_note_record where note_id = '5f3835e800000000010048b4' group by source, note_id,history_id,user_id,role,appeal_flag having num>1) as b);

delete from wrong_verify_note_record 
where note_id in (select note_id from (select note_id, count(1) as num from wrong_verify_note_record where note_id = '5f3835e800000000010048b4' group by source, note_id,history_id,user_id,role,appeal_flag having num > 1) as a) 
and id not in (select m_id from (select min(id) as m_id,count(1) as num from wrong_verify_note_record where note_id = '5f3835e800000000010048b4' group by source, note_id,history_id,user_id,role,appeal_flag having num>1) as b);


select count(*) from wrong_verify_note_record 
where note_id in (select note_id from (select note_id, count(1) as num from wrong_verify_note_record group by source, note_id,history_id,user_id,role,appeal_flag having num > 1) as a) 
and id not in (select m_id from (select min(id) as m_id,count(1) as num from wrong_verify_note_record group by source, note_id,history_id,user_id,role,appeal_flag having num>1) as b);

delete from wrong_verify_note_record 
where note_id in (select note_id from (select note_id, count(1) as num from wrong_verify_note_record group by source, note_id,history_id,user_id,role,appeal_flag having num > 1) as a) 
and id not in (select m_id from (select min(id) as m_id,count(1) as num from wrong_verify_note_record group by source, note_id,history_id,user_id,role,appeal_flag having num>1) as b);
```







### 删除重复noteRecord

```
//每一个派生出来的表都必须有一个自己的别名

select note_id from (select note_id from statistics_note_record where source = 'mid_normal' group  by  source,note_id,history_id   having  count(note_id) > 1) as a;

//298条重复的

select note_id from (select note_id from statistics_note_record group  by  source,note_id,history_id   having  count(note_id) > 1) as a;

//298条最小的

select m_id from (select min(id) as m_id from  statistics_note_record group by source,note_id,history_id  having count(note_id )>1) as b;

select count(*) from statistics_note_record where note_id in (select note_id from (select note_id from statistics_note_record group  by  source,note_id,history_id   having  count(note_id) > 1) as a) and id not in (select m_id from (select min(id) as m_id from  statistics_note_record group by source,note_id,history_id  having count(note_id )>1) as b) ;

delete from statistics_note_record where note_id in (select note_id from (select note_id from statistics_note_record group  by  source,note_id,history_id   having  count(note_id) > 1) as a) and id not in (select m_id from (select min(id) as m_id from  statistics_note_record group by source,note_id,history_id  having count(note_id )>1) as b) ;
```







### 排查质检数据对不齐问题

```
select quality_processor_id, source, count(*)  from statistics_note_record where quality_time > '2020-09-17 9:15:00' and quality_time < '2020-09-17 9:20:00' group by source,quality_processor_id;

select quality_processor_id, source, count(*) from task_quality_note_done where quality_end_time > '2020-09-17 9:15:00' and quality_end_time < '2020-09-17 9:20:00' group by source,quality_processor_id;



select  *  from statistics_note_record where quality_processor_id='58c8c0236a6a6972ea0c84e2' and source = 'mid_normal' and quality_time > '2020-09-17 9:15:00' and quality_time < '2020-09-17 9:20:00' \G;

select * from task_quality_note_done where quality_processor_id='58c8c0236a6a6972ea0c84e2' and source = 'quality_mid_normal' and quality_end_time > '2020-09-17 9:15:00' and quality_end_time < '2020-09-17 9:20:00' \G;

select * from task_quality_note_done where quality_end_time > '2020-09-16 9:50:00' and quality_end_time < '2020-09-16 9:55:00' ;
```







### 统计中心MQ

##### 一审MQ

```
{
    "taskType": "first",
    "noteId": "5f48ef83000000000100b2ec",
    "source": "mid_normal",
    "historyId": 134906707377042110,
    "processorId": "5aeb28afe8ac2b2b30b74d2e",
    "result": "{\"level\":2,\"remarks\":\"\"}",
    "startTime": 1598622741000,
    "endTime": 1598623071377
}
```



##### 二审MQ

注意：note_id、history_id和source三个数据确定唯一一条note记录，然后这个必须在note表中有数据才会二审mq进行处理，否则就不处理

```
{
    "taskType": "second",
    "source": "mid_normal",
    "noteId": "5f47639b000000000100302d",
    "historyId": 134905006989442240,
    "firstProcessorId": "58eb210482ec3969e795f11b",
    "processorId": "5bfe7abd000000000701e785",
    "result": "{\"level\":2,\"remarks\":\"疑似营销\"}",
    "endTime": 1598537008620
}
```







##### 三审MQ

```
{
    "taskType": "quality",
    "source": "quality_mid_normal",
    "noteId": "5f47639b000000000100302d",
    "historyId": 134905006989442240,
    "firstProcessorId": "58eb210482ec3969e795f11b",
    "firstResult": "{\"level\":2,\"remarks\":\"疑似营销\"}",
    "secondProcessorId": "5bfe7abd000000000701e785",
    "secondResult": "{\"level\":2,\"remarks\":\"疑似营销\"}",
    "qualityHistoryId": 134905006989442240,
    "qualityProcessorId": "5dfb0ae20000000001001db5",
    "qualityResult": "{\"level\":2,\"remarks\":\"\"}",
    "qualityStartTime": 1598582730000,
    "qualityEndTime": 1598582739218,
    "taskCreateTime": 1598582730000
}
```

select * from statistics_note_record where note_id = '5f5eaf770000000001006073' and history_id = '134930915540749673' and source = 'mid_normal'