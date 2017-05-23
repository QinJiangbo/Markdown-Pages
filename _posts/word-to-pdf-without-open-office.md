---
title: ' Word转PDF[不采用OpenOffice]'
date: 2016-08-16 22:50:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/pdf-thumbnail.jpg
tags:
	- DOC转PDF
	- DOCX4J
categories:
	- 开发技术
	- Java
keywords:
	- DOC转PDF
	- DOCX4J
---

目前在做一个与文档有关的项目，网上看到的都是利用OpenOffice来转化word为pdf的，其实局限性很大，下载那么大一个软件，却只是为了它的服务。所以决定找一下有没有别的方法，终于遇到了docx4j这个神一样的JAR包，完美解决了我的问题！不说了，直接上代码！

所需Jar包如下:

- avalon-framework-4.1.5
- commons-io-2.4 
- docx4j-2.7.1 
- log4j-1.2.15 
- serializer-2.7.1 
- xmlgraphics-commons-1.3 
- batik-util-1.6-1 
- commons-logging-1.1.3 
- fop-0.93 
- xalan-2.7.1

代码如下：

``` java
package in.javadomain;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.List;

import org.docx4j.convert.out.pdf.viaXSLFO.PdfSettings;
import org.docx4j.fonts.IdentityPlusMapper;
import org.docx4j.fonts.Mapper;
import org.docx4j.fonts.PhysicalFont;
import org.docx4j.fonts.PhysicalFonts;
import org.docx4j.openpackaging.packages.WordprocessingMLPackage;

public class Word2Pdf {
    public static void main(String[] args) {
        try {

            long start = System.currentTimeMillis();

            InputStream is = new FileInputStream(
                    new File("D:\\javadomain.docx"));
            WordprocessingMLPackage wordMLPackage = WordprocessingMLPackage
                    .load(is);
            List sections = wordMLPackage.getDocumentModel().getSections();
            for (int i = 0; i < sections.size(); i++) {

                System.out.println("sections Size" + sections.size());
                wordMLPackage.getDocumentModel().getSections().get(i)
                        .getPageDimensions().setHeaderExtent(3000);
            }
            Mapper fontMapper = new IdentityPlusMapper();

            PhysicalFont font = PhysicalFonts.getPhysicalFonts().get(
                    "Comic Sans MS");

            fontMapper.getFontMappings().put("Algerian", font);

            wordMLPackage.setFontMapper(fontMapper);
            PdfSettings pdfSettings = new PdfSettings();
            org.docx4j.convert.out.pdf.PdfConversion conversion = new org.docx4j.convert.out.pdf.viaXSLFO.Conversion(
                    wordMLPackage);

            OutputStream out = new FileOutputStream(new File(
                    "D:\\javadomain.pdf"));
            conversion.output(out, pdfSettings);
            System.err.println("Time taken to Generate pdf  "
                    + (System.currentTimeMillis() - start) + "ms");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
