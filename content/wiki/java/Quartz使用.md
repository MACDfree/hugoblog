---
title: "Quartz使用"
date: 2017-10-15T21:57:39+08:00
draft: false
categories: ["Java"]
tags: ["quartz"]
---

Quartz的主要概念是Scheduler、Job、JobDetail和Trigger，其中Scheduler（调度者）是主体；JobDetail和Trigger用于定义任务的相关信息，如：需要执行的任务类，任务执行的时间点等；Job为实际需要执行的任务。
<!--more-->

需要的Maven依赖：

``` xml
<dependency>
  <groupId>org.quartz-scheduler</groupId>
  <artifactId>quartz</artifactId>
  <version>2.2.1</version>
</dependency>
```

如果只需要使用Quartz的基本功能，只需要添加以上依赖
测试代码为

``` java
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;

import static org.quartz.JobBuilder.*;
import static org.quartz.TriggerBuilder.*;
import static org.quartz.SimpleScheduleBuilder.*;

public class QuartzTest {
    public static void main(String[] args) throws InterruptedException {
        try {
            Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
            scheduler.start();

            JobDetail job = newJob(HelloJob.class).withIdentity("job1", "group1").build();
            Trigger trigger = newTrigger().withIdentity("trigger1", "group1").startNow().withSchedule(simpleSchedule()
                    .withIntervalInSeconds(4).repeatForever()).build();

            scheduler.scheduleJob(job, trigger);
            System.out.println("start time:" + System.currentTimeMillis());

            Thread.sleep(20000);
            scheduler.shutdown();
        } catch (SchedulerException se) {
            se.printStackTrace();
        }
    }
}
```

需要注意的是需要使用静态导入，实现类似领域特定语言的效果。

如果需要使用配置文件配置job，需要添加依赖

``` xml
<dependency>
  <groupId>org.quartz-scheduler</groupId>
  <artifactId>quartz-jobs</artifactId>
  <version>2.2.1</version>
</dependency>
<dependency>
  <groupId>javax.transaction</groupId>
  <artifactId>jta</artifactId>
  <version>1.1</version>
</dependency>
```

同时添加quartz.properties配置文件，配置中指定配置job的文件名为jobs.xml

``` ini
org.quartz.scheduler.instanceName = MyScheduler
org.quartz.threadPool.threadCount = 3
org.quartz.jobStore.class = org.quartz.simpl.RAMJobStore

#===================================================
# Configure the Job Initialization Plugin
#===================================================
org.quartz.plugin.jobInitializer.class = org.quartz.plugins.xml.XMLSchedulingDataProcessorPlugin
org.quartz.plugin.jobInitializer.fileNames = jobs.xml
org.quartz.plugin.jobInitializer.failOnFileNotFound = true
org.quartz.plugin.jobInitializer.scanInterval = 10
org.quartz.plugin.jobInitializer.wrapInUserTransaction = false
```

配置的jobs.xml文件示例如下

``` xml
<?xml version='1.0' encoding='utf-8'?>
<job-scheduling-data xmlns="http://www.quartz-scheduler.org/xml/JobSchedulingData"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     xsi:schemaLocation="http://www.quartz-scheduler.org/xml/JobSchedulingData http://www.quartz-scheduler.org/xml/job_scheduling_data_1_8.xsd"
                     version="1.8">
  <schedule>
    <job>
      <name>job1</name>
      <group>group1</group>

      <description>Hello Job</description>
      <job-class>HelloJob</job-class>
    </job>

    <trigger>
      <cron>
        <name>trigger1</name>
        <group>group1</group>
        <job-name>job1</job-name>
        <job-group>group1</job-group>
        <cron-expression>0/5 * * * * ?</cron-expression>
      </cron>
    </trigger>
  </schedule>
</job-scheduling-data>
```

相应测试代码如下

``` java
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.impl.StdSchedulerFactory;

public class QuartzTest {
    public static void main(String[] args) throws InterruptedException {
        try {
            Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
            scheduler.start();

            Thread.sleep(20000);
            scheduler.shutdown();
        } catch (SchedulerException se) {
            se.printStackTrace();
        }
    }
}
```
