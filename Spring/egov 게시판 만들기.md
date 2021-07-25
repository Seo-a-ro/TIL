#### 정부 게시판 만들기

---

* 정부 게시판 만들 때 port8080 서버 겹쳤을 때 오류 해결 방법 

[](https://aspring.tistory.com/aentry/Apache-Tomcat-%ED%8F%AC%ED%8A%B8-%EC%98%A4%EB%A5%98-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0-Starting-Tomcat-v80-Server-at-localhost-has-encountered-a-ploblem-Port-8080-required-by-Tomcat-v80-Server-at-localhost-is-already-in-use)

##### 방법 1  포트 8080 사용하고 있는 PID 삭제하기 

* netstat -ano -p tcp


![image-20210718191843895](https://user-images.githubusercontent.com/82528589/126889808-fd073c52-f782-493f-992c-eb7a227259f3.png)
내 경우는 8080 을 사용하고 있는 PID는 19032 



* TASKKILL/F /PID 19032
* 안될 시 관리자권한으로 실행하여 진행 ^^;;

![image-20210718191843895](https://user-images.githubusercontent.com/82528589/126889811-8011630b-ed5c-4cc9-8bc9-40b0d6cc8e92.png)



완성

![image-20210718191959028](https://user-images.githubusercontent.com/82528589/126889814-bdd5a936-26ef-490b-ad18-83af7c3e2973.png)



### base-package 변경 

---

어떤 패키지 디렡고리들에서 컨트롤러 파일들을 읽어올 것인지 

파일검색 ctrl +H 



#### 3. 참조 패키지 디렉토리명 일괄 변경

[[Spring/eGov\] #4 스프링 Sample 기본 예제 프로젝트, 패키지 이름 바꾸기 / 파일 찾기 (tistory.com)](https://extsdd.tistory.com/100?category=851852)

* egovframework.example.sample -> cpservice.fwd
* egovframework.example ->cpservice

로 변경해야 http://localhost:8080 이 잘 작동된다. 
