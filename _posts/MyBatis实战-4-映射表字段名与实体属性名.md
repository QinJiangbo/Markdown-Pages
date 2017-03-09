---
title: MyBatis实战-4-映射表字段名与实体属性名
date: 2016-09-16 16:07:14
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
## 映射表字段名与实体属性名并自定义返回类型
很多时候我们需要自定义一些字段的名称或者是结果的返回类型，这个时候就需要将数据表中的字段名称与实体的属性名称相对应，并且添加自定义的结果返回类型。

### 添加实体对象
添加的对象为Student学生对象，属性名称有学生编号，学生姓名，学生密码，以及学生年龄。

``` java
package com.qinjiangbo.pojo;

/**
 * Date: 9/16/16
 * Author: qinjiangbo@github.io
 */
public class Student {
    private int id;
    private String name;
    private String password;
    private int age;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                '}';
    }

}

```

### 添加对应表数据
在数据库中新建一张表，并向其中插入测试数据。

``` sql
-- ----------------------------
--  Table structure for `student`
-- ----------------------------
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `s_id` int(11) NOT NULL AUTO_INCREMENT,
  `s_name` varchar(45) NOT NULL,
  `s_password` varchar(45) NOT NULL,
  `s_age` int(11) NOT NULL,
  `c_id` int(11) NOT NULL,
  PRIMARY KEY (`s_id`),
  KEY `fk_cid` (`c_id`),
  CONSTRAINT `fk_cid` FOREIGN KEY (`c_id`) REFERENCES `class` (`c_id`)
) ENGINE=InnoDB AUTO_INCREMENT=580320 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `student`
-- ----------------------------
BEGIN;
INSERT INTO `student` VALUES 
('580314', 'QinJiangbo', '234562', '21', '3'), 
('580315', 'YuYing', '123245', '21', '3'), 
('580316', 'LiuBiqin', '123456', '22', '2'), 
('580317', 'ZhouChongyi', '222333', '21', '2'), 
('580318', 'XuYouying', 'whuiss', '22', '1'), 
('580319', 'YeZetao', 'isswhu', '21', '1');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

### 映射表字段名称与实体属性名称
新建一个映射XML文件StudentMapper.xml和接口StudentMapper.java，在MyBatis配置文件configure.xml中注册这个接口。

``` xml
<mappers>
    <mapper resource="com/qinjiangbo/dao/UserMapper.xml"/>
    <mapper class="com.qinjiangbo.dao.UserMapperC"/>
    <mapper class="com.qinjiangbo.dao.StudentMapper"/>
</mappers>
```

并在其中加入一个ResultMap，关于ResultMap的定义可以在我的`MyBatis框架`系列教程里面学习。

**StudentMapper.xml**

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.StudentMapper">

    <resultMap id="StudentInfoMap" type="com.qinjiangbo.pojo.Student">
        <id column="s_id" property="id"/>
        <result column="s_name" property="name"/>
        <result column="s_password" property="password"/>
        <result column="s_age" property="age"/>
    </resultMap>

    <resultMap id="StudentBasicInfoMap" type="java.util.Map">
        <result column="s_name" property="name"/>
        <result column="s_password" property="password"/>
    </resultMap>

    <select id="findStudentInfo" resultMap="StudentInfoMap" parameterType="int">
        SELECT * FROM student WHERE s_id = #{id};
    </select>

    <select id="findStudentBasicInfo" resultMap="StudentBasicInfoMap" parameterType="int">
        SELECT s_name, s_password FROM student WHERE s_id = #{id};
    </select>
</mapper>
```

**StudentMapper.java**

``` java
package com.qinjiangbo.dao;

import com.qinjiangbo.pojo.Student;

import java.util.Map;

/**
 * Date: 9/16/16
 * Author: qinjiangbo@github.io
 */
public interface StudentMapper {
    /**
     * 查找学生信息
     *
     * @param id
     * @return
     */
    public Student findStudentInfo(Integer id);

    /**
     * 查找学生基本信息
     *
     * @param id
     * @return
     */
    public Map findStudentBasicInfo(Integer id);
}

```

### 测试实例

1.获取学生信息

``` java
@Test
public void testFindStudentInfo() {
    StudentMapper studentMapper = sqlSession.getMapper(StudentMapper.class);
    Student student = studentMapper.findStudentInfo(580314);
    System.out.println(student);
}
```

结果:

`Student{id=580314, name='QinJiangbo', password='234562', age=21}`

2.获取学生基本信息

``` java
@Test
public void testFindStudentBasicInfo() {
    StudentMapper studentMapper = sqlSession.getMapper(StudentMapper.class);
    Map map = studentMapper.findStudentBasicInfo(580314);
    System.out.println(map.get("name") + ":" + map.get("password"));
}
```

结果：

`QinJiangbo:234562`

以上就是将表中的字段名和实体的属性名对应起来并且自定义返回类型的使用方式。以上代码均通过测试。