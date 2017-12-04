---
title: Drools与SpringMVC整合
date: 2017-11-01 16:22:49
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/drools-springmvc.png
tags:
	- 规则引擎
	- Drools
	- SpringMVC
categories:
	- 开发技术
	- Java
keywords:
	- 规则引擎
	- Drools
	- SpringMVC
---
本文主要介绍Drools6.4.Final版本与SpringMVC的整合。网上现有的教程大多数不能完整运行，本文将会提供具体的操作过程以及相应的注意事项，保证Demo代码是可以跑通的。

## 开发环境
+ macOS High Sierra
+ Intellij IDEA 2017.2
+ Drools 6.4.0.Final
+ SpringMVC 4.3.12.RELEASE
+ JDK 1.8.0_144

## 搭建Maven项目
关于具体如何创建一个Maven的Webapp项目我就不赘述了，其实在IDEA中可以一步一步选择的，下面贴出本项目的`pom.xml`文件。

### pom.xml
``` xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.qinjiangbo</groupId>
    <artifactId>DroolsSpring</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>Drools SpringMVC</name>
    <url>http://maven.apache.org</url>

    <!-- 各个框架的版本 -->
    <properties>
        <spring.version>4.3.12.RELEASE</spring.version>
        <drools.version>6.4.0.Final</drools.version>
        <slf4j.version>1.7.25</slf4j.version>
        <fastjson.version>1.2.39</fastjson.version>
    </properties>

    <dependencies>
        <!-- Spring框架 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- Drools框架 -->
        <dependency>
            <groupId>org.kie</groupId>
            <artifactId>kie-api</artifactId>
            <version>${drools.version}</version>
        </dependency>
        <dependency>
            <groupId>org.drools</groupId>
            <artifactId>drools-core</artifactId>
            <version>${drools.version}</version>
        </dependency>
        <dependency>
            <groupId>org.drools</groupId>
            <artifactId>drools-compiler</artifactId>
            <version>${drools.version}</version>
        </dependency>
        <dependency>
            <groupId>org.kie</groupId>
            <artifactId>kie-spring</artifactId>
            <version>${drools.version}</version>
        </dependency>
        <!-- Tomcat相关包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!-- JUnit框架 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <!-- Slf4j日志框架 -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <!-- Fastjson框架 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>${fastjson.version}</version>
        </dependency>

    </dependencies>

    <build>
        <finalName>DroolsSpring</finalName>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
接着在`web.xml`中配置相关的SpringMVC环境。

### web.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         version="2.5" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
<display-name>Archetype Created Web Application</display-name>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:springContext.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>springServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:springContext.xml</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>springServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

在介绍`springContext.xml`文件之前，有必要贴出一下项目的结构，以便于大家有一个更明确的认知。

![项目主体结构](https://obrxbqjbi.qnssl.com/blog/image/drools-springmvc-project.png)

好，现在分别介绍`springContext.xml`，`springDrools.xml`以及`springMVC.xml`三个文件。

### springContext.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置包扫描路径 -->
    <context:component-scan base-package="com.qinjiangbo"/>

    <!-- 导入其他配置 -->
    <import resource="springDrools.xml"/>
    <import resource="springMVC.xml"/>

</beans>
```

### springDrools.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:kie="http://drools.org/schema/kie-spring"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://drools.org/schema/kie-spring http://drools.org/schema/kie-spring.xsd">
    <!-- Drools配置 -->
    <bean id="kiePostProcessor"
          class="org.kie.spring.annotations.KModuleAnnotationPostProcessor"/>
    <kie:kmodule id="kModule">
        <kie:kbase name="kBase" packages="rules">
            <kie:ksession name="kSession" type="stateful"/>
        </kie:kbase>
    </kie:kmodule>
</beans>
```

### springMVC.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 开启MVC注解 -->
    <mvc:annotation-driven/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

## Fact模型
在Drools中我们使用的Model被称之为事实(Fact)模型，本例中的事实模型是`Message.java`。

``` java
package com.qinjiangbo.model;

public class Message {

    public static final int DEAD = 0;
    public static final int ALIVE = 1;

    private String message;
    private int status;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }

}
```

## rules文件
我们来编写一下相关的规则文件，一般是以`.drl`结尾。

``` java
package rules;
import com.qinjiangbo.model.*;

rule "rule1"
    when
        /**
        * 当该实体的status属性为DEAD的时候，将该实体赋值给m，
        * 实体的message属性赋值给printMsg
        */
        m:Message(status == Message.DEAD, printMsg: message);
    then
        // 系统输出和重新设置实体属性
        System.out.println(printMsg);
        m.setMessage("I'm alive~");
        m.setStatus(Message.ALIVE);

        // 更新实体，触发规则
        update(m);
end

rule "rule2"
    when
        Message(status == Message.ALIVE, printMsg: message);
    then
        System.out.println(printMsg);
end
```

## Service类和Controller类
在Service类中调用规则引擎相关的逻辑，在Controller中进行Web页面的交互。

``` java
package com.qinjiangbo.service;

import com.alibaba.fastjson.JSON;
import com.qinjiangbo.model.Message;
import org.kie.api.cdi.KSession;
import org.kie.api.runtime.KieSession;
import org.springframework.stereotype.Service;

@Service
public class MessageService {

    @KSession("kSession")
    private KieSession kieSession;

    public String updateMessage() {
        Message message = new Message();
        message.setMessage("Left 4 Dead 2");
        message.setStatus(Message.DEAD);
        kieSession.insert(message);
        kieSession.fireAllRules();
        return JSON.toJSONString(message);
    }

}
```
`注意`上面的`@KSession("kSession")`注解在使用的时候要在`springDrools.xml`文件中声明`kiePostProcessor`为注解类型的`org.kie.spring.annotations.KModuleAnnotationPostProcessor`，否则会失效的。另外，这里使用的`KieSession`其实就是指代的`StatefulKieSession`类，不要使用`KieSession`去声明一个`StatelessKieSession`类的对象。这里要在`springDrools.xml`文件中把`<kie:ksession name="kSession" type="stateful"/>`类型声明为`stateful`。

``` java
package com.qinjiangbo.controller;

import com.qinjiangbo.service.MessageService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
@RequestMapping("/")
public class MessageController {

    @Autowired
    private MessageService messageService;

    @RequestMapping("/")
    public String index() {
        return "index";
    }

    @RequestMapping("/update")
    public ModelAndView update() {
        ModelAndView modelAndView = new ModelAndView("success");
        String message = messageService.updateMessage();
        modelAndView.addObject("message", message);
        return modelAndView;
    }
}
```

## 测试结果
在浏览器中输入`http://localhost:8080/update`，就可以看到如下页面：
![](https://obrxbqjbi.qnssl.com/blog/image/project-result.png)
Bingo，那就证明整合成功啦！
