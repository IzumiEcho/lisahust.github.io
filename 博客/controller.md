





```
@ApiOperation("运营人员获取所有错题列表")
@GetMapping("/getOperationWrongNotesByFlagByPage")
public BaseResponse.SuccessResponse getWrongNoteByFlagByPage(

  @RequestParam("source") String source,
  @RequestParam("userId") String userId,
  @RequestParam(value = "开始时间", defaultValue = "1593510632000") String startTimeStr,
  @RequestParam(value = "endTime", defaultValue = "1604051432000") String endTimeStr,
  @RequestParam(value = "starFlag", defaultValue = "-1") int starFlag,  //-1全部，0未标记，1已标记
  @RequestParam(value = "appealFlag", defaultValue = "-1") int appealFlag,  //-1全部，0未申诉，1已申诉，2已处理
  @RequestParam(value = "pageNo", defaultValue = "1") int pageNo,
  @RequestParam(value = "perPage", defaultValue = "10") int perPage) {

return BaseResponse.success(operationUserService.getWrongNoteByFlagByPage(source, userId, startTimeStr, endTimeStr, starFlag, appealFlag, pageNo, perPage));

}
```

