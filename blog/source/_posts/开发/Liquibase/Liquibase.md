---
title: 学习Liquibase
tags:
  - 数据库管理
  - Liquibase
categories:
  - - 数据库管理
  - - Liquibase
series: Liquibase
description: hello liquibase
top_img: false
cover: 'https://global.discourse-cdn.com/business7/uploads/liquibase1/original/1X/d22b9ff3fb6422458a8c0de80248a3a68eadc8c6.jpeg'
abbrlink: f7dc1405
date: 2024-09-23 10:28:30
---

![Liquibase](https://global.discourse-cdn.com/business7/uploads/liquibase1/original/1X/d22b9ff3fb6422458a8c0de80248a3a68eadc8c6.jpeg)

# 初入[Liquibase](https://blog.csdn.net/weixin_41404773/article/details/106355563)

### 一、与SpringBoot集成

#### 1、引入依赖：

```
<!--Liquibase-->
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
</dependency>
```

#### 2、配置LiquiBaseConfig

```
@Configuration
public class LiquibaseConfig {
 
    @Bean
    public SpringLiquibase liquibase(DataSource dataSource) {
        SpringLiquibase liquibase = new SpringLiquibase();
        liquibase.setDataSource(dataSource);
        //指定changelog的位置，这里使用的一个master文件引用其他文件的方式
        liquibase.setChangeLog("classpath:liquibase/master.xml");
        //liquibase.setContexts("development,test,production");
        liquibase.setShouldRun(true);
        return liquibase;
    }
 
}
```

#### 3、xml文件中属性讲解：

```xml
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
         http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">
 
       <changeSet id="20190713-02" author="solo">
        <createTable tableName="dh_project_category" remarks="项目类型表">
            <column name="id" type="varchar(64)" remarks="项目类型id">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="name" type="varchar(255)" remarks="类目类型名称"/>
            <column name="status" type="int(11)" remarks="状态。1正常，2删除"/>
            <column name="remark" type="varchar(255)" remarks="备注"/>
        </createTable>
    </changeSet>
</databaseChangeLog>
```

**Id：唯一id       Author：修改人**

```xml
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
         http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">
 
    <!--
    1：includeAll 标签可以把一个文件夹下的所有 changelog 都加载进来。如果单个加载可以用 include。
    2：includeAll 标签里有两个属性：path 和 relativeToChangelogFile。
        2.1：path （在 include 标签里是 file）：指定要加载的文件或文件夹位置
        2.2：relativeToChangelogFile ：文件位置的路径是否相对于 root changelog 是相对路径，默认 false，即相对于               classpath 是相对路径。	
    -->
 
    <includeAll path="liquibase/changelog/" relativeToChangelogFile="false"/>
 
</databaseChangeLog>
```

### 二、基于liquibase对数据库的操作

#### 1、创建表

```xml
<changeSet id="20190713-01" author="solo">
    <createTable tableName="project_info">
        <column name="project_id" type="varchar(64)" encoding="utf8" remarks="项目id">
            <constraints primaryKey="true" nullable="false"/>
        </column>
        <column name="project_name" type="varchar(255)" encoding="utf8" remarks="项目名字"/>
    </createTable>
</changeSet>
```

<`createTable`>创建表标签   `tableName` 表名     

<`column`>  字段标签  `name`：字段名称  `type`：字段类型   `encoding`：编码类型  `remarks`：字段注释

<`constraints`>主键标签   `primaryKey`：是否主键   `Nullable`：是否为空

项目启动会自动在数据库生成`project_info`表

#### 2、修改表-添加列

```xml
<changeSet id="20190713-01" author="yunshan">
<addColumn tableName="project_info">
    <column name="address"  type="varchar(255)" encoding="utf8" remarks="地址"/>
</addColumn>
</changeSet>
```

<`addColumn`>  `tableName` 修改表名

<`column`>  字段标签  ： `name`：字段名称    `type`：字段类型  

==注==：1：同一个文件中，`changeSet`中的id不能重复，

​        2： 每次数据库变更，都会在`databasechangelog`生成一条记录

#### 3、修改表-删除列

```xml
<changeSet id="20190713-03" author="yunshan">
    <dropColumn tableName="project_info" columnName="address"/>
</changeSet>
```

#### 4、修改表-添加数据

```xml
<changeSet id="20190713-061" author="yunshan">
    <insert tableName="project_info">
        <column name="project_id" valueNumeric="312223"/>
        <column name="project_status" valueNumeric="322434343"/>
        <column name="project_name" value="wkn"/>
    </insert>
</changeSet>
```

<`inSert`>   数据插入标签

   <`column`>  字段标签，`name`：字段名称   `valueNumeric`：对应的字符值

#### 5、修改表-通过sql脚本

```
 <changeSet id="20200522-02" author="yunshan">
    <sqlFile path="liquibase/sql/project_info.sql"/>
</changeSet>
```

<`sqlFile`>标签，指定需要加载的sql路径 （相对路径）

### 扩展：

#### [`liquibase`](https://blog.csdn.net/u010227394/article/details/111186651)：（实用小文章）

##### 1、将已有的字段进行类型的变更：

```Java
	//原来的类型
	<column name="unique_key" remarks="唯一标识" type="varchar(100)">
				<constraints nullable="false" primaryKey="true" />
	</column>
	
	//变更的语句modifyDataType标签
	<changeSet id="alter_customer_batch_20240914141700" author="songqi.xiao">
		<modifyDataType tableName="bfs_customer_batch_info" columnName="unique_key" newDataType="int"/>
	</changeSet>
```

##### 2、对已有的主键设置自增：

```Java
//标签addAutoIncrement
<changeSet author="songqi.xiao" id="addAutoIncrement_20240914145400">
		<addAutoIncrement tableName="bfs_customer_batch_info" generationType="ALWAYS"  columnName="unique_key" columnDataType="int"  />
	</changeSet>
```

