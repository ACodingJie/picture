> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [brucege.com](https://brucege.com/doc/#/methodNameToSql?id=%e6%b3%a8%e6%84%8f%e7%9a%84%e7%82%b9)

> Description

[根据 mybatis 接口中的方法名生成对应的 mapper sql 并进行方法补全 支持通用 mapper mybatisplus (插件原创功能)](#/methodNameToSql?id=%e6%a0%b9%e6%8d%aemybatis%e6%8e%a5%e5%8f%a3%e4%b8%ad%e7%9a%84%e6%96%b9%e6%b3%95%e5%90%8d%e7%94%9f%e6%88%90%e5%af%b9%e5%ba%94%e7%9a%84mapper-sql%e5%b9%b6%e8%bf%9b%e8%a1%8c%e6%96%b9%e6%b3%95%e8%a1%a5%e5%85%a8-%e6%94%af%e6%8c%81%e9%80%9a%e7%94%a8mapper-mybatisplus-%e6%8f%92%e4%bb%b6%e5%8e%9f%e5%88%9b%e5%8a%9f%e8%83%bd)
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

只需要一个方法名 即可 不需要方法和参数 和返回值 输入方法名后 alt+enter Generate mybatis sql 就可以生成了

![](https://images.brucege.com/findMethodNameToSql.gif)

![](https://images.brucege.com/updateMethodNameToSql.gif)

![](https://images.brucege.com/deleteMethodNameToSql.gif)

![](https://images.brucege.com/countMethodNameToSql.gif)

[方法名生成 sql 时支持 if test (2.3 版本后无需配置 直接 alt+enter Generate mybatis sql with if test 即可)](#/methodNameToSql?id=%e6%96%b9%e6%b3%95%e5%90%8d%e7%94%9f%e6%88%90sql%e6%97%b6%e6%94%af%e6%8c%81if-test-23%e7%89%88%e6%9c%ac%e5%90%8e%e6%97%a0%e9%9c%80%e9%85%8d%e7%bd%ae-%e7%9b%b4%e6%8e%a5altenter-generate-mybatis-sql-with-if-test%e5%8d%b3%e5%8f%af)
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

![](https://images.brucege.com/findWithIfTest.gif)

[一键生成 findByAll](#/methodNameToSql?id=%e4%b8%80%e9%94%ae%e7%94%9f%e6%88%90findbyall)
------------------------------------------------------------------------------------

![](https://images.brucege.com/findByAllEE.gif)

[一键生成 insertList](#/methodNameToSql?id=%e4%b8%80%e9%94%ae%e7%94%9f%e6%88%90insertlist)
--------------------------------------------------------------------------------------

![](https://images.brucege.com/insertList.gif)

[支持生成 mybatisplus 的 queryWrapper](#/methodNameToSql?id=%e6%94%af%e6%8c%81%e7%94%9f%e6%88%90-mybatisplus%e7%9a%84querywrapper)
-----------------------------------------------------------------------------------------------------------------------------

![](https://images.brucege.com/MethodNameToMybatisplusWrapper.gif)

[使用方法](#/methodNameToSql?id=%e4%bd%bf%e7%94%a8%e6%96%b9%e6%b3%95)
-----------------------------------------------------------------

*   在 mybatis 的接口文件上的方法名 (只需要一个名字，不需要返回值和参数 会自动推导出来) 上使用 alt+enter Generate mybatis sql 或者选中方法名右键来生成

[方法名的规则](#/methodNameToSql?id=%e6%96%b9%e6%b3%95%e5%90%8d%e7%9a%84%e8%a7%84%e5%88%99)
-------------------------------------------------------------------------------------

数据库对象 User

<table><thead><tr><th>字段名</th><th>类型</th></tr></thead><tbody><tr><td>id</td><td>Integer</td></tr><tr><td>userName</td><td>String</td></tr><tr><td>password</td><td>String</td></tr></tbody></table>

表名为 user

xml 中对应的 resultMap 为

```
<resultMap id="AllCoumnMap" type="com.codehelper.domain.User">
    <result column="id" property="id"/>
    <result column="user_name" property="userName"/>
    <result column="password" property="password"/>
</resultMap>
```

以下是方法名与 sql 的对应关系 (方法名的大小写无所谓)

可以跟在字段后面的比较符有

<table><thead><tr><th>比较符</th><th>生成 sql</th><th>开始支持的版本号</th></tr></thead><tbody><tr><td>between</td><td>prop &gt; {} and prop &lt;{}</td><td>v1.3</td></tr><tr><td>betweenOrEqualto</td><td>prop &gt;={} and prop &lt;={}</td><td>v1.3</td></tr><tr><td>lessThan</td><td>prop &lt;{}</td><td>v1.3</td></tr><tr><td>lessThanOrEqualto</td><td>prop &lt;={}</td><td>v1.3</td></tr><tr><td>greaterThan</td><td>prop &gt; {}</td><td>v1.3</td></tr><tr><td>greaterThanOrEqualto</td><td>prop &gt;={}</td><td>v1.3</td></tr><tr><td>isnull</td><td>prop is null</td><td>v1.3</td></tr><tr><td>notnull</td><td>prop is not null</td><td>v1.3</td></tr><tr><td>like</td><td>prop like {}</td><td>v1.3</td></tr><tr><td>in</td><td>prop in {}</td><td>v1.3</td></tr><tr><td>notin</td><td>prop not in {}</td><td>v1.3</td></tr><tr><td>not</td><td>prop != {}</td><td>v1.3</td></tr><tr><td>notlike</td><td>prop not like {}</td><td>v1.3</td></tr><tr><td>startingWith</td><td>prop like {}%</td><td>v1.6.0</td></tr><tr><td>endingWith</td><td>prop like %{}</td><td>v1.6.0</td></tr><tr><td>containing</td><td>prop like %{}%</td><td>v1.6.0</td></tr><tr><td>true</td><td>prop = true</td><td>2.0</td></tr><tr><td>false</td><td>prop = false</td><td>2.0</td></tr><tr><td>lessThanEqual</td><td>prop &lt;= {}</td><td>2.0</td></tr><tr><td>greaterThanEqual</td><td>prop &gt;={}</td><td>2.0</td></tr></tbody></table>

*   find 方法 可以使用 select query get 替代 find 开头

支持获取多字段，by 后面可以设置多个字段的条件 一个字段后面只能跟一个比较符 支持 orderBy,distinct, findFirst

<table><thead><tr><th>方法名</th><th>sql</th></tr></thead><tbody><tr><td>find</td><td>select * from user</td></tr><tr><td>findUserName</td><td>select user_name from user</td></tr><tr><td>findById</td><td>select * from user where id = {}</td></tr><tr><td>findUserNameById</td><td>select user_name from user where id = {}</td></tr><tr><td>findByIdGreaterThanAndUserName</td><td>select * from user where id &gt; {} and user_name = {}</td></tr><tr><td>findByIdGreaterThanOrIdLessThan</td><td>select * from user where id &gt; {} or id &lt; {}</td></tr><tr><td>findByIdLessThanAndUserNameIn</td><td>select * from user where id &lt;{} and user_name in {}</td></tr><tr><td>findByUserNameAndPassword</td><td>select * from user where user_name = {} and password = {}</td></tr><tr><td>findUserNameOrderByIdDesc</td><td>select user_name from user order by id desc</td></tr><tr><td>findDistinctUserNameByIdBetween</td><td>select distinct(user_name) from user where id &gt;= {} and id &lt;={}</td></tr><tr><td>findOneById</td><td>select * from user where id = {}</td></tr><tr><td>findFirstByIdGreaterThan</td><td>select * from user where id &gt; {} limit 1</td></tr><tr><td>findFirst20ByIdLessThan</td><td>select * from user where id &lt;{} limit 20</td></tr><tr><td>findFirst10ByIdGreaterThanOrderByUserName</td><td>select * from user where id &gt; {} order by user_name limit 10</td></tr><tr><td>findMaxIdByUserNameGreaterThan</td><td>select max(id) from user where user_name &gt; {}</td></tr><tr><td>findMaxIdAndMinId</td><td>select max(id) as maxId, min(id) as minId from user</td></tr><tr><td>findByAll</td><td>select with all column if test</td></tr><tr><td>findByAllExceptCreateBetween</td><td>select with all column if test except date between</td></tr></tbody></table>

*   update 方法 by 后面设置的条件同上 可以使用 modify 替代 update 开头

<table><thead><tr><th>方法名</th><th>sql</th></tr></thead><tbody><tr><td>updateUserNameById</td><td>update user set user_name = {} where id = {}</td></tr><tr><td>updateUserNameAndPasswordByIdIn</td><td>update user set user_name = {} and password = {} where id in {}</td></tr><tr><td>updateIncVersionByIdAndVersion</td><td>update user set version = version+1 where id = {} and version = {}</td></tr></tbody></table>

*   delete 方法 可以使用 remove 替代 delete 开头 by 后面设置的条件同上

<table><thead><tr><th>方法名</th><th>sql</th></tr></thead><tbody><tr><td>deleteById</td><td>delete from user where id = {}</td></tr><tr><td>deleteByUserNameIsNull</td><td>delete from user where user_name is null</td></tr></tbody></table>

*   count 方法 by 后面设置的条件同上 支持 distinct

<table><thead><tr><th>方法名</th><th>sql</th></tr></thead><tbody><tr><td>count</td><td>select count(1) from user</td></tr><tr><td>countDistinctUserNameByIdGreaterThan</td><td>select count(distinct(user_name)) from user where id &gt; {}</td></tr></tbody></table>

[注意的点](#/methodNameToSql?id=%e6%b3%a8%e6%84%8f%e7%9a%84%e7%82%b9)
-----------------------------------------------------------------

*   使用方法名生成 sql 需要在接口中提供一个 insert 或 save 或 add 方法并以表对应的 java 对象为第一参数 (类似于 insert(User user) 需要通过 User 来进行方法名的推断) 使用了通用 mapper 或者 mybatisplus 的话 不需要 insert 方法
    
*   2.7.5 以上版本方法名生成 sql 不再依赖于 insert 方法 但需要一个 BaseResultMap 里面包含 @Table 注释，可以在表上右键使用 mybatis generator 左下角有个预览 xml 来生成好
    
*   使用方法名生成的 sql 的字段会从数据库对象对应的 resultMap 中的数据库字段来设置。
    
*   "please check with your resultMap dose it contain all the property of 此时可以检查这个接口对应的对象 比如 这个接口有个 insert(User user) 即 User 对象 是否有一个对应的完整的 resultMap 在 xml 中，resultMap 中少了属性的话，无法生成 如果属性在表里面有，请将属性添加到 resultMap(一般为 baseResultMap) 如果没有 可以将属性设置为 transient 类型 即 private transient String moreField 或者添加 @javax.persistence.Transient 注解 如果找不到该注解 请添加 maven 依赖
    
    ```
    <dependency>
      <groupId>javax.persistence</groupId>
      <artifactId>javax.persistence-api</artifactId>
      <version>2.2</version>
    </dependency>
    ```
    
*   当生成 sql 时 如果比如 UserMapper 对应的 User 对象中含有 List 或 Set 类型的属性时，sql 会无法生成 请将这些属性设置为 transient 类型 比如 private transient List 或者最好把这些属性移到一个继承的类中
    
*   我写了 find 后方法名没有自动提示如何处理?
    

查看 IDEA 设置里的 completion 是否设置为 firstLetter ![](https://images.brucege.com/completeSetting.png)