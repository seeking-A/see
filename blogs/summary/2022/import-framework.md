---
title: Android Studio引入自定义framework
date: 2022-09-18
tags: Android
categorys: 学习总结

---

 

# Android Studio引入自定义framework

## 前言

笔者进入公司后入门了安卓，日常接触的内容主要在 framework 层。组长上周安排我重构一个系统应用，该应用运行在公司定制的 Android 系统上，调用了 framework 层相关的自定义的接口和函数。由于工作性质原因，部门人员日常使用 Android Studio 也相对较少，而不使用集成环境进行开发过程又过于繁琐。因此笔者还是优先选择了 Android Studio 作为应用的主要开发工具。原因：Android Studio 集成了代码调试、应用签名、打包、部署等一系列功能，相比于在 Linux 下来回切换各种工具完成任务，效率不知要高出多少倍，毕竟专业的工具干专业的事。不过在此之前，由于所在部门人员都没相关经验，因此只能自己进行初步摸索。原本以为很快就能配置完成，结果花了近一个工作日的时间才从坑里爬出来。希望通过此次爬坑可以给看到本篇博客的朋友一些启发。



## 环境

系统环境：Ubuntu 14.04.1 x86_64

集成工具：Android Studio 2021.2.1 

编译插件：Gradle 7.2.1

Android版本：Android 11



## 步骤

### 1. 导入 framework.jar

笔者在此之前已经完成了 Android 源码的编译，因此直接从相应目录找到对应的 framework.jar 导入到项目的中。关于编译完源码后 framework.jar 所在路径，可以参考一下本篇博客：[Android Studio 导入自己编译的 framework.jar](https://blog.csdn.net/yzwfeng/article/details/125540849)。

- debug 版本

```shell
# debug 版本
out/target/product/projectXX/system/framewor framework.jar
```

- user 版本

```shell
# Android N/O
out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/classes.jar
# Android P/Q
out/soong/.intermediates/frameworks/base/framework/android_common/combined/framework.jar
# Android R
out/soong/.intermediates/frameworks/base/framework-minus-apex/android_common/combined/framework-minus-apex.jar
```



### 2. 添加 jar 依赖

向模块 app 的 build.gradle 的中添加依赖。因为引入的 framework.jar 只为了是 app 在调试时能够运行通过，所以设置为 compileOnly

```groovy
dependencies {
    compileOnly files('libs/framework.jar')
}
```

![image-20220915205205699](http://image.xiaobailx.top/images/202209182151475.png)



### 3. 调整 jar 编译优先级

在项目的 bulid.gradle 文件下添加如下代码：

```groovy
allprojects {
    gradle.projectsEvaluated {
        tasks.withType(JavaCompile) {
            Set<File> fileSet = options.bootstrapClasspath.getFiles()
            List<File> newFileList = new ArrayList<>();
            //相对位置，根据存放的位置修改路径
            newFileList.add(new File("app/libs/framework.jar"))
            newFileList.addAll(fileSet)
            options.bootstrapClasspath = files(
                    newFileList.toArray()
            )
            println('Change ' + project.name + '.iml order')
        }
    }
}
```

在这里有两个需要注意的地方，Gradle 插件版本和引入的 framework.jar 所在路径。笔者的 Gradle 插件使用的是 7.2.1 版本，framwork.jar 在 app 模块的 libs 目录下，这里配置的相对路径是以当前配置的 build.gradle 路径为基点。笔者在填坑过程中了解到先前低版本与高版本配置存在差异，低版本配置如下：

```groovy
allprojects {
    gradle.projectsEvaluated {
        tasks.withType(JavaCompile) {
            options.compilerArgs.add('-Xbootclasspath/p:app\\libs\\framework.jar')
        }
    }
}
```

在这里，“先前低版本” 笔者目前没有了解到具体版本号，笔者从该篇博客 [android编译 配置-Xbootclasspath/p优先级无效](https://blog.csdn.net/zou249014591/article/details/109752786) 判断应在 Gradle 6.7.1 之前。

![image-20220915212609947](http://image.xiaobailx.top/images/202209182151124.png)

完成以上配置，正常情况下就可以完成编译了，但是在源码中还会报红：Connot resolve symbol 'xxxx'，修改 .idea/moudel 目录下的 app.iml 文件，手动将 ` <orderEntry type="library" />`  调整到至 SDK  `<orderEntry type="jdk"/> ` 之前，如下：

```xml
<orderEntry type="library" name="Gradle: ./app/libs/framework.jar" level="project" />
<orderEntry type="jdk" jdkName="Android API 30 Platform" jdkType="Android SDK" />
```

此时源码不再报红且能正确定位到引入的 framework.jar 对应的接口和函数。但是 moudle 下的 .iml 文件是动态生成的，当 Gradle 再次进行同步时 .iml 又会调整回原来的状态。笔者填坑过程了解到一些版本可以使用如下代码实现 .iml 的动态调整，在 app 模块下的 build.gradle 下添加如下方法：

```groovy
preBuild {
    doLast {
        //此处文件名根据实际情况修改：
        def imlFile = file("../.idea/modules/app/xxxx.app.main.iml")
        try {
            def parsedXml = (new XmlParser()).parse(imlFile)
            def jdkNode = parsedXml.component[1].orderEntry.find { it.'@type' == 'jdk' }
            parsedXml.component[1].remove(jdkNode)
            def sdkString = "Android API " + android.compileSdkVersion.substring("android-".length()) + " Platform"
            println sdkString
            new Node(parsedXml.component[1], 'orderEntry', ['type': 'jdk', 'jdkName': sdkString, 'jdkType': 'Android SDK'])
            groovy.xml.XmlUtil.serialize(parsedXml, new FileOutputStream(imlFile))
        } catch (FileNotFoundException e) {
            // nop, iml not found
            println "no iml found"
        }
    }
}
```

以上配置代码实现了 `<orderEntry/>` 的自动调整。笔者初次尝试没有输出，发现没有反应，以为自己的版本问题，但在多次试错之后发现是路径问题，相对路径依然一当前配置文件所在位置为基点。

![image-20220915204003184](http://image.xiaobailx.top/images/202209182151457.png)



### 4. 修改 APK 签名配置

原以为万事俱备，笔者尝试使用 Android Studio 连接真机进行调试，控制台又突然出现了 apk 签名与当前机器上已安装的 apk 的签名不一致问题，于是又向组长反馈，找到系统内置的签名证书，将 platform.x509.pem 和 platform.pk8 转为 keystore.jks 文件。转换过程参考本篇博客 [AndroidStudio 配置系统签名](https://blog.csdn.net/songqinging/article/details/109469520) ，中间没有踩到坑，不再赘述。在模块 app 下的 build.gradle 下根据实际情况添加如下配置：

```java
android {
    signingConfigs {
        debug {
            //可以配置绝对路径，相对位置以配置文件为基点
            storeFile file('../keystore.jks')
            storePassword 'password'
            keyAlias 'alias'
            keyPassword 'password'
        }
    }
}
```

也可以在 project structure 中进行配置

![image-20220915203050523](http://image.xiaobailx.top/images/202209182151350.png)

经过近一天的折腾，终于能够在真机上调试代码了。



## 总结

第一次摸索中间磕磕绊绊，总算把环境初步搭建起来，之后搞定制化开发就容易得多了，另外还有就是看定制化的 framework 源码也轻松得多。进公司后发现周围的同事基本很少使用 Android Studio 做开发，我也不知道为啥！难道是我太依赖集成开发环境了还是说搞 framework 用不上？



参考博客：[Android Studio 导入自己编译的 framework.jar](https://blog.csdn.net/yzwfeng/article/details/125540849)

参考博客：[android编译 配置-Xbootclasspath/p优先级无效](https://blog.csdn.net/zou249014591/article/details/109752786)

参考博客： [AndroidStudio 配置系统签名](https://blog.csdn.net/songqinging/article/details/109469520)



