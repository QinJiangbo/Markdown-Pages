---
title: MyBatis实战-7-XML标签动态 SQL
date: 2016-09-25 21:04:08
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
## MyBatis的XML标签
MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其他类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句有多么痛苦。拼接的时候要确保不能忘了必要的空格，还要注意省掉列名列表最后的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。

### 代码示例
这里就不做过多讲解啦，需要说的都在代码中进行了注释，希望大家仔细阅读。

TeacherMapper.java

``` java
package com.qinjiangbo.dao;

import com.qinjiangbo.pojo.Teacher;

import java.util.List;
import java.util.Map;

/**
 * Date: 24/09/2016
 * Author: qinjiangbo@github.io
 */
public interface TeacherMapper {

    /**
     * 调用存储过程
     *
     * @param map
     * @return
     */
    public void countTeacherNumber(Map map);

    /**
     * 根据编号列表查找教师信息
     *
     * @param ids List
     * @return
     */
    public List<Teacher> findTeachersByIds(List<Integer> ids);

    /**
     * 根据编号列表查找教师信息
     *
     * @param ids Array
     * @return
     */
    public List<Teacher> findTeachersByIds2(int[] ids);

    /**
     * 根据名称前缀获取教师信息
     *
     * @param prefix
     * @return
     */
    public List<Teacher> findTeacherByNamePrefix(String prefix);

    /**
     * 更新教师信息
     *
     * @param teacher
     * @return
     */
    public int updateTeacherInfo(Teacher teacher);
}

```

TeacherMapper.xml

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.TeacherMapper">
    <select id="countTeacherNumber" parameterMap="countTeacherNumberMap" statementType="CALLABLE">
        CALL mybatis.count_teacher_procedure(?, ?)
    </select>

    <parameterMap type="java.util.Map" id="countTeacherNumberMap">
        <parameter property="t_sex" jdbcType="INTEGER" mode="IN"/>
        <parameter property="user_count" jdbcType="INTEGER" mode="OUT"/>
    </parameterMap>

    <select id="findTeachersByIds" resultMap="teacherInfoMap">
        SELECT * FROM teacher WHERE t_id in
        <!-- collection这里指定参数的集合类型 list, array等-->
        <foreach collection="list" item="ids" index="index" open="(" close=")" separator=",">
            #{ids}
        </foreach>
    </select>

    <select id="findTeachersByIds2" resultMap="teacherInfoMap">
        SELECT * FROM teacher WHERE t_id in
        <!-- collection这里指定参数的集合类型 list, array等-->
        <foreach collection="array" item="ids" index="index" open="(" close=")" separator=",">
            #{ids}
        </foreach>
    </select>

    <!--
        !!注意!!
        这个地方特别容易出错!当使用if标签的时候,需要注意这个问题,
        就是单个参数进来判断为空,必须使用_parameter来进行判断,否则
        会报错。如果是多个参数的话建议使用一个参数对象封装起来,然后直接使用
        #{参数名}来判断!切记!
    -->
    <select id="findTeacherByNamePrefix" parameterType="java.lang.String" resultMap="teacherInfoMap">
        SELECT * FROM teacher
        <if test="_parameter != null and _parameter != ''">
            WHERE t_name LIKE CONCAT(#{prefix}, '%')
        </if>
    </select>

    <update id="updateTeacherInfo" parameterType="com.qinjiangbo.pojo.Teacher">
        UPDATE teacher
        <set>
            <if test="name != null and name != ''">
                t_name = #{name}
            </if>
            <if test="office != null and office != ''">
                t_office = #{office}
            </if>
            <if test="gender != null and gender != ''">
                t_gender = #{gender}
            </if>
        </set>
        WHERE t_id = #{id}
    </update>

    <resultMap id="teacherInfoMap" type="com.qinjiangbo.pojo.Teacher">
        <id column="t_id" property="id"/>
        <result column="t_name" property="name"/>
        <result column="t_office" property="office"/>
        <result column="t_gender" property="gender"/>
    </resultMap>
</mapper>
```
### 测试用例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.TeacherMapper;
import com.qinjiangbo.pojo.Teacher;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

/**
 * Date: 25/09/2016
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest6 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void testFindTeacherByIds() {
        TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
        List<Integer> ids = new ArrayList<Integer>();
        ids.add(1002);
        ids.add(1003);
        ids.add(1006);
        List<Teacher> teachers = teacherMapper.findTeachersByIds(ids);
        Iterator<Teacher> iterator = teachers.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    @Test
    public void testFindTeacherByIds2() {
        TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
        int[] ids = new int[]{1002, 1004, 1005};
        List<Teacher> teachers = teacherMapper.findTeachersByIds2(ids);
        Iterator<Teacher> iterator = teachers.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    @Test
    public void testFindTeacherByNamePrefix() {
        TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
        List<Teacher> teachers = teacherMapper.findTeacherByNamePrefix("s");
        Iterator<Teacher> iterator = teachers.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    @Test
    public void testUpdateTeacherInfo() {
        TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
        Teacher teacher = new Teacher();
        teacher.setId(1003);
        teacher.setName("QinJiangbo");
        int result = teacherMapper.updateTeacherInfo(teacher);
        System.out.println(result);
    }

    @After
    public void commit() {
        sqlSession.commit();
    }
}

```

以上代码均通过严格的测试，大家可以根据自己的代码再跑一遍。