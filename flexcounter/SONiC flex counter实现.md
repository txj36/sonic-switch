# SONiC flex counter实现

SONiC(201811) FlexCounter消息流如图
![](assets/SONiC%20flex%20counter实现.23-54-17.png)

## 关键表描述

- FLEX_COUNTER_DB(5)
  - FLEX_COUNTER_GROUP 计数器组表：配置不同counter组统计间隔、模式、使能、插件等
    - counter group类型有下面几种
      - PORT
      - QUEUE
      - QUEUE_WATERMARK
      - PG_WATERMARK(PG 优先级队列)
      - PFCWD
  - FLEX_COUNTER 计数器表: 配置具体的统计
    - COUNTER_ID_LIST，如SAI_PORT_STAT_IF_IN_OCTETS、SAI_PORT_STAT_IF_IN_UCAST_PKTS等
    - ATTR_ID_LIST

- CONFIG_DB (4)
  - 资源数据映射，便于查找
    - PORT_NAME_MAP 维护port名与id映射
    - QUEUE_xxx_MAP 维护QUEUE的NAME、PORT、INDEX、TYPE映射
    - PG_xxx_MAP 维护PG的NAME、PORT、INDEX映射
  - COUNTERS  保存不同资源的统计信息，以资源ID为KEY

## 核心流程

- enable_counter 系统启动后180或60s后执行自动执行，使能PORT、 QUEUE和PFCWD
- FLEX_COUNTER配置
  - 由不同模块在init或使能时进行配置
- FLEX_COUNTER统计
  - syncd通过订阅COUNTER_GROUP和FLEX_COUNTER创建相应的flexCounter线程
  - flexCounter线程定时调用SAI API获取和保存统计结果到COUNTERS
  - runPlugins 执行统计后的数据转存、比较等
