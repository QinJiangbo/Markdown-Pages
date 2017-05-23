---
title: MyBatis实战-2-CRUD操作
date: 2016-09-14 10:46:58
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
`NOTE` CRUD操作指的就是增(Create)删(Delete)改(Update)查(Retrieve)
## 使用MyBatis对数据表进行CRUD操作--XML实现
1.定义Mapper映射XML文件UserMapper.xml

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.qinjiangbo.dao.UserMapper">
    <!-- 根据编号查找用户 -->
    <select id="findUserById" parameterType="int"
            resultType="com.qinjiangbo.pojo.User">
        SELECT * FROM users WHERE id = #{id}
    </select>

    <!-- 创建用户信息 -->
    <insert id="createUser" parameterType="com.qinjiangbo.pojo.User">
        INSERT INTO users
        VALUES (#{id}, #{name}, #{pwd}, #{age}, #{email}, #{tel}, #{addr})
    </insert>

    <!-- 删除用户信息 -->
    <delete id="deleteUser" parameterType="int">
        DELETE FROM users WHERE id = #{id}
    </delete>

    <!-- 更新用户信息 -->
    <update id="updateUser" parameterType="com.qinjiangbo.pojo.User">
        UPDATE users SET name = #{name}, pwd = #{pwd}, age = #{age}, email = #{email},
        tel = #{tel}, addr = #{addr} WHERE id = #{id}
    </update>

</mapper>
```

2.在MyBatis文件中注册UserMapper.xml，这个前一个教程讲过，这里不再赘述。
3.测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.UserMapperC;
import com.qinjiangbo.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/13/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest2 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    /**
     * BASED ON XML
     */

    // C
    @Test
    public void testCreateUser() {
        // 这里需要指明是Mapper中的哪个方法
        String statement = "com.qinjiangbo.dao.UserMapper.createUser";
        User user = new User(580327, "Xinxin", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        sqlSession.insert(statement, user);
    }

    // D
    @Test
    public void testDeleteUser() {
        String statement = "com.qinjiangbo.dao.UserMapper.deleteUser";
        sqlSession.delete(statement, 580327);
    }

    // U
    @Test
    public void testUpdateUser() {
        User user = new User(580314, "QinJiangbo", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        String statement = "com.qinjiangbo.dao.UserMapper.updateUser";
        sqlSession.update(statement, user);
    }

    // R
    @Test
    public void testRetrieveUser() {
        String statement = "com.qinjiangbo.dao.UserMapper.findUserById";
        User user = sqlSession.selectOne(statement, 580314);
        System.out.println(user);
    }

    @After
    public void commit() {
        sqlSession.commit();
    }

}

```

## 使用MyBatis对数据表进行CRUD操作--Java注解实现
1.定义Mapper映射类UserMapperC.java

``` java
package com.qinjiangbo.dao;

import com.qinjiangbo.pojo.User;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

/**
 * Date: 9/14/16
 * Author: qinjiangbo@github.io
 */
public interface UserMapperC {

    @Insert("INSERT INTO users VALUES (#{id}, #{name}, #{pwd}, #{age}, #{email}, #{tel}, #{addr})")
    public int createUser(User user);

    @Delete("DELETE FROM users WHERE id = #{id}")
    public int deleteUser(int id);

    @Update("UPDATE users SET name = #{name}, pwd = #{pwd}, age = #{age}, email = #{email}, tel = #{tel}, addr = #{addr} WHERE id = #{id}")
    public int updateUser(User user);

    @Select("SELECT * FROM users WHERE id = #{id}")
    public User retrieveUser(int id);
}

```

2.在MyBatis文件中注册UserMapperC.java

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="Richard"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/qinjiangbo/dao/UserMapper.xml"/>
        <mapper class="com.qinjiangbo.dao.UserMapperC"/>
    </mappers>
</configuration>
```

3.测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.UserMapperC;
import com.qinjiangbo.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/13/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest2 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    /**
     * BASED ON ANNOTATION
     */

    // C
    @Test
    public void testCreateUser2() {
        UserMapperC userMapperC = sqlSession.getMapper(UserMapperC.class);
        User user = new User(580327, "Xinxin", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        userMapperC.createUser(user);
    }

    @Test
    public void testDeleteUser2() {
        UserMapperC userMapperC = sqlSession.getMapper(UserMapperC.class);
        userMapperC.deleteUser(580327);
    }

    @Test
    public void testUpdateUser2() {
        UserMapperC userMapperC = sqlSession.getMapper(UserMapperC.class);
        User user = new User(580314, "Handsome", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        userMapperC.updateUser(user);
    }

    @Test
    public void testRetrieveUser2() {
        UserMapperC userMapperC = sqlSession.getMapper(UserMapperC.class);
        User user = userMapperC.retrieveUser(580314);
        System.out.println(user);
    }

    @After
    public void commit() {
        sqlSession.commit();
    }

}

```

## 使用MyBatis对数据表进行CRUD操作--XML与注解混合实现（推荐）
1.定义Mapper映射文件UserMapper.xml和UserMapper.java

`NOTE`这里Java文件和XML文件中指定的名称必须一致。一般来说，在这里两个文件的名称一致就行了。

``` java
package com.qinjiangbo.dao;

import com.qinjiangbo.pojo.User;

/**
 * Date: 9/14/16
 * Author: qinjiangbo@github.io
 */
public interface UserMapper {

    public User findUserById(int id);

    public int createUser(User user);

    public int updateUser(User user);

    public int deleteUser(int id);

}

```

2.在MyBatis文件中注册UserMapper.xml

`NOTE`这个之前讲过就不再赘述，不过有一个地方需要注意，同时采用XML和注解时，需要将XML文件中的命名空间namespace设置为Mapper接口的全类路径，否则接口和XML具体实现没法对应。

3.测试实例

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.dao.UserMapper;
import com.qinjiangbo.dao.UserMapperC;
import com.qinjiangbo.pojo.User;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;

/**
 * Date: 9/13/16
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest2 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }
    
    /**
     * BASED ON ANNOTATION AND XML
     */

    // C
    @Test
    public void testCreateUser3() {
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User(580327, "Xinxin", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        userMapper.createUser(user);
    }

    @Test
    public void testDeleteUser3() {
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        userMapper.deleteUser(580327);
    }

    @Test
    public void testUpdateUser3() {
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User(580314, "Handsome", "123456", 23, "123abc@126.com", "13888899211", "Wuhan University");
        userMapper.updateUser(user);
    }

    @Test
    public void testRetrieveUser3() {
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = userMapper.findUserById(580314);
        System.out.println(user);
    }

    @After
    public void commit() {
        sqlSession.commit();
    }

}

```

以上的相关代码是全部测试通过的，关于使用MyBatis对表执行CRUD操作的内容就这么多。