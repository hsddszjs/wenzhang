---
layout: post
title: "Java注解源代码处理"
description: ""
comments: true
keywords: ""
---


源代码级别的注解处理是从jdk5开始的。通常这个api被用来写代码生成器。

这个api无法改动原来的源文件，但是可以增加新的源文件(不限于java文件)。

mapstruct是就是这样一个利用注解处理的类库。Lombok是一个特殊的例子，它使用了编译器的内部api修改了生成的类，这与我们这里的讨论的东西不同。


## 过程

处理有好几轮，每当有新的源文件产生就会触发新的一轮，直到没有新的源文件产生。

## 写法

先创建注解类

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface MyAnno {
}
```

然后创建处理类，继承*AbstractProcessor*

```java
@SupportedAnnotationTypes("com.example.annotation.processor.MyAnno")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
// @AutoService(Processor.class)
public class MyAnnoProcessor extends AbstractProcessor {
 
    @Override
    public boolean process(Set<? extends TypeElement> annotations, 
      RoundEnvironment roundEnv) {
        JavaFileObject builderFile = processingEnv.getFiler()
        .createSourceFile(...);
        try (PrintWriter out = new PrintWriter(builderFile.openWriter())) {
            //在这里生成你要的新的源文件
        }
        return false;
    }
}
```

## 使用

有显式调用和注册SPI自动调用两种

1. 编译器显式调用

```bash
javac -processor com.baeldung.annotation.processor.MyProcessor Person.java
```

2. maven显式调用

```xml
<build>
    <plugins>
 
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.5.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <generatedSourcesDirectory>${project.build.directory}
                  /generated-sources/</generatedSourcesDirectory>
                <annotationProcessors>
                    <annotationProcessor>
                        com.example.annotation.processor.MyAnnoProcessor
                    </annotationProcessor>
                </annotationProcessors>
            </configuration>
        </plugin>
 
    </plugins>
</build>
```
3. 注册SPI自动调用

手动注册：
新建src/main/resources/META-INF/services/javax.annotation.processing.Processor，写入

```
com.example.annotation.processor.MyAnnoProcessor
```

但是这样打包会报错([ERROR] Bad service configuration file)，原因是自己编译自己的时候这个类尚未注册

自动注册：
使用google的auto-service，在所写的Processer上加上@AutoService(Processor.class)即可，这样打包时会自动在jar下生成



