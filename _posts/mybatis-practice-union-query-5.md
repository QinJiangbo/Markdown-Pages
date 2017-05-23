---
title: MyBatis实战-5-多表联合查询
date: 2016-09-21 17:52:03
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
## 多表联合查询
在MyBatis中，时常会需要联合多张表进行联合查询，那么如果处理多张表联合查询的结果呢？下面将会讲述如何在MyBatis中进行多张表的联合查询。

### 一对一查询

1.添加实体类Classes, Teacher

Classes.java

``` java
package com.qinjiangbo.pojo;

import java.util.List;

/**
 * Date: 9/16/16
 * Author: qinjiangbo@github.io
 */
public class Classes {
    private int id;
    private String name;
    private Teacher teacher;
    private List<Student> students;

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

    public Teacher getTeacher() {
        return teacher;
    }

    public void setTeacher(Teacher teacher) {
        this.teacher = teacher;
    }

    public List<Student> getStudents() {
        return students;
    }

    public void setStudents(List<Student> students) {
        this.students = students;
    }

    @Override
    public String toString() {
        return "Classes{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", teacher=" + teacher +
                ", students=" + students +
                '}';
    }

}

```

Teacher.java

``` java
package com.qinjiangbo.pojo;

/**
 * Date: 9/16/16
 * Author: qinjiangbo@github.io
 */
public class Teacher {
    private int id;
    private String name;
    private String office;
    private String gender;

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

    public String getOffice() {
        return office;
    }

    public void setOffice(String office) {
        this.office = office;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", office='" + office + '\'' +
                ", gender='" + gender + '\'' +
                '}';
    }

}

```

2.在数据库中插入实体类的数据

``` sql
SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `class`
-- ----------------------------
DROP TABLE IF EXISTS `class`;
CREATE TABLE `class` (
  `c_id` int(11) NOT NULL AUTO_INCREMENT,
  `c_name` varchar(45) NOT NULL,
  `t_id` int(11) NOT NULL,
  PRIMARY KEY (`c_id`),
  KEY `fk_tid` (`t_id`),
  CONSTRAINT `fk_tid` FOREIGN KEY (`t_id`) REFERENCES `teacher` (`t_id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `class`
-- ----------------------------
BEGIN;
INSERT INTO `class` VALUES ('1', 'International Class', '1001'), ('2', 'International Class', '1002'), ('3', 'International Class', '1003');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

``` sql
SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `teacher`
-- ----------------------------
DROP TABLE IF EXISTS `teacher`;
CREATE TABLE `teacher` (
  `t_id` int(11) NOT NULL AUTO_INCREMENT,
  `t_name` varchar(45) NOT NULL,
  `t_office` varchar(45) NOT NULL,
  `t_gender` varchar(45) NOT NULL DEFAULT 'female',
  PRIMARY KEY (`t_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1008 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `teacher`
-- ----------------------------
BEGIN;
INSERT INTO `teacher` VALUES ('1001', 'Loris', 'B4056', 'Male'), ('1002', 'Lily', 'B3032', 'Female'), ('1003', 'Sarah', 'B4012', 'Female'), ('1004', 'Sony', 'B4013', 'Male'), ('1005', 'Sarah', 'B4015', 'Female'), ('1006', 'Toffy', 'B1012', 'Male'), ('1007', 'Jeserf', 'B2018', 'Male');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

3.创建Mapper映射文件

ClassesMapper.xml

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.ClassesMapper">

    <!-- 根据班级编号查找班级、教师信息 -->
    <!--
        以下为第一种方式,执行一次查询
        SELECT * FROM class c, teacher t WHERE c.c_id = #{id} AND c.t_id = t.t_id;
    -->
    <resultMap id="classInfoMap" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association property="teacher" javaType="com.qinjiangbo.pojo.Teacher">
            <id column="t_id" property="id"/>
            <result column="t_name" property="name"/>
            <result column="t_office" property="office"/>
            <result column="t_gender" property="gender"/>
        </association>
    </resultMap>

    <select id="findClassById" parameterType="int" resultMap="classInfoMap">
        SELECT * FROM class c, teacher t WHERE c.c_id = #{id} AND c.t_id = t.t_id;
    </select>

    <!--
        以下为第二种方式,执行两次查询
        1. SELECT * FROM class WHERE c_id = #{id};
        2. SELECT * FROM teacher WHERE t_id = #{id};
        第二次查询依赖于第一次查询所得的结果
    -->
    <resultMap id="classInfoMap2" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association column="t_id" property="teacher" select="findTeacherById"/>
    </resultMap>

    <resultMap id="teacherInfoMap" type="com.qinjiangbo.pojo.Teacher">
        <id column="t_id" property="id"/>
        <result column="t_name" property="name"/>
        <result column="t_office" property="office"/>
        <result column="t_gender" property="gender"/>
    </resultMap>

    <select id="findClassById2" parameterType="int" resultMap="classInfoMap2">
        SELECT * FROM class WHERE c_id = #{id};
    </select>

    <select id="findTeacherById" parameterType="int" resultMap="teacherInfoMap">
        SELECT * FROM teacher WHERE t_id = #{id};
    </select>

</mapper>
```

ClassesMapper.java

``` java
package com.qinjiangbo.dao;

import com.qinjiangbo.pojo.Classes;

import java.util.List;

/**
 * Date: 9/16/16
 * Author: qinjiangbo@github.io
 */
public interface ClassesMapper {

    /**
     * 根据编号查找班级信息
     *
     * @param id
     * @return
     */
    public Classes findClassById(int id);

    public Classes findClassById2(int id);
    
}

```

`注意` 这里需要在MyBatis的配置文件中注册这个Mapper映射文件，千万别忘记了，否则会报错！

4.测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.ClassesMapper;
import com.qinjiangbo.pojo.Classes;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/17/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest4 {
    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void testFindClassById() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById(1);
        System.out.println(classes);
    }

    @Test
    public void testFindClassById2() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById2(1);
        System.out.println(classes);
    }
    
    @After
    public void commit() {
        sqlSession.commit();
    }
}

```

### 一对多查询

1.修改Mapper映射文件

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.ClassesMapper">

    <!-- 根据班级编号查找班级、教师信息 -->
    <!--
        以下为第一种方式,执行一次查询
        SELECT * FROM class c, teacher t WHERE c.c_id = #{id} AND c.t_id = t.t_id;
    -->
    <resultMap id="classInfoMap" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association property="teacher" javaType="com.qinjiangbo.pojo.Teacher">
            <id column="t_id" property="id"/>
            <result column="t_name" property="name"/>
            <result column="t_office" property="office"/>
            <result column="t_gender" property="gender"/>
        </association>
    </resultMap>

    <select id="findClassById" parameterType="int" resultMap="classInfoMap">
        SELECT * FROM class c, teacher t WHERE c.c_id = #{id} AND c.t_id = t.t_id;
    </select>

    <!--
        以下为第二种方式,执行两次查询
        1. SELECT * FROM class WHERE c_id = #{id};
        2. SELECT * FROM teacher WHERE t_id = #{id};
        第二次查询依赖于第一次查询所得的结果
    -->
    <resultMap id="classInfoMap2" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association column="t_id" property="teacher" select="findTeacherById"/>
    </resultMap>

    <resultMap id="teacherInfoMap" type="com.qinjiangbo.pojo.Teacher">
        <id column="t_id" property="id"/>
        <result column="t_name" property="name"/>
        <result column="t_office" property="office"/>
        <result column="t_gender" property="gender"/>
    </resultMap>

    <select id="findClassById2" parameterType="int" resultMap="classInfoMap2">
        SELECT * FROM class WHERE c_id = #{id};
    </select>

    <select id="findTeacherById" parameterType="int" resultMap="teacherInfoMap">
        SELECT * FROM teacher WHERE t_id = #{id};
    </select>

    <!-- 根据班级编号查找班级、教师、学生信息 -->
    <!--
        方式一: 嵌套结果: 使用嵌套结果映射来处理重复的联合结果的子集
    -->
    <resultMap id="classInfoMap3" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association property="teacher" javaType="com.qinjiangbo.pojo.Teacher">
            <id column="t_id" property="id"/>
            <result column="t_name" property="name"/>
            <result column="t_office" property="office"/>
            <result column="t_gender" property="gender"/>
        </association>
        <collection property="students" ofType="com.qinjiangbo.pojo.Student">
            <id column="s_id" property="id"/>
            <result column="s_name" property="name"/>
            <result column="s_password" property="password"/>
            <result column="s_age" property="age"/>
        </collection>
    </resultMap>

    <select id="findClassById3" parameterType="int" resultMap="classInfoMap3">
        SELECT * FROM class c, teacher t, student s WHERE c.t_id = t.t_id AND s.c_id = c.c_id AND c.c_id = #{id};
    </select>

    <!--
        方式二：嵌套查询：通过执行另外一个SQL映射语句来返回预期的复杂类型
    -->
    <resultMap id="classInfoMap4" type="com.qinjiangbo.pojo.Classes">
        <id column="c_id" property="id"/>
        <result column="c_name" property="name"/>
        <association column="t_id" property="teacher" javaType="com.qinjiangbo.pojo.Teacher" select="findTeacherById2"/>
        <collection property="students" ofType="com.qinjiangbo.pojo.Student" column="c_id" select="findStudentsByCid"/>
    </resultMap>

    <select id="findClassById4" parameterType="int" resultMap="classInfoMap4">
        SELECT * FROM class WHERE c_id = #{id};
    </select>

    <select id="findTeacherById2" parameterType="int" resultMap="teacherInfoMap">
        SELECT * FROM teacher WHERE t_id = #{id};
    </select>

    <resultMap id="studentInfoMap" type="com.qinjiangbo.pojo.Student">
        <id column="s_id" property="id"/>
        <result column="s_name" property="name"/>
        <result column="s_password" property="password"/>
        <result column="s_age" property="age"/>
    </resultMap>

    <select id="findStudentsByCid" parameterType="int" resultMap="studentInfoMap">
        SELECT * FROM student WHERE c_id = #{id};
    </select>
</mapper>
```

2.测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.ClassesMapper;
import com.qinjiangbo.pojo.Classes;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/17/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest4 {
    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void testFindClassById() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById(1);
        System.out.println(classes);
    }

    @Test
    public void testFindClassById2() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById2(1);
        System.out.println(classes);
    }

    @Test
    public void testFindClassById3() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById3(1);
        System.out.println(classes);
    }

    @Test
    public void testFindClassById4() {
        ClassesMapper classesMapper = sqlSession.getMapper(ClassesMapper.class);
        Classes classes = classesMapper.findClassById4(1);
        System.out.println(classes);
    }

    @After
    public void commit() {
        sqlSession.commit();
    }
}

```

**以上结果均通过严格的测试！**