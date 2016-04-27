title: "Quartz.NET中文开发指南(二):Jobs And Triggers"
date: 2016-04-16 18:35:33
categories: Quartz.NET 
description: "Quartz.NET中文开发指南 Lesson 2:Jobs And Triggers"
tags:
- Quartz.NET
---
## Lesson 2: Jobs And Triggers

### Quartz API
Quartz 主要的接口和类如下：
- IScheduler-操作scheduler主要的接口
- IJob-组件需要实现该接口才能被scheduler执行
- IJobDetail-用来定义Jobs的实例
- ITrigger-定义Job何时执行的组件
- JobBuilder-用来创建JobDetail实例
- TriggerBuilder-用来创建Trigger实例

> 为了本教程的可读性 IScheduler and Scheduler, IJob and Job, IJobDetail and JobDetail, ITrigger and Trigger.这些单词是可以相互替代的。  

Scheduler的生命周期是从在SchedulerFactory创建到执行Shutdown()方法，Scheduler一旦被创建就可添加、删除、获取Job和Trigger，
也可以执行其他调度操作（例如：暂停Trigger），但是Scheduler在调用Start()方法前不会触发Trigger和执行Job，如Lesson1所示。  

Quartz提供了“builder”类来声明领域特定语言（DSL,有时也称为"fluent interface"），在这里我们再次给出上一节例子的一小部分：
```csharp
  // 定义一个Job 绑定HelloJob
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
上述代码是创建Job的方式是使用JobBuilder以"fluent interface"形式来创建IJobDetail，
同样，创建trigger也是使用TriggerBuilder以"fluent interface"形式 和trigger类型的扩展方法来创建。
调度扩展方法有：
- WithCalendarIntervalSchedule
- WithCronSchedule
- WithDailyTimeIntervalSchedule
- WithSimpleSchedule  

DateBuilder类包含多种易于创建具体时间点DateTimeOffset实例的方法（例如获取下一个偶数小时，具体例子就是现在为 9:43:27，获取的结果是 10:00:00）。

### Jobs and Triggers

Job类就是IJob接口的实现，它只有一个简单的方法。
IJob Interface
```csharp
namespace Quartz
    {
        public interface IJob
        {
            void Execute(JobExecutionContext context);
        }
    }
```
当Job的trigger（触发器）被触发，Execute方法就会被scheduler的一个工作线程调用。传入这个方法的JobExecutionContext实例提供了Job的运行时环境：执行Job的Scheduler，触发Job的Trigger，Job的JobDetail对象和一些其他属性。  
JobDetail对象是在Job添加到 scheduler时，被 Quartz.NET client（你的程序）创建的。它包含了多个Job的属性设置，例如一个用来储存Job实例状态信息的JobDataMap。这是Job实例最基本的定义，我们静载下一节详细讨论。 
 

Trigger对象用来触发执行Job.当你想要调度一个job时,你需要一个tigger的实例然后调整他就可以提供你想要的调度方式。触发器也有一个与之相关的JobDataMap，用来传递参数给触发的Job。Quartz 拥有不同的几个触发器类型，但是最常见的两个类型是SimpleTrigger (接口是 ISimpleTrigger) 、 CronTrigger (接口是 ICronTrigger)。   


SimpleTrigger常常被用于执行"一次性"的操作，或者你需要执行一个Job在特定的时间，执行N次，每次执行延迟为T。CronTrigger常常用于执行基于日程表的任务，例如："每周五中午"或者"每月第十天的10:15"。   


为什么分开Job和Trigger?许多任务调度框架并没有把job和trigger分开。许多定时调度程序只是将“job”简单地定义为一个带有一些小任务标识的执行时间。还有一些框架定义Job更像 Quartz Job和Trigger的结合。
当我们开发Quartz时，我们决定将调度程序和在其上面执行的Job分离开来，这样定义在我们看来是十分有利的。    


例如，Job 可以独立于trigger单独的创建和存储，同一个Job可以与多个不同的Trigger绑定。这种松耦合的另一个好处是当Job的Trigger执行完后，Job还会存在scheduler中，当下次执行时，不需要重新创建Job。这种设计还允许在不重新定义一个Job的情况下，更改或者替换Trigger。  

### Identities
Jobs和Triggers当注册到Quartz的scheduler时，会被给予一个标识key。Jobs和Triggers的Key(JobKey and TriggerKey)允许他们通过分组的方式组织job和trigger。job和trigger的Key在分组中是唯一的，换句话说这个Key就是分组和名字组合成的。你现在对Job和Trigger也有了一定的了解，在 Lesson 3: More About Jobs & JobDetails 和 Lesson 4: More About Triggers，你能学到更多。