# spring-boot-quartz

基于spring-boot 2.1.6 + quartz 的CRUD任务管理系统，适用于中小项目。

## 开发环境

JDK1.8、Maven、Eclipse

## 技术栈

SpringBoot 2.1.6、thymeleaf 3.0.9、quartz 2.3.0、iview、vue、layer、AdminLTE、bootstrap

## 启动说明
- 项目使用的数据库为MySql，选择resources/sql中的tables_mysql_innodb.sql文件初始化数据库信息。
- 在resources/application.properties 以及quartz.properties文件中替换为自己的数据源。
- 运行Application main方法启动项目，项目启动会自动创建一个测试任务 见：com.itstyle.quartz.config.TaskRunner.java。
- 项目访问地址：http://localhost:8080/quartz


## 版本区别(spring-boot 1.x and 2.x)

这里只是针对这两个项目异同做比较，当然spring-boot 2.x版本升级还有不少需要注意的地方。

项目名称配置：
```
# spring boot 1.x
server.context-path=/quartz
# spring boot 2.x
server.servlet.context-path=/quartz
```
thymeleaf配置：
```
#spring boot 1.x
spring.thymeleaf.mode=LEGACYHTML5
#spring boot 2.x
spring.thymeleaf.mode=HTML
```
Hibernate配置：
```
# spring boot 2.x JPA 依赖  Hibernate 5
# Hibernate 4 naming strategy fully qualified name. Not supported with Hibernate 5.
spring.jpa.hibernate.naming.strategy = org.hibernate.cfg.ImprovedNamingStrategy
# stripped before adding them to the entity manager)
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
# Hibernate 5
spring.jpa.hibernate.naming.implicit-strategy=org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyJpaImpl
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```
quartz配置：
```
# spring boot 2.x 已集成Quartz，无需自己配置
spring.quartz.job-store-type=jdbc
spring.quartz.properties.org.quartz.scheduler.instanceName=clusteredScheduler
spring.quartz.properties.org.quartz.scheduler.instanceId=AUTO
spring.quartz.properties.org.quartz.jobStore.class=org.quartz.impl.jdbcjobstore.JobStoreTX
spring.quartz.properties.org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.StdJDBCDelegate
spring.quartz.properties.org.quartz.jobStore.tablePrefix=QRTZ_
spring.quartz.properties.org.quartz.jobStore.isClustered=true
spring.quartz.properties.org.quartz.jobStore.clusterCheckinInterval=10000
spring.quartz.properties.org.quartz.jobStore.useProperties=false
spring.quartz.properties.org.quartz.threadPool.class=org.quartz.simpl.SimpleThreadPool
spring.quartz.properties.org.quartz.threadPool.threadCount=10
spring.quartz.properties.org.quartz.threadPool.threadPriority=5
spring.quartz.properties.org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread=true
```
默认首页配置：
@Configuration
public class MyAdapter extends WebMvcConfigurerAdapter{
    @Override
    public void addViewControllers( ViewControllerRegistry registry ) {
        registry.addViewController( "/" ).setViewName( "forward:/login.shtml" );
        registry.setOrder( Ordered.HIGHEST_PRECEDENCE );
        super.addViewControllers( registry );
    } 
}

@Configuration
public class MyAdapter implements WebMvcConfigurer{
    @Override
    public void addViewControllers( ViewControllerRegistry registry ) {
        registry.addViewController( "/" ).setViewName( "forward:/login.shtml" );
        registry.setOrder( Ordered.HIGHEST_PRECEDENCE );
    } 
}
```



## 集群测试

打开quartz集群配置：
```
# 打开集群配置
spring.quartz.properties.org.quartz.jobStore.isClustered = true
# 设置集群检查间隔20s
spring.quartz.properties.org.quartz.jobStore.clusterCheckinInterval = 2000  
```
本地跑两个项目，分别设置不同的端口8081和8082，启动成功以后，会发现只有一个任务在跑，然后杀掉在跑的任务，你会发现另一个项目会检测到集群中的一个任务挂了，然后接管任务。
```
2018-04-12 09:00:01.792  INFO 7488 --- [_ClusterManager] o.s.s.quartz.LocalDataSourceJobStore     : ClusterManager: detected 1 failed or restarted instances.
2018-04-12 09:00:01.793  INFO 7488 --- [_ClusterManager] o.s.s.quartz.LocalDataSourceJobStore     : ClusterManager: Scanning for instance "itstyle-PC1523496348539"'s failed in-progress jobs.
2018-04-12 09:00:01.839  INFO 7488 --- [_ClusterManager] o.s.s.quartz.LocalDataSourceJobStore     : ClusterManager: ......Freed 1 acquired trigger(s).
大吉大利、今晚吃鸡-01
大吉大利、今晚吃鸡-01

```

## 扩展

Quartz API核心接口有：

- Scheduler – 与scheduler交互的主要API；
- Job – 你通过scheduler执行任务，你的任务类需要实现的接口；
- JobDetail – 定义Job的实例；
- Trigger – 触发Job的执行；
- JobBuilder – 定义和创建JobDetail实例的接口;
- TriggerBuilder – 定义和创建Trigger实例的接口；
