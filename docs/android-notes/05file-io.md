# Android数据存储与IO

## SharedPreferences与Editor

SharedPreferences 接口负责读取应用程序的 Preference 数据，常用的方法如下：

```java
//判断SharePreferences是否包含key
boolean contains(String key);
//获取SharePreferences中的所有数据
Map<String,Object> getAll();
//获取SharePreferences数据里指定key的value
getXxx(String key);
```

SharedPreferences 接口没有提供写入数据的能力，通过 SharePreferences.Editor 才允许写入。Editor 常用方法如下：

```java
//清空SharedPreferences里所有数据
SharedPreferences.Editor.clear();
//向SharedPreferences中存入指定key对应的数据
SharedPreferences.Editor.remove(String key);
//向SharePrefereneces中删除指定key的对应数据
SharedPreferences.Editor.putXxx(String key,Xxx value);
//非阻塞提交修改
boolean apply();
//阻塞提交修改
boolean commit();
```



## File存储

### openFileInput和openFileOutput

Java基础回顾：[Java IO流详解](https://blog.csdn.net/Wyunpeng/article/details/122850878?spm=1001.2014.3001.5502)

```java
//打开应用的数据文件夹下的name文件对应的输入流
FileInputStream openFileInput(String name);
//打开应用的数据文件夹下的nane文件对应的输出流
FileOutputStream openFileOutput(String name);
```

四种文件打开模式：

```java
MODE_PRIVATE:只被当前程序读写;
MODE_APPEND:以追加方式打开文件;
//不在推荐使用
MODE_WORLD_READABLE:该文件内容可以被其他程序读取;
MODE_WORLD_WRITEABLE:该文件内容可以被其他程序读取
```



### 读写SD卡文件

1. 请求动态获取读写 SD 卡的权限，只有当用户授权读写 SD 卡时才执行读写

```java
requestPremissions(new int[]{Mainfest.permisssion.WRITE_EXTEPNAL_STORQAGE},0x123);
```

2. 调用 Environment 的 getExternalStorageDirectory() 方法来获取外部存储器，即 SD 卡目录；

```java
Environment.getExternalStorageDirectory().getPath();
```

3. 使用 FileInputStream、FileOutputStream、FileReader 或 FileWriter 读写 SD 卡里的文件。



## SQLite数据库

SQLite 是一个嵌入式的数据库引擎，允许开发者使用 SQL 语句操作数据库中的数据。SQLite 数据库只是一个文件。

### SQLiteDatabase

#### 打开数据库

```java
//打开path文件所代表的SQLite数据库
static SQLiteDatabase openDatabase(String path,SQLiteDatabase.CursorFactory factory,int falgs);
//打开或创建file文件所代表的SQLite数据
static SQLiteDatabase openDatabase(File file,SQLiteDatabase.CursorFactory factory,int falgs);
//打开或创建path文件所代表的SQLite数据
static SQLiteDatabase openDatabase(String path,SQLiteDatabase.CursorFactory factory,int falgs);
```



#### 操作数据库

```java
//执行带占位符的SQL语句
execSQL(String sql,Object[] bindArgs);
//执行SQL
execSQL(String sql);
//向指定表中插入数据
insert(String table,String nullColumnHack,ContenValues values);
//更新指定表的数据
update(String table,ContentValues value,String whereClause,String[] whereArgs);
//删除指定表的数据
delete(String table,String whereCluse,String whereArgs);
//查询指定表的数据
Cursor query(String table,String[] colums,String whereColums,String[] whereArgs,String groupBy,String having,String orderBy);
//查询指定表的分页数据
Cursor query(String table,String[] colums,String whereColums,String[] whereArgs,String groupBy,String having,String orderBy，String limit);
//查询指定表的分页数据并去重
Cursor query(boolean distinct, String table,String[] colums,String whereColums,String[] whereArgs,String groupBy,String having,String orderBy，String limit);
//执行带占位符的SQL查询
rawQuery(String sql,String[] selectionArgs);
```



#### Cursor移动操作

```java
//将指针向上或向下移动offset行，正下负上
move(int offset);
//将指针移至第一行，移动成功返回true
boolean moveToFirst();
//将指针移至第最后一行，移动成功返回true
boolean moveToLast();
//将指针移至下一行，移动成功返回true
boolean moveToNext();
//将指针移至指定行，移动成功返回true
boolean moveToPosition();
//将指针移至上一行，移动成功返回true
boolean moveToPrevious();
```



#### 事务操作

```java
//开始事务
beginTransaction();
//结束事务
endTransaction();
//若当前上下文处于事务中，返回true，否则返回false
inTransaction();
//设置事务标识
setTransactionSuccessful();
```



### SQLiteOpenHelper

```java
//以读写方式打开数据库对应的SQLiteDatabase对象
synchronized SQLiteDatabase getReadableDatabase();
//以写的方式打开数据库对应的SQLiteDatabase对象
synchronized SQLiteDatabase getWritable();
//当第一次创建数据库时回调该方法
abstract void onCreate(SQLiteDatabase db);
//关闭所有打开的SQLiteDatabase对象
abstract void onUpdate(SQLiteDatebase db, int oldVersion, int newVersion);
```