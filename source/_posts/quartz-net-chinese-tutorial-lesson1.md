title: "Quartz.NET中文开发指南(一):快速使用Quartz.NET"
date: 2016-04-13 17:38:40
categories: Quartz.NET 
description: "Quartz.NET中文开发指南 Lesson 1:快速使用Quartz.NET"
tags:
- Quartz.NET
---
Quartz.NET中文开发指南 Lesson 1:快速使用Quartz.NET
<!-- more -->
## Lesson 1: 使用Quartz

在你使用scheduler之前，你应该使用ISchedulerFactory的实例来实例化一个scheduler。一旦scheduler被实例化，它可以被启动（started），或者置于准备（stand-by）状态，或者关闭（shutdown）。  
注意的是一旦scheduler 被关闭，他将不能被重启，除非重新实例化。当scheduler在启动状态，或者不是暂停（paused）状态时，触发器（Triggers）才能被触发（Jobs也将被执行）。  
下面的代码段告诉我们如何实例化和启动scheduler，并且怎样调度执行Job：
```csharp
  // 实例化scheduler工厂类
  ISchedulerFactory schedFact = new StdSchedulerFactory();
  // 由scheduler工厂类创建一个Scheduler
  IScheduler sched = schedFact.GetScheduler();
  sched.Start();
  // 定义一个Job
  IJobDetail job = JobBuilder.Create<HelloJob>()
       .WithIdentity("myJob", "group1")
       .Build();
  // 定义一个立即启动的Trigger，启动后每40秒触发一次
  ITrigger trigger = TriggerBuilder.Create()
        .WithIdentity("myTrigger", "group1")
        .StartNow()
        .WithSimpleSchedule(x => x
             .WithIntervalInSeconds(40)
             .RepeatForever())
         .Build();
  sched.ScheduleJob(job, trigger);
```
你可以看到，使用Quartz.NET是十分简单的，在看完Lesson2 我们将对Jobs and Triggers进行概述，你可能更好的理解本节课的例子。
