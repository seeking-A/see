# Android入门

## Gradle

### 安装配置

[Linux下安装gradle](http://t.zoukankan.com/guoapeng-p-10632275.html)

### 构建文件和创建任务

1. 默认构建文件为 ```build.gradle```， 构建是可使用 ```-b``` 替代 ```--build```

```shell
gradle -b <fileName>
```

2. .gradle 文件夹存放的是 ```Gradle``` 的构建信息

3. ```Gradle``` 采用领域对象模型，每个```Project``` 维护一个```TaskContain``` 类的```task``` 属性，```Taskl```代表需要执行的任务，允许指定依赖的任务、任务类型，可通过```configure()```方法配置任务，提供了```doFirst()```、```doLast()``` 方法来添加```Action```，```Action``` 和```Closure```对象都可代表```Closure```对象都可以是```Action```。

4. 为```Gradle```构建文件创建```Task```的常用方法

   1. 调用 ```project.task()```
   2. 调用 ```TaskContainer.crate()```

   无论使用哪种方式创建```Task```，通常都可为``` Task```指定以下3种属性：

   1. dependsOn：指定```Task```所依赖的其它```Task```

   2. type：指定该```Task```的类型

   3. 通过传入的代码块参数配置

5. ```Gradle```构建过程
   1. 配置阶段
   2. 按依赖关系执行指定```Task```
6. ```Gradle```编译过程

```groovy
//应用名为java的插件，主要是为了引入JavaCompile、JavaExec两个任务
apply plugin: 'java'
task compile(type:javaCompile){
    source = filetree('src/main/java')
    classpath = sourceSets.main.compileClassPath
    destinationDir = file('build/classes/main')
    options.fork = true
    options.incremental = true
}
//指定任务类型为JavaExec
task run(type:JavaExec,dependsOn:'compile'){
    classPath = sourceSets.main.runtimeClassPath
    //指定主类为lee.HelloWorld
    main = 'lee.HelloWorld'
}
```

编译运行

```shell
gradle run
```



### Gradle的属性定义

#### 1. 为属性指定属性值

```groovy
//为Project内置属性指定属性值
version = 1.0.0
task showProps{
	description = 'task'
}
```

Project 常用的属性
1. name：项目名称
2. path：项目绝对路径
3. description：项目描述路径
4. buildDir：项目的构建结果的存放路径
5. version： 项目版本号

#### 2. 通过 ext 添加属性

````groovy
//使用ext方法传入代码来设置属性
ext{
	prop1 = 'prop1'
	prop2 = 'prop2'
}
task showAddPros{
	ext{
		prop3 = 'prop3'
		prop4 = 'prop4'
	}
}
````

#### 3. 通过-P选项添加属性

```shell
gradle -P prop='prop-value' <task-name>
```

#### 4. 通过JVM参数添加属性

```shell
gradle -D org.gradle.project.prop= 'prop-value' <task-name>
```



### 增量式构建

```Gradle```通过任务的输入、输出部分是否发生改变来判断该任务是否需要重新执行，```Task```使用```TaskInputs```类型```inputs```属性来代表输入，使用```TaskOutputs```类型```outPuts```属性来代表输出。

```groovy
task fileContentCopy{
	//定义代表source目录的文件集
	def sourceTxt = fileTree("source")
	def dest = file('dest.txt')
	//定义任务的输入和输出
	inputs.dir sourceTxt	//指定输出目录为sourceTxt
	outputs.file dest		//指定输出目录为dest
}
```



### Gradle 插件机制

应用插件相当于引入了该插件包含的所有任务类型、任务、属性等，这样```Gradle```就可以执行插件种预定义的任务。

```groovy
apply plugin: <plugin-name>
```

```shell
#查看构建文件支持的所有任务
apply tasks --all
```

**通过```resource```项目添加第三方或额外的依赖源码**

```groovy
//配置被依赖的源代码路径
sourceSets{
	fkframework
}
```

```Gradle```自动为为每个新建的```sourceSets```创建相应的```Task```，包括```compilexxxJava```，```processxxxResource```和```xxxClasses```这三个```Task```。

```groovy
//配置compileJava任务依赖compilexxxJava任务
compileJava.dependsOn compilexxxJava
//将第三方项目字节码的存储路径添加到系统编译时、运行时的类路径中
sourceSets{
    main{
        compileClassPath = compileClassPath + files(xxx.output.classesDir)
    }
    test{
        //将xxx生成的字节码文件的存储路径添加到运行时的类路径中
        runtimeClassPath = runtimeClassPath + files(xxx.output.classDir)
    }
}
```

### 依赖管理

Gradle 配置依赖步骤：

1. 为```Gradle```配置仓库

```groovy
//定义仓库
reposities{
	//使用Maven默认的中央仓库
	mavenCentral()
	//使用远程仓库
	url "http://repo2.maven.org/maven2"
	//显示指定本地磁盘路径作为maven仓库url
	url "g:/abc"
}
```

2. 为不同组配置依赖的```jar```包

```groovy
//使用configurations配置组
configrations{
    //配置名为xxx的依赖组
    xxx
}
dependencies {
    //配置依赖的jar包
    //为xxx依赖组添加了commons-logging 1.2的jar包
    xxx group: 'commons-logging',name:'commons-logging',version:'1.2'
    //简写
    xxx 'commons-logging:commons-logging:1.2'
}
```



## ADB

常用命令：

```shell
 #查看当前运行的模拟器
 abd devices
 #电脑与手机之间文件的相互复制
 abd push <file-path> <targer-path>
 #启动模拟器的shell窗口
 adb shell
 #安装、卸载APK程序
 adb install [-r] [-s] <file>
 #卸载APK程序
 adb uninstatll [-k] <file>
```



## 开发Android应用

### 开发流程

1. 创建项目
2. 编写布局文件
3. 编写```java```逻辑代码

### 项目结构

```AndroidMainfest.xml``` 文件主要内容：

```xaml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.topwisesz.helloworldapp">
    
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.HelloWorldApp"
        tools:targetApi="31">
        <!--定义一个Android应用的一个组件-->
        <activity android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <!--制定该Activity是程序入口-->
                <action android:name="android.intent.action.MAIN" />
                <!--制定运行时加载该应用时运行该Activity-->
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```



## Android基本组件

常用组件：VIew、Activity、Service、BroadcastReceiver、ContentProvider、Intent、IntentFilter

四大组件：Activity、Service、BroadcastReceiver、ContentProvider

### View和Activity

```View```是所有```UI```控件的基类，```Activity```用于显示```View```

```java
//创建一个线性布局管理器
LinearLayout linearLayout = new LinearLayout(this);
//设置该Activity显示线性布局
setContentView(linearLayout);
//设置该Activity显示在main.xml定义的view
setContentView(R.layout.main);
```

### BroadcastReceiver

```BroadcastReceiver```类似于一个全局监听器，用于接受广播消息

使用```BroadcastReceiver```接受消息步骤：

1. 继承BroadcastReceiver重写OnReceive()方法

```java
public class DefineBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        System.out.println("接受消息");
    }
}
```

2. 发送广播消息

```
sendBroadcast();
sendstickBroadcast();
sendOrderedcast();
```

3. 通过```Intentfilter```配置```BroadcastReceiver```需接受的消息
4. 注册```BroadcastReceiver```
   - 通过代码```Context.registReceiver()```方法注册```BroadcastReciver```
   - 在```AndroidMainfest.xml```文件中使用```<receiver.../>```元素完成注册

### ContentProvider

```ContentProvider``` 可以实现不同应用之间的数据交换

```java
//插入数据
insert(url,contentValues)
//删除指定数据
delete(url,contentValues)
//更新指定数据
update(url,contentValues,String,String[])
//查询数据
query(url,String[],String,String[],String)
```

### Intent和IntentFilter

```Intent```作为```Android```应用内不同之间通信的载体，```Activity```、```Service```、```BroadcastReceiver```三种组件都是以```Intent```作为通信主题；

````java
//启动Activity
context.startActivity(Intent intent);
context.startActivityForResult(Intent intent, int requestCode);
//启动Service
context.startService(Intent intent);
bindService(Intent service,ServiceConnection conn,int flags);
//启动BroadcastReceiver
sendBroadcast(Intent intent);
sendStickyBroadcast(Intent intent);
````

- 显式```Intent```：明确指定需要启动或触发的组件应满足的类名
- 隐式```Intent```：只是指定需要启动或触发的组件应满足的条件

```IntentFilter```用于声明```Intent```所满足的条件。

