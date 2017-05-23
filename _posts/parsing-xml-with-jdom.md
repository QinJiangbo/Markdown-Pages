---
title: Jdom解析XML
date: 2016-04-17 22:29:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/xml-thumbnail.jpg
tags:
	- Jdom
	- XML
categories:
	- 开发技术
	- Java
keywords:
	- Jdom
	- XML
---
![](https://obrxbqjbi.qnssl.com/blog/image/xml-thumbnail.jpg)

XML是一种广为使用的可扩展标记语言，java中解析xml的方式有很多，最常用的像jdom、dom4j、sax等等。前两天刚好有个程序需要解析xml，就学了下jdom，写了个小例子，这里做个学习笔记。
 
 要使用jdom解析xml文件，需要下载jdom的包，我使用的是jdom-1.1。解压之后，将lib文件夹下的.jar文件以及build文件夹下的jdom.jar拷贝到工程文件夹下，然后就可以使用jdom操作xml文件了。
 
### 一、读取xml文件

假设有这样一个xml文件：

``` xml
<?xml version="1.0" encoding="UTF-8"?>  
<sys-config>  
    <jdbc-info>  
        <driver-class-name>oracle.jdbc.driver.OracleDriver</driver-class-name>  
        <url>jdbc:oracle:thin:@localhost:1521:database</url>  
        <user-name>why</user-name>  
        <password>why</password>  
    </jdbc-info>  
    <provinces-info>  
        <province id="hlj" name="黑龙江">  
            <city id="harb">哈尔滨</city>  
            <city id="nj">嫩江</city>  
        </province>  
        <province id="jl" name="吉林"></province>  
    </provinces-info>  
</sys-config>
```

首先，用 org.jdom.input.SAXBuilder 这个类取得要操作的xml文件，会返回一个 org.jdom.Document 对象，这里需要做一下异常处理。然后，取得这个xml文件的根节点，org.jdom.Element 代表xml文件中的一个节点，取得跟节点后，便可以读取xml文件中的信息。利用 org.jdom.xpath.XPath 可以取得xml中的任意制定的节点中的信息。

例如，要取得上面文件中的 <jdbc-info> 下的 <driver-class-name> 中的内容，先取得这个节点Element driverClassNameElement = (Element)XPath.selectSingleNode(rootEle, "//sys-config/jdbc-info/driver-class-name")，注意，根节点前要使用两个 "/" ，然后，用 driverClassNameElement.getText() 便可以取得这个节点下的信息。

如果一个节点下有多个名称相同的子节点，可以用XPath.selectNodes()方法取得多个子节点的List，遍历这个List就可以操作各个子节点的内容了。

下面是我写的读取上面xml文件的例子，比起文字描述更直观一些吧：

``` java
package com.why.jdom;  
  
import java.io.IOException;  
import java.util.Iterator;  
import java.util.List;  
  
import org.jdom.input.SAXBuilder;  
import org.jdom.xpath.XPath;  
import org.jdom.Document;  
import org.jdom.Element;  
import org.jdom.JDOMException;  
  
public class ReadXML {  
  
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        SAXBuilder sax = new SAXBuilder();  
        try {  
            Document doc = sax.build("src/config.xml");  
            Element rootEle = doc.getRootElement();  
            Element driverClassNameElement = (Element)XPath.selectSingleNode(rootEle, "//sys-config/jdbc-info/driver-class-name");  
            String driverClassName = driverClassNameElement.getText();  
            System.out.println("driverClassName = " + driverClassName);  
              
            List provinceList = XPath.selectNodes(rootEle, "//sys-config/provinces-info/province");  
            for(Iterator it = provinceList.iterator();it.hasNext();){  
                Element provinceEle = (Element)it.next();  
                String proId = provinceEle.getAttributeValue("id");  
                String proName = provinceEle.getAttributeValue("name");  
  
                System.out.println("provinceId = " + proId + "   provinceName = " + proName);  
                  
                List cityEleList = (List)provinceEle.getChildren("city");  
                  
                for(Iterator cityIt = cityEleList.iterator();cityIt.hasNext();){  
                    Element cityEle = (Element)cityIt.next();  
                    String cityId = cityEle.getAttributeValue("id");  
                    String cityName = cityEle.getText();  
  
                    System.out.println("    cityId = " + cityId + "   cityName = " + cityName);  
                }  
            }  
        } catch (JDOMException e) {  
            // TODO 自动生成 catch 块  
            e.printStackTrace();  
        } catch (IOException e) {  
            // TODO 自动生成 catch 块  
            e.printStackTrace();  
        }  
    }  
}  
```

### 二、写xml文件
 
 写xml文件与读取xml文件的操作类似，利用 org.jdom.output.XMLOutputter 就可以将处理好的xml输出到文件了。可以设置文件的编码方式，不过一般使用UTF-8就可以了。代码如下：
 
 ``` java
 package com.why.jdom;  
  
import java.io.FileNotFoundException;  
import java.io.FileOutputStream;  
import java.io.IOException;  
  
import org.jdom.Document;  
import org.jdom.Element;  
import org.jdom.output.XMLOutputter;  
  
public class WriteXML {  
  
          
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        // TODO 自动生成方法存根  
        Element rootEle = new Element("sys-config");  
        Element provincesEle = new Element("provinces-info");  
          
        Element provinceEle = new Element("province");  
        provinceEle.setAttribute("id","hlj");  
        provinceEle.setAttribute("name","黑龙江省");  
          
        Element cityEle1 = new Element("city");  
        cityEle1.setAttribute("id","harb");  
        cityEle1.addContent("哈尔滨");  
          
        Element cityEle2 = new Element("city");  
        cityEle2.setAttribute("id","nj");  
        cityEle2.addContent("嫩江");  
          
          
        provinceEle.addContent(cityEle1);  
        provinceEle.addContent(cityEle2);  
        provincesEle.addContent(provinceEle);  
        rootEle.addContent(provincesEle);  
          
        Document doc = new Document(rootEle);  
          
        XMLOutputter out = new XMLOutputter();  
          
          
//      out.setFormat(Format.getCompactFormat().setEncoding("GBK"));//设置文件编码，默认为UTF-8  
        String xmlStr = out.outputString(doc);  
        System.out.println(xmlStr);  
          
        try {  
            out.output(doc, new FileOutputStream("c:/test.xml"));  
        } catch (FileNotFoundException e) {  
            // TODO 自动生成 catch 块  
            e.printStackTrace();  
        } catch (IOException e) {  
            // TODO 自动生成 catch 块  
            e.printStackTrace();  
        }     
    }
}  
 ```