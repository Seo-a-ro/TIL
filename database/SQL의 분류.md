### SQL의 분류

---

#### DML( Data Manipulation Language)

* 데이터 조작 언어
* 데이터를 조작(선택, 삽입, 수정, 삭제)하는데 사용되는 언어
* DML 구문이 사용되는 대상은 테이블의 행
* DML 사용하기 위해서는 꼭 그 이전에 테이블이 정의되어 있어야 함
* SQL문 중 SELECT, INSERT, UPDATE, DELETE가 이 구문에 해당함
* 트랜잭션(Transaction)이 발생하는 SQL도 이 DML에 속함
  * 테이블의 데이터를 변경(입력/수정/삭제)할 때 실제 테이블에 완전히 적용하지 않고, 임시로 적용시키는 것
  * 취소 가능 



#### DDL(Data Definition Language)

* 데이터 정의 언어 
* 데이터베이스, 테이블, 뷰, 인덱스 등의 데이터 베이스 객체를 생성/삭제/변경하는 역할
* CREATE, DROP,ALTER 구문
* DDL은 트랜잭션 발생시키지 않음
* ROLLBACK이나 COMMIT 사용 불가
* DDL문은 실행 즉시 MYSQL에 적용



#### DCL(Data Control Language)

* 데이터 제어 언어
* 사용자에게 어떤 권한을 부여하거나 빼앗을 때 주로 사용하는 구문
* GRANT/REVOKE/DENY 구문 

----

##### SHOW DATABASES

6개의 데이터베이스가 존재함 

![image-20210708113404342](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708113404342.png)



##### USE

* 사용할 데이터베이스를 지정
* 지정해 놓은 후 특별히 다시 USE문 사용하거나 다른 DB를 사용하겠다고 명시하지 않는 이상 모든 SQL문은 지정 DB에서 수행 

![image-20210708205254979](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708205254979.png)



##### SHOW TABLE

* 데이터베이스 world의 테이블 이름 보기 

  

![image-20210708205322039](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708205322039.png)



##### SHOW TABLE STATUS 

* 데이터베이스 world의 테이블 정보 조회 

* 어떤 특정테이블의 어떤 열로 되어있는지 

![image-20210708114112145](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708114112145.png)



##### DESCRIBE(DESC)

* city 테이블에 무슨 열이 있는지 확인 
* 열에 대한 정보를 확인
* 테이블에 대한 속성 정보 

![image-20210708114657913](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708114657913.png)



##### select 

* 이 테이블에서 보고싶은 정보를 가져와라
* 요구하는 데이터를 가져오는 구문
* 일반적으로 가장 많이 사용되는 구문
* 데이터베이스 내 테이블에서 원하는 정보를 추출 
* SELECT의 구문 형식

```mysql
SELECT select_expr
	   [FROM table_references]
	   [WHERE where_condition]
	   [GROUP BY{col_name|expr|position}]
	   [HAVING where_condition]
	   [ORDER BY{col_name|expr|position}]
```



![image-20210708115247137](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708115247137.png)

![image-20210708115634520](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708115634520.png)



##### where

* 조회하는 결과에 특정한 조건을 줘서 원하는 데이터만 보고 싶을 때 사용
* SELECT 필드이름 FROM 테이블 이름 where 조건식;
* 조건이 없을 경우 테이블의 크기가 클수록 찾는 시간과 노력이 증가 

![image-20210708120023759](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708120023759.png)



##### 관계 연산자의 사용

![image-20210708120238869](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708120238869.png)



* 한국에 있는 도시들 중 인구 수가 1,000,000 이상인 도시들만 select 하기 

![image-20210708120753959](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708120753959.png)



##### Between 

* 데이터가 숫자로 구성되어 있어 연속적인 값은 BETWEEN ... AND 사용 가능
* (숫자)범위 지정 

![image-20210708120941047](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708120941047.png)



##### IN

* 이산적인(Discrete) 값의 조건에서는 IN() 사용 가능 

![image-20210708121138829](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708121138829.png)



##### LIKE

* 문자열의  특정내용을 검색하기 위해 LIKE연산 사용
* 하나의 문자가 아니라 어떤것도 올 수 있을 때 %사용 
* 한 글자와 매치하기 위해서는 '_' 사용 

![image-20210708121900125](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708121900125.png)

![image-20210708121955644](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708121955644.png)

##### Sub Query

* 서브 쿼리 

* 쿼리문 안에 또 쿼리문이 들어 있는 것

* 서브 쿼리의 결과가 둘 이상이 되면 에러가 발생함 

  ![image-20210708122717390](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708122717390.png)



##### ANY 

* 서브쿼리 여러 개의 결과 중 한가지만 만족해도 가능
* SOME은 ANY 와 동일한 의미로 사용
* =ANY 구문은 IN과 동일한 의미 

![image-20210708123054216](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708123054216.png)



##### ALL

* 서브쿼리의 여러 개의 결과를 모두 만족 시켜야 함. 
* EX> 뉴욕의 도시보다 인구가 많은 곳을 보여줘라. 

![image-20210708205725208](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708205725208.png)



##### ORDER BY 

![image-20210708205908778](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708205908778.png)

![image-20210708205950922](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708205950922.png)



##### DISTINCT

* 중복된 것은 1개씩만 보여주면서 출력
* 테이블의 크기가 클수록 효율적

![image-20210708214333406](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708214333406.png)

![image-20210708214453453](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708214453453.png)



##### LIMIT

* 출력 개수를 제한
* 상위의 N개만 출력하는 'LIMIT N'구문
* 서버의 처리량을 많이 사용해 서버의 전반적인 성능을 나쁘게 하는 악성 쿼리문 개선할 때 사용
* EX> 상위 10개의 인구 수만 내림차순으로 

![image-20210708215417027](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708215417027.png)



##### GROUP BY

* 그룹으로 묶어주는 역할
* 집계 함수(Aggregate Function)를 함께 사용
  * AVG(): 평균
  * MIN():최소
  * MAX():최대
  * COUNT():행의 개수
  * COUNT(DISTINCT): 중복 제외된 행의 개수
  * STDEV():표준편차
  * VARIANCE():분산
* 효율적인 데이터 그룹화 (Grouping)
* 읽기 좋게 하기 위해 별칭(Alias)사용 

![image-20210708220009058](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708220009058.png)

##### 

##### HAVING 

* WHERE 과 비슷한 개념으로 조건 제한
* 집계 함수에 대해서 조건 제한하는 편리한 개념
* HAVING절은 반드시 GROUP BY 절 다음에 나와야 함

![image-20210708220810968](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708220810968.png)

##### ROLLUP

* 총합 또는 중간합계가 필요할 경우 사용

* GROUP BY 절과 함께 WITH ROLLUP문 사용 

* 각각의 모든 집계 현황을 보여줌 

  ![image-20210708221531466](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708221531466.png)



##### JOIN 

* JOIN은 데이터베이스 내의 여러 테이블에서 가져온 레코드를 조합하여 하나의 테이블이나 결과 집합으로 표현
* 합쳐지려면 조인에 조건이 필요함 

![image-20210708221749141](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708221749141.png)



* city, country, countrylnaguage 테이블 3개 JOIN 하기 

  ![image-20210708222149606](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210708222149606.png)

