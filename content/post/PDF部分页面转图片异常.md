---
title: "PDF部分页面转图片异常"
date: "2018-03-04"
categories:
 - "整理"
tags:
 - "异常"
 - "PDFBOX"
toc: true
---


# Error
```
java.lang.IllegalArgumentException: Numbers of source Raster bands and source color space components do not match
        at java.awt.image.ColorConvertOp.filter(ColorConvertOp.java:482)
        at com.sun.imageio.plugins.jpeg.JPEGImageReader.acceptPixels(JPEGImageReader.java:1268)
        at com.sun.imageio.plugins.jpeg.JPEGImageReader.readImage(Native Method)
        at com.sun.imageio.plugins.jpeg.JPEGImageReader.readInternal(JPEGImageReader.java:1236)
        at com.sun.imageio.plugins.jpeg.JPEGImageReader.read(JPEGImageReader.java:1039)
        at javax.imageio.ImageReader.read(ImageReader.java:939)
        at org.apache.pdfbox.filter.DCTFilter.decode(DCTFilter.java:85)
        at org.apache.pdfbox.cos.COSInputStream.create(COSInputStream.java:69)
        at org.apache.pdfbox.cos.COSStream.createInputStream(COSStream.java:162)
        at org.apache.pdfbox.pdmodel.common.PDStream.createInputStream(PDStream.java:235)
        at org.apache.pdfbox.pdmodel.graphics.image.PDImageXObject.<init>(PDImageXObject.java:125)
        at org.apache.pdfbox.pdmodel.graphics.PDXObject.createXObject(PDXObject.java:70)
        at org.apache.pdfbox.pdmodel.PDResources.getXObject(PDResources.java:409)
        at org.apache.pdfbox.contentstream.operator.graphics.DrawObject.process(DrawObject.java:53)
        at org.apache.pdfbox.contentstream.PDFStreamEngine.processOperator(PDFStreamEngine.java:838)
        at org.apache.pdfbox.contentstream.PDFStreamEngine.processStreamOperators(PDFStreamEngine.java:495)
        at org.apache.pdfbox.contentstream.PDFStreamEngine.processStream(PDFStreamEngine.java:469)
        at org.apache.pdfbox.contentstream.PDFStreamEngine.processPage(PDFStreamEngine.java:150)
        at org.apache.pdfbox.rendering.PageDrawer.drawPage(PageDrawer.java:206)
        at org.apache.pdfbox.rendering.PDFRenderer.renderImage(PDFRenderer.java:145)
        at org.apache.pdfbox.rendering.PDFRenderer.renderImageWithDPI(PDFRenderer.java:94)
        at com.yunkai.component.resource.service.impl.PDFConvertServiceImpl.convertToImage(PDFConvertServiceImpl.java:72)
        at com.yunkai.component.resource.service.impl.PDFConvertServiceImpl.convertToImage(PDFConvertServiceImpl.java:88)
        at com.yunkai.component.resource.service.impl.PDFConvertServiceImpl.convertToImage(PDFConvertServiceImpl.java:93)
        at com.yunkai.component.resource.service.impl.ConvertServiceImpl.pdf2Image(ConvertServiceImpl.java:171)
        at com.yunkai.training.commons.resource.service.impl.LogicConvertServiceImpl.handlerPDF(LogicConvertServiceImpl.java:319)
        at com.yunkai.training.commons.resource.service.impl.LogicConvertServiceImpl.convertExerciseToAVI(LogicConvertServiceImpl.java:187)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:483)
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:302)
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:190)
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:157)
        at org.springframework.aop.interceptor.AsyncExecutionInterceptor$1.call(AsyncExecutionInterceptor.java:115)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.lang.Thread.run(Thread.java:745)
```

# 参考        
- [Bug 178906 - IllegalArgumentException: Numbers of source Raster bands and source color space components do not match](https://netbeans.org/bugzilla/show_bug.cgi?id=178906)
- [JDK-4893408 : JPEGReader throws IllegalArgException when setting the destination to BYTE_GRAY](https://bugs.java.com/view_bug.do?bug_id=4893408)


# 优化
- 单片转空白页
- 尝试其它类型 ImageType