# 入门-SqlSessionFactory

## 1. Introduction

![](../../.gitbook/assets/image%20%282%29.png)

* `SqlSessionFactoryBuilder`
  * 职责：创建`SQLSessionFactory`
  * 根据XML构建：`InputStream`
  * 根据Java构建：`Configuration`
* `SqlSessionFactory`
  * 职责：以多种重载执行`openSession`
  * `openSessionFromDataSource，openSessionFromConnection`
  * 返回`SqlSession`
* SqlSession
  * 职责：执行SQL语句，获取Mapper，管理事务，线程不安全的
  * `selectOne,selectList,selectMap`
  * `insert, update, delete`
  * `commit, rollback`
  * `getMapper`
* Configuration
  * 职责：整个核心配置，上下文
  * 只有两个构造函数：无参数和传递`Environment`的

## 2. SqlSession示例

### 2.1 XML方式

根据XML创建SqlSessionFactory：

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
                                      .build(inputStream);
```

对应的`Configuration` XML文件：

```markup
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

对应的`Mapper`文件：

```markup
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

执行：

```java
package org.mybatis.example;
public interface BlogMapper {
  Blog selectBlog(int id);
}

try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

### 2.2 Java（ 注解\)方式

SqlSessionFactory：

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", 
                 transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
                 .build(configuration);

```

执行：

```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}

//新版
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```





