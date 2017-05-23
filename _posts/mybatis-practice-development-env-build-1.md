---
title: MyBatis实战-1-开发环境搭建
date: 2016-09-13 21:05:23
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/mybatis-plus-thumbnail.png
tags:
	- MyBatis
	- 实战
categories:
	- 开发技术
	- MyBatis
keywords:
	- MyBatis
	- 实战
---
## MyBatis简介
MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

## MyBatis快速上手
### 开发环境准备
	说明，本系列教程所有的代码均是在Intellij Idea 16.02版本的IDE上编写的。
1.项目结构

![项目结构](https://obrxbqjbi.qnssl.com/blog/image/MyBatis-1-1.png)

2.添加依赖

- mybatis-3.4.1.jar
- mysql-connector-java-6.0.3.jar
- hamcrest-core-1.3.jar
- junit-4.12.jar

### 查询数据库中的记录
1.创建数据库和数据表

- 创建数据表

``` sql
SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `users`
-- ----------------------------
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(45) NOT NULL,
  `pwd` varchar(45) NOT NULL,
  `age` int(11) NOT NULL ,
  `email` varchar(45) NOT NULL ,
  `tel` varchar(45) NOT NULL,
  `addr` varchar(45) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=580317 DEFAULT CHARSET=utf8;
```
- 向表中添加数据

``` sql
INSERT INTO `users` 
VALUES
('580314', 'Richard', '123456', '21', '1105351276@qq.com', '13627341394', 'ISS'), 
('580315', 'QinJiangbo', '123456', '22', '1105351276@qq.com', '13627341394', 'Hongshan Square'), 
('580316', 'Oliver', 'whuiss', '22', '2456281765@qq.com', '13627341394', 'ISS');
```

2.创建用户POJO类`User.java`

``` java
package com.qinjiangbo.pojo;

/**
 * Date: 9/13/16
 * Author: qinjiangbo@github.io
 */
public class User {

    // 实体类的属性名称和数据库中的字段名称要一一对应起来
    private long id;
    private String name;
    private String pwd;
    private int age;
    private String email;
    private String tel;
    private String addr;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getTel() {
        return tel;
    }

    public void setTel(String tel) {
        this.tel = tel;
    }

    public String getAddr() {
        return addr;
    }

    public void setAddr(String addr) {
        this.addr = addr;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", tel='" + tel + '\'' +
                ", addr='" + addr + '\'' +
                '}';
    }
}

```

3.添加MyBatis的配置文件`configure.xml`

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="Richard"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
    	 <!-- 注册Mapper映射XML文件 -->
        <mapper resource="com/qinjiangbo/dao/UserMapper.xml"></mapper>
    </mappers>
</configuration>
```
`NOTE` 上面是数据库相关的配置，各位读者可以根据自己的实际情况进行相应的配置。

4.编写Mapper映射XML文件

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.UserMapper">
    <select id="findUserById" parameterType="int"
            resultType="com.qinjiangbo.pojo.User">
        SELECT * FROM users WHERE  id = #{id}
    </select>
</mapper>
```
`NOTE` 编写了Mapper映射XML文件以后一定要向MyBatis配置文件注册这个Mapper文件，否则它没办法被识别！

5.编写测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/13/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest1 {

    @Test
    public void testFindUserById() {
        // 注意这个地方,不少人会直接写configure.xml, 这样会一直取不到值,从而报空指针,
        // 需要写全这个资源的全路径
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest1.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = factory.openSession();
        String statement = "com.qinjiangbo.dao.UserMapper.findUserById";
        User user = sqlSession.selectOne(statement, 580314);
        System.out.println(user);
    }
}

```

6.测试结果

![查询用户信息测试结果](https://obrxbqjbi.qnssl.com/blog/image/MyBatis-1-2.png)


