### Spring boot 게시판 만들기- MySQL 연동하기

---

#### 1. DB 만들기 

MySQL에 워크벤치로 develop스키마를  새로 만들고 board 테이블을 만들어 줌.



charset : utf-8mb4

Collation : Default Collation

```sql
use board;
CREATE TABLE tb_board(
idx INT NOT NULL AUTO_INCREMENT COMMENT '번호(PK)',
title VARCHAR(100) NOT NULL COMMENT '제목',
content VARCHAR(3000) NOT NULL COMMENT '내용',
writer VARCHAR(20) NOT NULL COMMENT '작성자',
view_cnt INT NOT NULL DEFAULT 0 COMMENT '조회 수',
notice_yn ENUM('Y','N') NOT NULL DEFAULT 'N' COMMENT '공지글 여부',
secret_yn ENUM('Y','N') NOT NULL DEFAULT 'N' COMMENT '비밀글 여부',
delete_yn ENUM('Y','N') NOT NULL DEFAULT 'N' COMMENT '삭제 여부',
insert_time DATETIME NOT NULL DEFAULT NOW() COMMENT '등록일',
update_time DATETIME NULL COMMENT '수정일',
delete_time DATETIME NULL COMMENT '삭제일',
PRIMARY KEY(idx)
) COMMENT '게시판';
```



#### 2. 데이터 소스 설정하기 

스프링 부트에서 데이터 소스의 설정은 두 가지 방법이 있음. 

@Bean 애너테이션 또는 application.properties 파일을 이용할 수 있음. 

src/main/resources 디렉터리의 application.properties 파일을 열고 다음의 코드를 입력. 

```java
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.jdbc-url=jdbc:mysql://localhost:3306/board?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.hikari.username=root
spring.datasource.hikari.password=비밀번호
spring.datasource.hikari.connection-test-query=SELECT NOW() FROM dual
```

| 속성                  | 설명                                                         |
| --------------------- | ------------------------------------------------------------ |
| jdbc-url              | 데이터베이스의 주소를 의미.<br /> 포트 번호 뒤의 board 앞에서 생성한 스키마의 이름.<br /> severTimezone 등의 파라미터는 한글 등의 기본적인 설정을 처리하는 용도. |
| username              | MySQL을 설치하는 과정에서 입력했던 아이디를 의미.            |
| password              | MySQL을 설치하는 과정에서 입력했던 패스워드를 의미.          |
| connection-test-query | 커넥션이 정상적으로 맺어졌는지 확인하기 위한 SQL 쿼리 문.  애플리케이션이 실행되면, 다음의 테스트 쿼리가 콘솔에 출력. 아직은 애플리케이션이 실행되지 않는 것이 정상입니다. |



#### 3. 패키지 & 클래스 추가하기

src/main/java 디렉터리의 com.board 패키지에 configuration 패키지를 추가하고, configuration 패키지에 DBConfiguration 클래스를 생성하기 

구성, 설정 파일 만들기 

원래는 XML을 통해서 Bean(POJO)을 등록했는데 규모가 커질수록 번거롭다보니 어노테이션을 이용해 등록하는 방법이 나왔음. 

![img](https://blog.kakaocdn.net/dn/bQGucK/btqCymS4ghs/y5l9D1fikVwHw5PakV8Iu1/img.png)

코드작성. 

```JAVA
package com.board.configuration;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

@Configuration
@PropertySource("classpath:/application.properties")
public class DBConfiguration {

	@Autowired
	private ApplicationContext applicationContext;

	@Bean
	@ConfigurationProperties(prefix = "spring.datasource.hikari")
	public HikariConfig hikariConfig() {
		return new HikariConfig();
	}

	@Bean
	public DataSource dataSource() {
		return new HikariDataSource(hikariConfig());
	}

	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
		factoryBean.setDataSource(dataSource());
//		factoryBean.setMapperLocations(applicationContext.getResources("classpath:/mappers/**/*Mapper.xml"));
		return factoryBean.getObject();
	}

	@Bean
	public SqlSessionTemplate sqlSession() throws Exception {
		return new SqlSessionTemplate(sqlSessionFactory());
	}

}
```



| **애너테이션**           | **설명**                                                     |
| ------------------------ | ------------------------------------------------------------ |
| @Configuration           | 스프링은 @Configuration이 지정된 클래스를 자바 기반의 설정 파일로 인식. |
| @PropertySource          | 해당 클래스에서 참조할 properties 파일의 위치를 지정.        |
| @Autowired               | 빈(Bean)으로 등록된 인스턴스(이하 객체)를 클래스에 주입하는 데 사용. @Autowired 이외에도 @Resource, @Inject 등이 존재합니다. |
| ApplicationContext       | ApplicationContext는 **스프링 컨테이너(Spring Container)** 중 하나.  컨테이너는 사전적 의미로 무언가를 담는 용기 또는 그릇을 의미하는데,  스프링 컨테이너는 **빈(Bean)의 생성과 사용, 관계, 생명 주기 등을 관리**함.  빈(Bean)은 쉽게 이야기하면 객체임.  예를 들어, 프로젝트에 100개의 클래스가 있다고 가정해 보도록 하자.  100개의 클래스 간의 의존적인 문제가 많으면 **"결합도가 높다"**라고 표현하는데,  이러한 문제를 컨테이너에서 빈(Bean)을 주입받는 방법으로 해결할 수 있음. |
| @Bean                    | Configuration 클래스의 메서드 레벨에만 지정이 가능하며,  @Bean이 지정된 객체는 컨테이너에 의해 관리되는 빈(Bean)으로 등록.  인자로 몇 가지 속성을 지정할 수 있음. |
| @ConfigurationProperties | 해당 애너테이션은 인자에 **prefix** 속성을 지정할 수 있고,  prefix는 접두사, 즉 머리를 의미.  우리는 prefix에 spring.datasource.hikari를 지정하였는데, 쉽게 이야기하면 @PropertySource에 지정된 파일(application.properties)에서  prefix에 해당하는 **spring.datasource.hikari**로 시작하는  설정을 모두 읽어 들여 해당 메서드에 매핑(바인딩)함.  추가적으로 해당 애너테이션은 메서드뿐만 아니라  클래스 레벨에도 지정할 수 있음. |
| hikariConfig             | 히카리CP 객체를 생성.  히카리CP는 **커넥션 풀(Connection Pool) 라이브러리** 중 하나임. |
| dataSource               | 데이터 소스 객체를 생성.  순수 JDBC는 SQL을 실행할 때마다 커넥션을 맺고 끊는 I/O 작업을 하는데,  이러한 작업은 상당한 리소스를 잡아먹는다고 함.  이러한 문제의 해결책으로 커넥션 풀이 등장했는데  커넥션 풀은 커넥션 객체를 생성해두고, 데이터베이스에 접근하는 사용자에게  미리 생성해둔 커넥션을 제공했다가 다시 돌려받는 방법.  데이터 소스는 커넥션 풀을 지원하기 위한 인터페이스. |
| sqlSessionFactory        | SqlSessionFactory 객체를 생성.  SqlSessionFactory는 데이터베이스의 커넥션과  SQL 실행에 대한 모든 것을 갖는 정말 중요한 역할을 함.  **SqlSessionFactoryBean**은 마이바티스와 스프링의 연동 모듈로 사용되는데요,  마이바티스 XML Mapper, 설정 파일 위치 등을 지정하고,  SqlSessionFactoryBean 자체가 아닌, getObject 메서드가 리턴하는  SqlSessionFactory를 생성. |
| sqlSession               | sqlSession 객체를 생성.  마이바티스 공식 문서에는 다음과 같이 정의되어 있음 .  1. SqlSessionTemplate은 마이바티스 스프링 연동 모듈의 핵심이다.  2. SqlSessionTemplate은 SqlSession을 구현하고,   코드에서 SqlSession을 대체하는 역할을 한다.  3. SqlSessionTemplate은 쓰레드에 안전하고,   여러 개의 DAO나 Mapper에서 공유할 수 있다.  4. 필요한 시점에 세션을 닫고, 커밋 또는 롤백하는 것을 포함한   세션의 생명주기를 관리한다.  SqlSessionTemplate은 SqlSessionFactory를 통해 생성되고,  공식 문서의 내용과 같이 데이터베이스의 커밋, 롤백 등  SQL의 실행에 필요한 모든 메서드를 갖는 객체로 생각. |



#### 4. 테스트 코드 작성

```JAVA
package com.board2;

import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.ApplicationContext;

@SpringBootTest
class Board2ApplicationTests {

	@Autowired
	private ApplicationContext context;
	
	@Autowired
	private SqlSessionFactory sessionFactory;
	
	@Test
	void contextLoads() {}
	
	@Test
	public void testApplicationContext() {
		try {
			System.out.println("=========================================");
			System.out.println(context.getBean("sqlSessionFactory"));
			System.out.println("=========================================");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	@Test
	public void testSqlSessionFactory() {
		try {
			System.out.println("=========================================");
			System.out.println(sessionFactory.toString());
			System.out.println("=========================================");

		} catch (Exception e) {
			e.printStackTrace();
		}
	}

}
```

메서드 이름을 더블클릭하고 우클릭 -> Run As -> Junit Test 하면 간단하게 테스트 가능 

testApplicationContext 의 context.getBean("sqlSessionFactory");

DBConfiguration 클래스의 sqlSessionFactory 메서드의 이름을

ApplicationContext의 getBean 메서드의 인자로 지정

 

testApplicationContext 결과



![img](https://blog.kakaocdn.net/dn/k1I1q/btqKwQ6mDP8/AkQxO6BXoJfx9oKzPYfWMk/img.png)



testSqlSessionFactory 의 sessionFactory.toString();

위의 getBean() 의 인자인 sqlSessionFactory와 @Autowired로 주입한 sessionFactory는 같은 SqlSessionFactory 객체

 

testSqlSessionFactory 결과



![img](https://blog.kakaocdn.net/dn/bAWkKR/btqKwqGQ6bN/Y3I7eEzXK8skIIHy5JhA70/img.png)



 

