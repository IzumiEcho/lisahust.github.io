```
&lt; less than <
&gt; greater than >
&amp; ampersand &
```





### if

```xml
<if test="bookName!=null">  
<if test="type!=null and type!=''">
<if test='id != null and id lt 28 '>  //lt小于
<if test='id != null and id lte 28 '>  //lt小于等于
<if test="'xiaohong' eq username or 'xiao' eq username ">
<if test="username != null and username.indexOf('xiao')==0">  //indexOf()  lastIndexOf()  判断是否包含某个特定字符

```



SELECT id, source, note_id, history_id, user_id, role, wrong_time, first_processor_id, first_result, first_time, second_processor_id, second_result, second_time, quality_processor_id, quality_result, quality_time, operation_processor_id, operation_result, operation_time, star_flag, appeal_flag, appeal_reason, appeal_result FROM wrong_verify_note_record WHERE source = 'mid_normal' AND user_id = '5c3c741b000000000601026b' AND (role = 1 or role = 2) And wrong_time Between '2020-6-30 17:50:32' AND '2020-10-30 17:50:32' ORDER BY wrong_time DESC limit 0, 10