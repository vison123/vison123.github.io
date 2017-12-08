---
layout: post
title:  Android SQLite 使用
date:   2017-07-06 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:  "Vison"
catalog: true
tags:
    - 数据库
    - Android
---

### SQLite简介
SQLite是一款轻量级的关系型数据库。支持标准的SQL语法，还遵循数据库的ACID事务。相比较Realm等数据库，我更倾向于SQLite，大多数开发者
对SQL语法较为熟悉，上手较快。另一方面，Realm数据库虽然较为轻量，但在事务方面的表现不如SQLite，Android又在SDK中集成了SQLite，
使用起来不需要额外依赖。所以至少在Android平台上，SQLite数据库是第一选择。

### 创建数据库
Android提供了SQLiteOpenHelper工具类，只需要新建一个类继承SQLiteOpenHelper，并实现onCreate和onUpgrade方法即可。
为了以后数据库升级和App各版本兼容考虑，onCreate方法中应该只有第一个稳定版本的数据库建表语句，然后调用onUpgrade方法升级到当前数据库版本。
数据库的升级代码都应该在onUpgrade方法中，这样可以保证用户无论安装任何版本都会从基础数据库开始升级，直至升级到当前版本。

```java
public class SqLiteHelper extends SQLiteOpenHelper {


    private static final String DATABASE_NAME  = "finance.db"; //数据库名称

    private static final int FIRST_DATABASE_VERSION  = 1; //初始数据库版本

    private static final int DATABASE_VERSION  = 3; //当前数据库版本

    public SqLiteHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    //实现方法  是一个回调方法
    //在创建数据库时调用
    //什么时候创建数据库：连接数据库的时候，如果数据库文件不存在
    //只调用一次
    public void onCreate(SQLiteDatabase db) {
        createTable(db);
        onUpgrade(db, FIRST_DATABASE_VERSION, DATABASE_VERSION)
    }

    //升级数据库，使用for实现跨版本升级数据库
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        for (int i = oldVersion; i < newVersion; i++) {
            switch (i) {
            case 1:
                upgradeToVersion2(db);
                break;
            case 2:
                upgradeToVersion3(db);
                break;
            default:
                break;
            }
        }
    }

    private void createTable (SQLiteDatabase db) {
        db.execSQL(DbProject.CREATE_PROJECT_TABLE);
        db.execSQL(DbCategory.CREATE_CATEGORY_TABLE);
        db.execSQL(DbMoney.CREATE_MONEY_TABLE);
    }

    private void upgradeToVersion2(SQLiteDatabase db){
        // Project表新增1个字段
        String sql = "ALTER TABLE " + DbProject.DATABASE_TABLE_PROJECT + " ADD COLUMN is_deleted VARCHAR";
        db.execSQL(sql);
    }

     private void upgradeToVersion3(SQLiteDatabase db){
        // Category表新增1个字段
        String sql = "ALTER TABLE " + DbCategory.DATABASE_TABLE_CATEGORY + " ADD COLUMN is_deleted VARCHAR";
        db.execSQL(sql);
    }

}
```

### 升级数据库
  数据库升级主要有两种情况：
   * 从未安装过该App
     直接执行SQLitehelper 中的onCreate()方法
     onCreate()方法中只执行第一个版本的数据库脚本，然后手动调用onUpgrade()，onUpgrade()中进行for循环和switch...case
     for循环是进行数据库的连续升级，switch...case根据版本号执行相应的升级方法；
   * 安装过老版本App（更新App）
     执行SQLitehelper 中的onUpgrade()方法, 同样进行循环升级；

### 备份数据库
   如果遇到复杂的修改操作，比如在修改的同时，需要进行数据的转移，那么可以采取在一个事务中执行如下语句来实现修改表的需求。

   * 将表名改为临时表
         ALTER TABLE t_project RENAME TO t_temp_project;

   * 创建新表
        CREATE TABLE t_project (OrderId VARCHAR(32) PRIMARY KEY ,UserName VARCHAR(32) NOT NULL ,ProductId VARCHAR(16) NOT NULL);
　　
   * 导入数据
        INSERT INTO t_project SELECT OrderId, “”, ProductId FROM t_temp_project;
　       (注意 双引号”” 是用来补充原来不存在的数据的)
　　
   * 删除临时表　　
        DROP TABLE t_temp_project;

通过以上四个步骤，就可以完成旧数据库结构向新数据库结构的迁移，并且其中还可以保证数据不会应为升级而流失。

### 提升数据库版本号
  数据库版本若没有提升到最新，下次启动应用还会进行升级，影响效率及对数据库数据造成影响
