title: 使用ApplicationEvent和Listener快速实现业务解耦
author: jackcooper
date: 2018-03-19 17:11:21
tags:
---

`ApplicationEvent`以及`Listener`是Spring为我们提供的一个事件监听、订阅的实现，内部实现原理是观察者设计模式，设计初衷也是为了系统业务逻辑之间的解耦，提高可扩展性以及可维护性。

通过 ApplicationEvent 类和 ApplicationListener 接口来提供在 ApplicationContext 中处理事件。如果一个 bean 实现 ApplicationListener，那么每次 ApplicationEvent 被发布到 ApplicationContext 上，那个 bean 会被通知。

###Spring 提供了以下的标准事件：

   序号      |Spring 内置事件 & 描述
|:-:|:-|
|1|**ContextRefreshedEvent** ApplicationContext 被初始化或刷新时，该事件被发布。这也可以在 ConfigurableApplicationContext 接口中使用 refresh() 方法来发生。|
|2|**ContextStartedEvent**当使用 ConfigurableApplicationContext 接口中的 start() 方法启动 ApplicationContext 时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序。|
|3|**ContextStoppedEvent**当使用 ConfigurableApplicationContext 接口中的 stop() 方法停止 ApplicationContext 时，发布这个事件。你可以在接受到这个事件后做必要的清理的工作。|
|4|**ContextClosedEvent**当使用 ConfigurableApplicationContext 接口中的 close() 方法关闭 ApplicationContext 时，该事件被发布。一个已关闭的上下文到达生命周期末端；它不能被刷新或重启。|
|5|**RequestHandledEvent**这是一个 web-specific 事件，告诉所有 bean HTTP 请求已经被服务。|

一下通过一个实例说明：

### 项目的整体结构如下图所示

![image.png](http://upload-images.jianshu.io/upload_images/3710706-d44279ecacfd0dfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 1、构建项目
```xml
 <groupId>com.br</groupId>
    <artifactId>ListenerDemo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.16</version>
        </dependency>
        <!--test-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 2、创建Event事件
首先注意：listener都是围绕着event来挂起的。
```java
package com.br.event;

import com.br.bean.User;
import org.springframework.context.ApplicationEvent;

/**
 * @author jackcooper
 * @create 2018-02-27 13:54
 */
public class UserRegisterEvent extends ApplicationEvent {

    public User user;

    /**
     *
     * @param source 发生事件的对象
     * @param user 注册用户对象
     */
    public UserRegisterEvent(Object source,User user) {
        super(source);
        this.user = user;
    }

    
    
    
    
    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
}


```

### 3、事件发布
此部分主要在业务逻辑模块触发，主要用到ApplicationContext类以及它的publishEvent(ApplicationEvent t)方法。

```java
package com.br.service;

import com.br.bean.User;
import com.br.event.UserRegisterEvent;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Service;

/**
 * @author jackcooper
 * @create 2018-02-27 14:03
 */
@Service
public class UserService {

    @Autowired
    private ApplicationContext applicationContext;

    public void register(User user){
        //省略业务逻辑
        //。。。。
        //发布UserRegisterEvent事件
        applicationContext.publishEvent(new UserRegisterEvent(this,user));
    }
  
}
```

### 4、创建Event事件监听

#### 第一种方式：基于EventListener注解实现
> 注解方式比较简单，并不需要实现任何接口
```java
package com.br.listener;

import com.br.bean.User;
import com.br.event.UserRegisterEvent;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

/**
 * @author jackcooper
 * @create 2018-02-27 14:17
 * @deprecated 方法一：注解方式比较简单，并不需要实现任何接口   排序随机
 */
@Component
public class AnnotationRegisterListener {
    /**
     * 注册监听实现方法
     * @param userRegisterEvent 用户注册事件
     */
    @EventListener
    public void sendEmail(UserRegisterEvent userRegisterEvent)
    {
        //获取注册用户对象
        User user = userRegisterEvent.getUser();

        //todo

        //输出注册用户信息
        System.out.println("发送邮件，用户名："+user.getName()+"，邮箱："+user.getEmail());
    }

    /**
     * 注册监听实现方法
     * @param userRegisterEvent 用户注册事件
     */
    @EventListener
    public void register(UserRegisterEvent userRegisterEvent)
    {
        //获取注册用户对象
        User user = userRegisterEvent.getUser();

        //todo

        //输出注册用户信息
        System.out.println("1注册信息，用户名："+user.getName()+"，邮箱："+user.getEmail());
    }

}
```

#### 第二种方式：实现 ApplicationListener接口
> 这种方式也是Spring之前比较常用的监听事件方式，在实现ApplicationListener接口时需要将监听事件作为泛型传递
```java
package com.br.listener;

import com.br.bean.User;
import com.br.event.UserRegisterEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

/**
 * @author jackcooper
 * @create 2018-02-27 14:29
 * @deprecated ApplicationListener实现监听：这种方式也是Spring之前比较常用的监听事件方式，在实现ApplicationListener接口时需要将监听事件作为泛型传递，监听实现代码如下所示
 */
@Component
public class RegisterListener implements ApplicationListener<UserRegisterEvent> {

    /**
     * 实现监听
     * @param userRegisterEvent
     */
    @Override
    public void onApplicationEvent(UserRegisterEvent userRegisterEvent) {
        //获取注册用户对象
        User user = userRegisterEvent.getUser();

        //../省略逻辑

        //输出注册用户信息
        System.out.println("2注册信息，用户名："+user.getName()+"，密码："+user.getEmail());
    }
}

```

#### 第三种方式：实现SmartApplicationListener接口

```java

package com.br.listener;

import com.br.bean.User;
import com.br.event.UserRegisterEvent;
import com.br.service.UserService;
import org.springframework.context.ApplicationEvent;
import org.springframework.context.event.SmartApplicationListener;
import org.springframework.stereotype.Component;

/**
 * @author 10400
 * @create 2018-02-27 15:03
 * @deprecated 有序监听
 */
@Component
public class SmartRegisterListener implements SmartApplicationListener {
    @Override
    public boolean supportsEventType(Class<? extends ApplicationEvent> aClass) {
        //只有UserRegisterEvent监听类型才会执行下面逻辑
        return aClass == UserRegisterEvent.class;
    }

    @Override
    public boolean supportsSourceType(Class<?> aClass) {
        //只有在UserService内发布的UserRegisterEvent事件时才会执行下面逻辑
        return aClass == UserService.class;
    }

    @Override
    public void onApplicationEvent(ApplicationEvent applicationEvent) {
        //转换事件类型
        UserRegisterEvent userRegisterEvent = (UserRegisterEvent) applicationEvent;
        //获取注册用户对象信息
        User user = userRegisterEvent.getUser();
        //.../完成注册业务逻辑

        System.out.println("SmartRegisterListener" + user.getName());

    }

    /**
     * return 的数值越小证明优先级越高，执行顺序越靠前。
     * @return
     */
    @Override
    public int getOrder() {
        return 10;
    }
}

```

> SmartApplicationListener接口继承了全局监听ApplicationListener，并且泛型对象使用的ApplicationEvent来作为全局监听，可以理解为使用SmartApplicationListener作为监听父接口的实现，监听所有事件发布。

> 可以看到除了上面的方法，还提供了一个getOrder方法，这个方法就可以解决执行监听的顺序问题，**return的数值越小证明优先级越高，执行顺序越靠前**。

#### 第四种方式：使用@Async实现异步监听
@Aysnc其实是Spring内的一个组件，可以完成对类内单个或者多个方法实现异步调用，这样可以大大的节省等待耗时。内部实现机制是线程池任务ThreadPoolTaskExecutor，通过线程池来对配置@Async的方法或者类做出执行动作。

* 线程任务池配置
我们创建一个ListenerAsyncConfiguration，并且使用@EnableAsync注解开启支持异步处理，具体代码如下所示：

```java
@Configuration
@EnableAsync
public class ListenerAsyncConfiguration implements AsyncConfigurer
{
    /**
     * 获取异步线程池执行对象
     * @return
     */
    @Override
    public Executor getAsyncExecutor() {
        //使用Spring内置线程池任务对象
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        //设置线程池参数
        taskExecutor.setCorePoolSize(5);
        taskExecutor.setMaxPoolSize(10);
        taskExecutor.setQueueCapacity(25);
        taskExecutor.initialize();
        return taskExecutor;
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return null;
    }
}

```

我们自定义的监听异步配置类实现了AsyncConfigurer接口并且实现内getAsyncExecutor方法以提供线程任务池对象的获取。
我们只需要在异步方法上添加@Async注解就可以实现方法的异步调用，为了证明这一点，我们在发送邮件onApplicationEvent方法内添加线程阻塞3秒，修改后的代码如下所示：

```java
/**
     * supportsEventType & supportsSourceType 两个方法返回true时调用该方法执行业务逻辑
     * @param applicationEvent 具体监听实例，这里是UserRegisterEvent
     */
    @Override
    @Async
    public void onApplicationEvent(ApplicationEvent applicationEvent) {
        try {
            Thread.sleep(3000);//静静的沉睡3秒钟
        }catch (Exception e)
        {
            e.printStackTrace();
        }
        //转换事件类型
        UserRegisterEvent userRegisterEvent = (UserRegisterEvent) applicationEvent;
        //获取注册用户对象信息
        UserBean user = userRegisterEvent.getUser();
        System.out.println("用户："+user.getName()+"，注册成功，发送邮件通知。");
    }
```

---
### 文章参考
https://www.jianshu.com/p/4359dd4b36a6