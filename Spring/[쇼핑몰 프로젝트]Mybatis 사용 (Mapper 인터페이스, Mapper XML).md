### Mybatis 사용 (Mapper 인터페이스, Mapper XML)

출처: [[Spring\][05-2]MyBatis 사용(Mapper 인터페이스, Mapper XML) (tistory.com)](https://kimvampa.tistory.com/59?category=765899)

---

목표

* MyBatis 사용하기 위해서 Mapper 인터페이스와 Mapper XML 구조가 필요
* Mapper 인터페이스와 Mapper XML 형태를 생성 및 설정을 마지막으로 테스트까지 진행 예정
* Mapper 인터페이스를 통한 맵핑과 Mapper XML을 통한 맵핑의 장단점에 대해 공부 

---

Mapper는 SQL과 그에 대한 처리를 지정하는 역할 수행. 스프링에서 MyBatis를 사용하는 경우에는 Mapper인터페이스 (Mapper 인터페이스 + 어노테이션) 형태와 XML(Mapper XML)형태로 작성 가능. 



#### 1. Mapper 인터페이스 

Mybatis-Spring은 Mapper 인터페이스를 이용해서 실제 SQL 처리가 되는 클래스를 자동으로 생성함. 



#### Mapper 인터페이스 생성 및 설정 방법

1. mapper로 사용할 패키지 추가 

   src/main/java 경로에 com.shopping.mapper 패키지 추가 

   : 해당 페이지에 mapper로 사용할 인터페이스를 생성하여 사용하기. 

![image-20210811110342412](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811110342412.png)

![image-20210811110420431](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811110420431.png)



2. root-context.xml 설정

   root-context.xml에 추가적인 설정을 통해 패키지에 생성한 인터페이스를 mapper로 인식하도록 만듦. 

   1. root-context.xml 창을 열기. 하단 namespaces클릭 후 뜨는 목록에서 mybatis-spring체크하여 추가.

![image-20210811110713826](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811110713826.png)



2. 하단의 Source 클릭 후 아래의 코드를 추가해 줌. 

   -> <mybatis-spring:scan > 태그의 base-package 속성은 지정된 패키지의 모든 Mybatis 관련 어노테이션을 찾아서 처리함. 

   

```xml
<mybatis-spring:scan base-package="자신이 mapper 인터페이스를 담을 패키지 지정"/>
```

![image-20210811110952787](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811110952787.png)



#### 테스트

앞서 Mapper 인터페이스를 생성하고 설정하였음. 이번에는 이 구조를 직접 사용해보도록 하겠음. 아직 데이터베이스에 테이블을 생성하지 않은 관계로 테이블 없이도 실행이 되는 SQL문 (select sysdate from dual [현재시간])을 사용하는 것을 목표로 함. 



1. root-context.xml에 mapper역할로 지정한 패키지(com.shopping.mapper)에 테스트를 위해 사용할 인터페이스 TimeMapper.java를 만들기.

   ![image-20210811111813029](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811111813029.png)



2. 코드 추가하기 

   ```java
   package com.vam.mapper;
   
   import org.apache.ibatis.annotations.Select;
   
   public interface TimeMapper {
   
   	@Select("SELECT sysdate FROM dual")
   	public String getTime();
   	
   }
   ```

   

![image-20210811111857512](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811111857512.png)



3. SQL문 작동하는지 테스트 하기 위해 src/test/java -> "com.shopping.persistence" 경로에 TimeMapperTests 클래스를 만들기.

![image-20210811112124872](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811112124872.png)



4. 코드 추가

   

```java
package com.vam.persistence;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.vam.mapper.TimeMapper;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("file:src/main/webapp/WEB-INF/spring/root-context.xml")
public class TimeMapperTests {

	@Autowired
	private TimeMapper timeMapper;
	
	@Test
	public void testGetTime() {
		System.out.println(timeMapper.getTime());
	}
	
	
}
```



5. JUnit 테스트 진행 및 결과 확인 

   테스트 진행 시 오류 발생. 

   **SQL문(select sysdate from dual [현재시간]) ** 은 ORACLE

   MYSQL에서는 **SELECT NOW() FROM DUAL** 로 사용 

   참고: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=miniii_su&logNo=221624861517

![image-20210811130632603](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811130632603.png)



#### 2. Mapper XML

Mapper 인터페이스만으로 충분히 sql문을 편리하게 처리 가능. 하지만 복잡하고 길어지는 sql문을 사용하는 경우에는 xml문을 사용해야 함. 앞서 추가한 MyBatis-Spring 라이브러리의 경우 Mapper 인터페이스와 XML 을 동시에 이용할 수 있음. 

Mapper XML을 사용하기 위해 가장 주의해야 할 점은 XML파일의 위치와 XML파일에서 지정하는 namespaces속성임. 



#### Mapper XML 생성 및 설정 방법

**XML 저장 위치 :**

 저장 방법은 두 가지

1. Mapper 인터페이스가 저장된 곳에 xml파일을 저장.

2. 'src/main/resources' 위치에 Mapper 인터페이스가 저장된 패키지명을 폴더 경로로 만들어 줌.

 

**XML 파일 명 :** 

 XML 파일을 만들 때 정해진 규칙은 없음. 그렇지만 가능하면 Mapper인터페이스와 같은 이름을 이용하여 가독성을 높여주는 것이 좋음.



목표 : 

\- XML 저장 위치를 src/main/resources에 만들어보도록 하겠음.

\- 기존에 만들었던 TimeMapper인터페이스와 연동되는 xml파일을 만들겠음.



1. 저의 인터페이스 경로는 'com.shopping.mapper'이기 때문에 'src/main/resources'경로에 com/shopping/mapper 경로가 되도록 폴더를 만들자.

![image-20210811131556131](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811131556131.png)



2. 생성한 경로에 TimeMapper 인터페이스의 이름과 동일한 TimeMapper.xml파일을 만들자. 

   검색박스에 xml을 기입함. 결과 물 중 XML File 클릭 -> next 클릭 

![image-20210811131628385](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811131628385.png)



​	자신이 사용할 파일 명을 작성한 후 finish 버튼 클릭 

![image-20210811131654849](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811131654849.png)



xml 파일 생성 완료 

![image-20210811131957485](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811131957485.png)



아래의 코드를 xml파일에 추가해주기.

<! DOCTYPE> 태그는 해당 문서가 mapper로서의 역할을 한다고 명시되어 있음. mapper로서 xml을 사용하고 싶으시면 반드시 작성해야 함. 

< mapper> 태그에서 namespace 속성을 여러분이 연동시키고자 하는 Mapper 인터페이스명을 경로와 함께 명시해주어야 함. 

SQL문은 < mapper>태그 안에 작성해주시면 됩니다. xml파일에서 SQL문 사용방법에 대한 자세한 내용은 MyBatis 공식 홈페이지를 참고하기 

![image-20210811132109899](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811132109899.png)



만든 구조를 정상적으로 작동이 되는지 직접 테스트 해보기. 



#### 테스트 

기존에 사용하였던 TimeMapper 인터페이스에서 추가적인 메서드를 선언하여 사용. 

인터페이스만을 사용한 메서드(getTime())을 그대로 두고 새로운 메서드(getTime2)를 추가. 해당 메서드는 XML 파일을 통해 SQL문이 실행되도록 하자.



1. TimeMapper 인터페이스에 getTime2() 메서드를 추가하기.

![image-20210811134322620](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811134322620.png)

2.  TimeMapper.xml 파일에 < select> 태그를 작성하고 실행시키고자 하는 SQL문(현재시간)을 추가하기.

   \- < mapper> 태그에 id속성은 Mapper인터페이스에 추가한 메서드명과 동일 하게 작성.

   

   \- < mapper>태그에 resultType속성은 연동시킬 메서드에 명시된 반환 타입을 작성.

   

   \- < mapper>태그 내부에 SQL문을 작성. => SELECT now()FROM dual(현재시간 검색)

   \- 자세한 사용방법은 MyBatis 공식 홈페이지를 참고.

![image-20210811135621819](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811135621819.png)



3. 기존에 테스트를 위해 사용하였던 TimeMapperTests 클래스에 테스트를 위한 코드 추가하기.

![image-20210811135520573](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811135520573.png)

4. Junit 테스트 진행 후 결과 확인 

   콘솔 창을 확인해보시면 두 개의 현재시간 문구가 떠 있음

   \- 위의 문구는 getTime1() 메서드의 결과. 오로지 인터페이스와 어노테이션을 사용해서 SQL문을 실행.

   \- 아래의 문구는 getTime2() 메서드의 결과. 해당 메서드는 인터페이스와 XML 파일을 통해서 SQL문을 실행.(어노테이션은 사용X.)

![image-20210811140318518](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210811140318518.png)



#### 3. 정리. 

MyBatis를 통해서 SQL문을 실행하기 위해서는 두 가지 방법이 있음. 

1. 인터페이스와 어노테이션의 사용
2. 인터페이스와 XML 사용



첫번째의 장점은 인터페이스 파일 한 가지만 사용함으로써 매우 편리하게 SQL을 실행시킬 수 있음. 

첫번째의 단점은 하지만 길고 복잡한 SQL문은 사용할 수 없음. 

길고 복잡한 SQL문을 사용해야 할 경우 XML을 사용하는 두번째 방식을 사용해야 함. 

XML 방식을 사용하여야 할 때는 '파일 생성 위치'와 XML 파일에서 작성하는 'namespace'속성을 주의해서 작성하길. 제대로 된 위치와 제대로 된 namespace속성을 작성하지 못할 경우 반드시 에러발생. 
