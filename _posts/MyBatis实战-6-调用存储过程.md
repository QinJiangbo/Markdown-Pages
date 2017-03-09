---
title: MyBatis实战-6-调用存储过程
date: 2016-09-24 23:12:08
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
## MyBatis调用存储过程
在业务逻辑非常复杂的时候，使用一般的代码编程会使得代码的效率变得非常的低，这个时候使用数据库中的存储过程将会是一个不错的选择。下面我们通过教师表这个例子为大家讲述如何使用MyBatis来调用数据库中的存储过程。

### 新建存储过程
在前面的教程中我们建立了Teacher表，我们现在有一个需求，需要知道男女老师的数量分别是多少？这个时候我们编写一个简单的存储过程。博主的环境是在MySQL5.5的环境下测试的，所有的语法都是基于这个版本。

``` sql
BEGIN
IF t_sex = 0 THEN
SELECT COUNT(*) FROM teacher WHERE t_gender = "Male" INTO user_count;
ELSE
SELECT COUNT(*) FROM teacher WHERE t_gender = "Female" INTO user_count;
END IF;
END
```

以上代码是在`Navicat`工具里面完成的，还需要设置输入输出参数，`IN t_sex INT, OUT user_count INT`，见下图。

![](https://obrxbqjbi.qnssl.com/blog/image/mybatis-6-1.png)

### 新建教师表映射关系

1.新建TeachMapper.java和TeacherMapper.xml两个文件，如下：

**TeacherMapper.java**

``` java
package com.qinjiangbo.dao;

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
}

```

**TeacherMapper.xml**

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
</mapper>
```
2.注册Mapper，这个千万不能忘记，具体操作见面教程，这里不再赘述。

### 测试实例
当编写完毕之后，我们还需要对整体进行一个测试，以便于测试我们编写代码是否有误。
在编写之前我需要给大家确认数据表中的数据。如下图：

![](https://obrxbqjbi.qnssl.com/blog/image/mybatis-6-2.png)

通过这个数据表我们可以知道女教师有三位，男教师有四位。

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.TeacherMapper;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;
import java.util.HashMap;
import java.util.Map;

/**
 * Date: 24/09/2016
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest5 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void testInvokeProcedure() {
        Map<String, Integer> map = new HashMap<>();
        map.put("t_sex", 1);
        map.put("user_count", 0);
        TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
        teacherMapper.countTeacherNumber(map);
        System.out.println(map.get("user_count"));
    }

    @After
    public void commit() {
        sqlSession.commit();
    }
}

```

最后结果确认，执行完毕之后控制台打印了3。说明我们之前的测试结果和业务需求的！