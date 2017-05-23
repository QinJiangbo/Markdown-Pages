---
title: MyBatis实战-8-代码生成工具MBG
date: 2016-10-01 21:35:55
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
## MyBatis代码生成工具MyBatis Generator

### MyBatis Generator简介
MyBatis Generator (MBG) 是一个Mybatis的代码生成器 MyBatis 和 iBATIS. 它可以生成Mybatis各个版本的代码，和iBATIS 2.2.0版本以后的代码。 它可以内省数据库的表（或多个表）然后生成可以用来访问（多个）表的基础对象。 这样和数据库表进行交互时不需要创建对象和配置文件。 MBG的解决了对数据库操作有最大影响的一些简单的CRUD（插入，查询，更新，删除）操作。 你仍然需要对联合查询和存储过程手写SQL和对象。

### MBG生成对象说明
MyBatis Generator 会生成:

匹配表结构的Java POJO，可能包括:

- 一个和表主键匹配的类(如果存在主键[注:只有联合主键会有])
- 一个包含了非主键字段的类(BLOB字段除外[注:单字段做主键时这里会包含])
- 一个包含了BLOB字段的类 (如果表包含了BLOB字段)
- 一个允许动态查询、更新和删除的类[注:指的是Example查询]

这些类之间会有适当的继承关系。 请注意可以配置生成器来生成不同类型的 POJO 的层次结构。 例如，如果你愿意你可能会选择针对每个表生成一个单独的实体对象。

### MBG映射文件说明

MyBatis/iBATIS 兼容 SQL 映射 XML 文件。MBG 在配置中为每个表简单的 CRUD 操作生成 SQL。 生成的 SQL 语句包括：

- insert (插入)
- update by primary key (根据主键更新记录)
- update by example (根据条件更新记录)
- delete by primary key (根据主键删除记录)
- delete by example (根据条件删除记录)
- select by primary key (根据主键查询记录)
- select by example (根据条件查询记录集)
- count by example (根据条件查询记录总数)

根据表的结构，生成的这些语句会有不同的变化（例如，如果表中没有主键，那么 MBG 将不会生成update by primary key方法）。

### 项目依赖管理

当前MySQL的JDBC连接的jar包已经出到了6.0.3版本，但很遗憾的是，这个版本还存在很多问题，比如jdbc连接的方式的改变，原来的声明需要做大量的修改，此外，最新版的mybatis-generator-core也无法正常的工作，因为生成的映射文件不包含primary key选项，估计是最新的MySQL连接包作了限制。现在当前环境建议大家还是使用5.x版本比较靠谱。以下是相关依赖：

- dom4j-1.6.1.jar
- hamcrest-core-1.3.jar
- junit-4.12.jar
- mybatis-3.4.1.jar
- mybatis-generator-core-1.3.5.jar
- mysql-connector-java-5.1.39.jar

Maven地址（关于Maven相关的教程后续会陆续推出，这里提供给使用Maven的读者）

``` xml
<dependency>
    <groupId>dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>1.6.1</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest-core</artifactId>
    <version>1.3</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.1</version>
</dependency>
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.5</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.39</version>
</dependency>
```

### （一）生成模板映射XML文件

CreateGenConfigXML.java

``` java
package com.qinjiangbo.gen.util;

import org.dom4j.Document;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.dom.DOMDocumentType;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;
import org.dom4j.tree.DefaultDocumentType;

import java.io.File;
import java.io.FileOutputStream;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

/**
 * Date: 9/26/16
 * Author: qinjiangbo@github.io
 */
public class CreateGenConfigXML {

    /**
     * 获得数据库连接
     *
     * @return
     */
    public static Connection getConnection() {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            Connection connection =
                    DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis",
                            "Richard", "123456");
            return connection;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 获取全部的表名
     * @return
     * @throws SQLException
     */
    public static List<String> getTableNames() throws SQLException {
        List<String> names = new ArrayList<String>();
        Connection connection = getConnection();
        DatabaseMetaData databaseMetaData = connection.getMetaData();
        String[] types = {"TABLE"};
        ResultSet resultSet = databaseMetaData.getTables(null, null, "%", types);
        while (resultSet.next()) {
            String name = (String) resultSet.getObject("TABLE_NAME");
            if (name.contains("（") || name.contains("）")) {
                continue;
            }
            names.add(name);
        }
        connection.close();
        return names;
    }

    /**
     * 生成DOM文件
     * @return
     * @throws SQLException
     */
    private static Document createDom() throws SQLException {
        Document doc = DocumentHelper.createDocument();
        //创建DOCTYPE申明
        doc.setDocType(new DOMDocumentType("generatorConfiguration",
                "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN",
                "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd"));

        //创建根结点
        Element root = doc.addElement("generatorConfiguration");

        //创建上下文结点
        Element context = root.addElement("context");
        context.addAttribute("id", "MBG");
        context.addAttribute("targetRuntime", "Mybatis3");
        context.addAttribute("defaultModelType", "conditional");

        //创建插件结点(这里默认每个类序列化)
        Element plugin = context.addElement("plugin");
        plugin.addAttribute("type", "org.mybatis.generator.plugins.SerializablePlugin");

        //创建注释结点,判断是否生成注释
        Element commentGenerator = context.addElement("commentGenerator");
        Element property = commentGenerator.addElement("property");
        property.addAttribute("name", "suppressAllComments");
        property.addAttribute("value", "false");

        //创建jdbc连接结点
        Element jdbcConnection = context.addElement("jdbcConnection");
        jdbcConnection.addAttribute("driverClass", "com.mysql.jdbc.Driver");
        jdbcConnection.addAttribute("connectionURL", "jdbc:mysql://localhost:3306/mybatis");
        jdbcConnection.addAttribute("userId", "Richard");
        jdbcConnection.addAttribute("password", "123456");

        //创建java类型处理类结点
        Element javaTypeResolver = context.addElement("javaTypeResolver");
        Element property2 = javaTypeResolver.addElement("property");
        property2.addAttribute("name", "forceBigDecimals");
        property2.addAttribute("value", "false");

        //创建java模型结点
        Element javaModelGenerator = context.addElement("javaModelGenerator");
        javaModelGenerator.addAttribute("targetPackage", "com.qinjiangbo.gen.model");
        javaModelGenerator.addAttribute("targetProject", "src");
        Element property3 = javaModelGenerator.addElement("property");
        property3.addAttribute("name", "enableSubPackages");
        property3.addAttribute("value", "true");

        //创建sql映射文件结点
        Element sqlMapGenerator = context.addElement("sqlMapGenerator");
        sqlMapGenerator.addAttribute("targetPackage", "com.qinjiangbo.gen.mapper");
        sqlMapGenerator.addAttribute("targetProject", "src");
        Element property4 = sqlMapGenerator.addElement("property");
        property4.addAttribute("name", "enableSubPackages");
        property4.addAttribute("value", "true");

        //创建java客户端结点,这里声明了dao层的位置
        Element javaClientGenerator = context.addElement("javaClientGenerator");
        javaClientGenerator.addAttribute("type", "XMLMAPPER");
        javaClientGenerator.addAttribute("targetPackage", "com.qinjiangbo.gen.mapper");
        javaClientGenerator.addAttribute("targetProject", "src");
        Element property5 = javaClientGenerator.addElement("property");
        property5.addAttribute("name", "enableSubPackages");
        property5.addAttribute("value", "true");

        //创建表(table)结点
        List<String> names = getTableNames();
        for (String name : names) {
            String[] words = name.split("_");
            String entityName = "";
            for (String word : words) {
                word = word.substring(0, 1).toUpperCase() + word.substring(1);
                if (entityName == null || entityName.equals("")) {
                    entityName = word;
                } else {
                    entityName = entityName + word;
                }
            }
            Element tableNode = context.addElement("table");
            tableNode.addAttribute("tableName", name);
            tableNode.addAttribute("domainObjectName", entityName);
            tableNode.addAttribute("enableCountByExample", "true");
            tableNode.addAttribute("enableUpdateByExample", "true");
            tableNode.addAttribute("enableDeleteByExample", "true");
            tableNode.addAttribute("enableSelectByExample", "true");
            tableNode.addAttribute("selectByExampleQueryId", "true");
            tableNode.addAttribute("enableDeleteByPrimaryKey", "true");
            tableNode.addAttribute("enableSelectByPrimaryKey", "true");
            tableNode.addAttribute("enableUpdateByPrimaryKey", "true");
            tableNode.addAttribute("enableInsert", "true");
        }

        return doc;
    }

    /**
     * 写入到XML文件中
     * @throws Exception
     */
    public static void writeXML() throws Exception {
        XMLWriter xmlWriter = null;
        String userDir = System.getProperty("user.dir");
        String fileName = userDir + "/src/com/qinjiangbo/gen/util/generatorConfiguration.xml";
        OutputFormat format = OutputFormat.createPrettyPrint();
        FileOutputStream fos = new FileOutputStream(new File(fileName));
        format.setEncoding("utf-8");
        xmlWriter = new XMLWriter(fos, format);
        xmlWriter.write(createDom());
        xmlWriter.close();
    }

    public static void main(String[] args) throws Exception {
        writeXML();
        System.out.println("Completed!");
    }
}

```

### （二）生成的GeneratorConfiguration.xml文件
由前一步生成的GeneratorConfiguration.xml文件如下：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
  <context id="MBG" targetRuntime="Mybatis3" defaultModelType="conditional">
    <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
    <commentGenerator>
      <property name="suppressAllComments" value="false"/>
    </commentGenerator>
    <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/mybatis"
                    userId="Richard" password="123456"/>
    <javaTypeResolver>
      <property name="forceBigDecimals" value="false"/>
    </javaTypeResolver>
    <javaModelGenerator targetPackage="com.qinjiangbo.gen.model" targetProject="src">
      <property name="enableSubPackages" value="true"/>
    </javaModelGenerator>
    <sqlMapGenerator targetPackage="com.qinjiangbo.gen.mapper" targetProject="src">
      <property name="enableSubPackages" value="true"/>
    </sqlMapGenerator>
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.qinjiangbo.gen.mapper" targetProject="src">
      <property name="enableSubPackages" value="true"/>
    </javaClientGenerator>
    <table tableName="class" domainObjectName="Class" enableCountByExample="true" enableUpdateByExample="true"
           enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="true"
           enableDeleteByPrimaryKey="true" enableSelectByPrimaryKey="true" enableUpdateByPrimaryKey="true"
           enableInsert="true"/>
    <table tableName="orders" domainObjectName="Orders" enableCountByExample="true" enableUpdateByExample="true"
           enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="true"
           enableDeleteByPrimaryKey="true" enableSelectByPrimaryKey="true" enableUpdateByPrimaryKey="true"
           enableInsert="true"/>
    <table tableName="student" domainObjectName="Student" enableCountByExample="true" enableUpdateByExample="true"
           enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="true"
           enableDeleteByPrimaryKey="true" enableSelectByPrimaryKey="true" enableUpdateByPrimaryKey="true"
           enableInsert="true"/>
    <table tableName="teacher" domainObjectName="Teacher" enableCountByExample="true" enableUpdateByExample="true"
           enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="true"
           enableDeleteByPrimaryKey="true" enableSelectByPrimaryKey="true" enableUpdateByPrimaryKey="true"
           enableInsert="true"/>
    <table tableName="users" domainObjectName="Users" enableCountByExample="true" enableUpdateByExample="true"
           enableDeleteByExample="true" enableSelectByExample="true" selectByExampleQueryId="true"
           enableDeleteByPrimaryKey="true" enableSelectByPrimaryKey="true" enableUpdateByPrimaryKey="true"
           enableInsert="true"/>
  </context>
</generatorConfiguration>

```

关于这个文件的具体说明请移步MyBatis-Generator的[官方说明](http://www.mybatis.org/generator/configreference/xmlconfig.html)，这里读者可以通过阅读XML获得相关属性的含义和作用。

### （三）生成DAO层的SQL映射文件

``` java
package com.qinjiangbo.gen.util;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

/**
 * Date: 9/26/16
 * Author: qinjiangbo@github.io
 */
public class Generator {

    public static void main(String[] args) throws Exception {
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        String userDir = System.getProperty("user.dir");
        File configFile = new File(userDir + "/src/com/qinjiangbo/gen/util/generatorConfiguration.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        myBatisGenerator.generate(null);
        System.out.println("Completed!");
    }
}

```
针对上面这个文件需要说明的是需要指明配置文件的所在位置，然后回调函数设置为空，表示不做任何操作。就是静态地生成这些DAO层的SQL映射文件即可。

### 项目结构图

项目的结构会发生很大的变化，新生成的SQL映射文件包括Java文件和XML文件都在同一个包，在企业开发中通常这两个也是一直被放在一个包，便于管理，然后模型Model放在另一个包。结构图如下：

![项目结构图](https://obrxbqjbi.qnssl.com/blog/image/MyBatis-8-1.png)

上面util包是前面生成映射文件所需要的工具包。读者可以根据自身的实际情况更改所在包。

### 项目测试

下面选择两个映射文件中的随机两个方法进行抽样测试（这个完全是为了节省版面，正规的还是需要做路径覆盖测试的，请读者自行在下面测试）。

``` java
package com.qinjiangbo.test;

import com.qinjiangbo.gen.mapper.StudentMapper;
import com.qinjiangbo.gen.mapper.TeacherMapper;
import com.qinjiangbo.gen.model.Student;
import com.qinjiangbo.gen.model.StudentExample;
import com.qinjiangbo.gen.model.Teacher;
import com.qinjiangbo.gen.model.TeacherExample;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;
import java.util.List;

/**
 * Date: 27/09/2016
 * Author: qinjiangbo@github.io
 */
public class MyBatisTest7 {

    private SqlSession sqlSession = null;

    @Before
    public void init() {
        String config = "com/qinjiangbo/conf/configure.xml";
        InputStream inputStream = MyBatisTest2.class.getClassLoader().getResourceAsStream(config);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void testFindTeachers() {
        TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
        TeacherExample example = new TeacherExample();
        example.createCriteria().andTGenderIsNotNull();
        List<Teacher> teacherList = mapper.selectByExample(example);
        for (Teacher teacher : teacherList) {
            System.out.println(teacher.gettName() + " " + teacher.gettGender());
        }
    }

    @Test
    public void testFindStudents() {
        StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
        StudentExample example = new StudentExample();
        example.createCriteria().andCIdEqualTo(3);
        List<Student> studentList = mapper.selectByExample(example);
        for (Student student : studentList) {
            System.out.println(student.getsName() + " " + student.getsAge());
        }
    }

    @After
    public void commit() {
        sqlSession.commit();
    }
}

```

#### 测试结果

testFindTeachers():

![测试结果](https://obrxbqjbi.qnssl.com/blog/image/MyBatis-8-2.png)

``` bash
Loris Male
Lily Female
QinJiangbo Female
Sony Male
Sarah Female
Toffy Male
Jeserf Male
```

testFindStudents():

![测试结果](https://obrxbqjbi.qnssl.com/blog/image/MyBatis-8-3.png)

``` bash
QinJiangbo 21
YuYing 21
```

以上测试全部通过！
