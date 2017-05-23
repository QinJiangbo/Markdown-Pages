---
title: MyBatis实战-3-优化MyBatis配置项
date: 2016-09-14 22:45:58
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
## 优化MyBatis配置项
前面一节说了MyBatis实现CRUD等操作的具体实践，本节将要站在代码的可读性和可维护性上优化MyBatis的配置文件的代码，已达到简洁的目的。

### 分离数据库配置项
**一般来说，数据库的配置信息是需要动态变化的。**在生产环境中，应用需要连接到生产数据库，在测试环境中，应用需要连接到测试数据库。如果数据库的配置不是动态配置的，则这项配置会变得非常麻烦，大大加大了工程师的工作量，而且是**极易发生错误**的，且发生了错误排除的成本也会非常的高，所以，我们有必要将这个数据库配置项单独分离出来。

1.在conf包下面创建一个database.properties文件

``` dsconfig
jdbc.driver = com.mysql.cj.jdbc.Driver
jdbc.url = jdbc:mysql://localhost:3306/mybatis
jdbc.username = Richard
jdbc.password = 123456
```

2.在MyBatis配置文件下面添加这个配置

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!-- 导入数据库相关的配置信息 -->
    <properties resource="com/qinjiangbo/conf/database.properties"/>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
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

测试实例同上一节相同，请同学们自己结合之前的代码再跑一遍。博主本地的代码均通过严格的测试。

### 将实体类定义一个别名
通常在UserMapper.xml文件中，我们如果需要制定参数类型或者返回数据类型为一个pojo类型，那么我们需要写上它的全类名，这样在某种程度上加大了复杂度，而且不利于开发人员维护代码。这个时候我们需要将这个全类名别名化，博主是提倡使用pojo类本身的类名作为别名的。

1.在MyBatis配置文件中的configuration元素下添加一个typeAliases元素。

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!-- 导入数据库相关的配置信息 -->
    <properties resource="com/qinjiangbo/conf/database.properties"/>

    <!-- 添加别名 -->
    <typeAliases>
        <typeAlias type="com.qinjiangbo.pojo.User" alias="User"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/qinjiangbo/dao/UserMapper.xml"/>
        <mapper class="com.qinjiangbo.dao.UserMapperC"/>
    </mappers>
</configuration>
```

`NOTE`如果你想要所有的pojo类都自动使用类名来做别名，而不像一个一个配置很麻烦，你可以使用package这个元素，它会默认将这个包下面的所有的类别名化。

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!-- 导入数据库相关的配置信息 -->
    <properties resource="com/qinjiangbo/conf/database.properties"/>

    <!-- 添加别名 -->
    <typeAliases>
        <package name="com.qinjiangbo.pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/qinjiangbo/dao/UserMapper.xml"/>
        <mapper class="com.qinjiangbo.dao.UserMapperC"/>
    </mappers>
</configuration>
```

2.将UserMapper.xml配置文件中的全类名全部改为类名。

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 当同时使用XML和注解的时候, 这个命名空间namespace的值必须是Mapper接口的全类路径 -->
<mapper namespace="com.qinjiangbo.dao.UserMapper">
    <!-- 根据编号查找用户 -->
    <select id="findUserById" parameterType="int"
            resultType="User">
        SELECT * FROM users WHERE id = #{id}
    </select>

    <!-- 创建用户信息 -->
    <insert id="createUser" parameterType="User">
        INSERT INTO users
        VALUES (#{id}, #{name}, #{pwd}, #{age}, #{email}, #{tel}, #{addr})
    </insert>

    <!-- 删除用户信息 -->
    <delete id="deleteUser" parameterType="int">
        DELETE FROM users WHERE id = #{id}
    </delete>

    <!-- 更新用户信息 -->
    <update id="updateUser" parameterType="User">
        UPDATE users SET name = #{name}, pwd = #{pwd}, age = #{age}, email = #{email},
        tel = #{tel}, addr = #{addr} WHERE id = #{id}
    </update>

</mapper>
```

3.测试实例

同上一样，这个测试实例和上一节的测试实例相同，请同学们自己结合之前的代码再跑一遍。博主本地的代码均通过严格的测试。
