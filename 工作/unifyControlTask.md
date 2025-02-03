## 学习

####  数据库表

|         告警          |  布控人员/用户   |     相机     |       日志        |          权限          |  短信   | 任务 |      车辆       |
| :-------------------: | :--------------: | :----------: | :---------------: | :--------------------: | :-----: | :--: | :-------------: |
|      alarm_info       |   person_info    |    device    | sys_operation_log |   tp_sys_permission    | message | task | tp_vehicle_info |
|  alarm_info_old_one   |   tp_sys_user    | device_copy1 | tp_sys_login_log  | tp_sys_role_permission |    -    |  -   |        -        |
|  vehicle_alarm_info   | tp_sys_user_role | device_group |         -         |           -            |    -    |  -   |        -        |
| vehicle_alarm_old_one |        -         |      -       |         -         |           -            |    -    |  -   |        -        |




#### 代码量

- service层: 5700行


- controller层: 1200行




#### 接口数

- 非直接封装ST: 38/42


- 直接封装ST: 52/52

​		统一流程: 1.构建对应Req对象 

​			         2.用HttpClientUnit(封装Hutool的HttpRequest) 调用ST接口 传入Req

​		         	3.解析原始Resp返回封装Resp

​		**难点在于看懂Req和Resp的所有字段作用, 信息量十分巨大! 需要结合产品, 亲自调用ST&KS接口,用F12自己调试才能完全理解!**

- 重点Controller:  PersonController  TaskController  VehicleController + UserController 



#### 配置

- aop


- kafka


- 全局异常处理器


- 日志


- minio存储系统


- mybatis拦截器


- 线程池


- redis


- security


- swagger




#### 问题

UserController.login()

AsyncManager



#### 定时任务

告警任务

获取相机任务

获取车辆告警任务



#### 接口

获取立即发送短信的TimerTask:

```
AsyncFactory.alarmException()
```

延迟10ms执行:

```
AsyncManager.me().execute(TimerTask task)
```



#### 问题

com.geshu.unify.controller.UserController#queryUserList 方法的@PreAuthorize("hasRole('ADMIN')")到底生不生效



## 工作量

1. 完成中转项目需求表仅涉及mysql的需求

   | 需求 |                        uri                         |
   | :--: | :------------------------------------------------: |
   |  29  |                 /log/queryLogList                  |
   |  25  |                /user/queryUserList                 |
   |  24  |                 /getDeploymentList                 |
   |  23  |                 /queryPersonAlarm                  |
   |  31  |                 /log/queryLogList                  |
   |  47  |              /log/queryLogStatistics               |
   |  48  |              /log/queryLogStatistics               |
   |  50  |              /log/queryLogStatistics               |
   |  51  |              /log/queryLogStatistics               |
   |  45  |              /log/queryLogStatistics               |
   |  53  |              /log/queryLogStatistics               |
   |  54  |              /log/queryLogStatistics               |
   |  56  |              /log/queryLogStatistics               |
   |  57  |              /log/queryLogStatistics               |
   |  59  |              /log/queryLogStatistics               |
   |  60  |              /log/queryLogStatistics               |
   |  68  |              /device/queryAllCameras               |
   |  70  |               /device/queryTabGroup                |
   |  72  |                      卜自己做                      |
   |  77  |             /device/queryAllCameras ??             |
   |  75  |                     跟我没关系                     |
   |  73  |              /device/queryAllCameras               |
   |  74  |              /device/queryAllCameras               |
   |  83  | user/selectRecentlyAlarm    person/queryPersonInfo |
   |  45  |              /log/queryLogStatistics               |
   | 110  |               /device/queryAllGroup                |
   |      |                                                    |

2. TotalAlarmJob定时任务

3. TpSysUserJob定时任务

4. 给alarm_info_old_one表加索引, 查询时间从 100秒 优化到 4秒

5. 给sys_user表添加sys_ip字段,并改动相应java代码

6. 给unifyControlTask数据库添加tp_sys_user_unit表, 并生成dao层

7. 给sys_user表添加dep_no和dep_name字段,并改动相应java代码

