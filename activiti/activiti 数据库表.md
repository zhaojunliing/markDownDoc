## activiti 数据库表

数据库表概述：


### act_ge: general,保存通用的数据  

	act_ge_byteArray 资源表 保存流程定义和流程资源
	act_ge_property 属性表

### act_re: repository, 存储流程定义和流程部署的信息  

	act_re_deployment 流程部署表，每部署一次，会写入一条记录
	act_re_procdef 流程定义表，通过解析bpmn文件，把数据保存到这里

### act_id: identity, 保存身份数据  

    act_id_info 用户信息表
    act_id_group 用户组表
    act_id_user 用户表
    act_id_membersship 用户组与用户关联表

### act_ru: runtime, 保存流程运行时的数据  

	act_ru_execution 执行表 保存流程实例和执行流数据
	act_ru_task 任务表
	act_ru_variable 参数表 用于任务参数，流程参数
	act_ru_identityLink 流程与身份的关系表 ，如用户与任务的关系
	act_ru_job 一般的工作表
	act_ru_deadletter_job 无法执行的工作表
	act_ru_suspended_job 中断的工作表
	act_ru_timer_job 定时器工作表 
	act_ru_event_subsrc 事件描述表

### act_hi: history, 保存流程的历史数据

	act_hi_procinst 流程实例的历史数据
	act_hi_taskinst 流程实例历史任务表
	act_hi_actinst 历史流程实例表
	act_hi_attachment 流程附件表
	act_hi_comment 流程评论表

备份数据说明：

需要备份的表有

1. act_ge_byteArray  （使用二进制备份）

   select t.*, t.rowid from ACT_GE_BYTEARRAY t where t.deployment_id_ = '' or t.id_ = '' or t.id_ = ''

   将4条数据使用pl/sql的备份备份出来

2. act_ge_property

3. act_re_deployment

4. act_re_procdef

5. act_re_model  （公司内部的表）