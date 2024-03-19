---
layout: post
title:  "Upgrade MySQL from 5.7 to 8.0"
date: 2024-03-19 11:30:00 +0900
abstract: "MySQL"
tags: [MySQL]
image:
toc: true
categories: Tech
last_modified_at: 
published: false
---


MySQL 5.7 에서 MySQL 8 버전으로 업그레이드 할때 고려할 사항들 정리하였습니다. 


### Query Cache

MySQL 5,7.20 부터 query cache 설정이 제거 되었습니다.   
Springframework 에서 query cache 설정을 제거한 커넥터 버전은 5.1.44 입니다.  

![image](/assets/article_images/2024-03-19-Mysql8-Upgrade/MySQL_1.png)
https://dev.mysql.com/doc/refman/5.7/en/query-cache.html

pom.xml 버전을 수정합니다. 
```java 
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.44</version>
        </dependency>
```

mysql-connector-java 5.1.44 이하 버전일때 어플리케이션 구동시 오류 로그입니다. 
```
17:22:46.922 ERROR n.i.willow.service.NoticeService[getListCount:100] - nested exception is org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is java.sql.SQLException: Unknown system variable 'query_cache_size'
### The error may exist in file [/willow/target/ROOT/WEB-INF/classes/db/mapper/NoticeMapper.xml]
### The error may involve net.willow.dao.notice.selectNoticeCount
### The error occurred while executing a query
### Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is java.sql.SQLException: Unknown system variable 'query_cache_size'
```

참고로 

mybatis-config.xml 설정이 있는경우 cacheEnabled의 값이 true로 설정되어 있더라도   
mysql-connector-java 5.1.44 로 변경하면 query cache 설정 항목이 없어지기때문에 어플리케이션 동작에 영향을 주지 않습니다.  

유지보수를 위해서라면 cacheEnabled 값을  false 로 변경해두면 혼란을 방지할 수 있겠네요. 

```xml  
<configuration>
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
</configuration>
```