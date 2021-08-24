### [쇼핑몰 프로젝트] 1 메일서버 (SMTP Server)를 이용한 메일전송 

출처 : [[Spring\]메일서버(SMTP Server)를 이용한 메일 전송 1 (tistory.com)](https://kimvampa.tistory.com/92)

---

**목표**

1. 스프링에서 SMTP 서버를 이용한 메일 전송을 할 수 있음.
2. 1의 목표 기능을 구현하기 위해 구글, 네이버, 다음에 어떠한 설정을 해야하는지 알아보자. 



##### 목차

0. 개요

1. 메일 계정 보안 설정

2. pom.xml 설정

3. mailSender Bean 등록

----- ↓다음 글 ----

4. Controller 메서드 추가

5. 텍스트 / 이미지 / 업로드 전송

6. 메일 전송 테스트

 

---

#### 0. 개요

스프링프레임워크에서 메일서버(SMTP Server)를 이용해서 메일을 전송하는 방법에 대해 알아보자. 

SMTP는 Simple Mail Transfer Protocol 로서 전자 메일 전송을 위한 표준 프로토콜. 따라서 SMTP Server란 이메일을 송신하는 서버. (메일서버는 SMTP Server, POP3 Server가 있고 POP3 Server는 이메일을 수신하는 서버.)

메일 전송 기능을 구현하기 위해선 아래의 4가지가 필요함.

1. pom.xml 설정

2. mailSender Bean 등록(root-context.xml 도는 servlet-context.xml)

3. Controller 메서드 추가

4. 메일 계정 보안 설정



메일서버(SMTP Server)를 통해 메일을 전송할 때 다음과 같은 두가지 방법 존재. 

1. 메일 서버가 릴레이를 허용하는 경우 

   - 계정이 존재하지 않더라도 이 방식은 메일 발송이 가능.

   - 릴레이를 허용하는 경우 잘못된 방식(타인에 의한 스팸메일)으로 악용될 수 있음.

   - 따라서 특정IP에서만 릴레이가 되도록 하는게 일반적.

     

2. 메일 서버에 계정 인증 후 메일을 전송하는 방법 으로 진행해보자.



#### 1. 메일 계정 보안 설정

SMTP Server를 통해서 메일 전송 기능을 구현하기 위해선 사용하고자 하는 메일의 SMTP보안 단계를 낮춰야함.

네이버의 경우 로그인시 휴대폰 승인을 통한 보안 단게까지 설정이 되어 있을 경우 아래의 설정만으로 부족. 



네이버, 구글만 진행

[[Spring\]메일서버(SMTP Server)를 이용한 메일 전송 1 (tistory.com)](https://kimvampa.tistory.com/92)



#### 2. pom.xml 설정

#### 2.1 mail 라이브러리 추가 

버전은 자신에게 맞는 버전을 선택하면 되지만 출처 글쓴이가 사용한 버전 그대로 1.4.7 사용

![image-20210813170423305](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210813170423305.png)

java에서 메일을 보내기 위해 필요로한 라이브러리

실제 전송되는 메일 객체를 가짐

주요 클래스로 Session, Message, Address, Authenticator, Tranport 등 있음

해당 라이브러리만으로도 메일 전송 기능이 구현 가능. 세팅 모두 직접해주기 

 spring-context-support 라이브러리를 사용할 것이기 때문에, mail 라이브러리에선 javax.mail.internet.MimeMessage만 사용. 



#### 2.2 spring-context-support 라이브러리 추가

![image-20210813170934191](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210813170934191.png)

 JavaMailSender 인터페이스(JavaMailSenderImpl)를 사용하기 위해 필요. mail 라이브러리를 일일이 세팅 할 필요 없이 몇가지의 설정으로도 사용가능 (MailSender Bean설정)

  \- JavaMailSender 인터페이스는 MIME 형식의 메일을 보낼 수 있도록 해줌.(javax.mail.MimeMessage 사용 가능)

  \- 스프링 프레임웍에서는 기본적으로 JavaMailSender 인터페이스와 비슷한 역할을 하는 메인 인터페이스 MainSender가 있음. 하지만 MainSender는 SimpleMailMessage만 사용 가능. (javax.mail.MimeMessage 사용 불가능)

 \* SimpleMailMessage는 단순한 텍스트 메시지만 사용할 수 있고, MimeMessage는 텍스트와 더불어 이미지와 같은 첨부파일을 같이 메시지에 포함시킬 수 있음.

 

#### 3. mailSender Bean 등록

 Bean 등록은 root-context.xml에 설정해주기

![image-20210816171618951](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210816171618951.png)

#### 3. 메일 서버가 릴레이를 허용하는 경우 

![image-20210813171629020](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210813171629020.png)



![image-20210816165905396](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210816165905396.png)

#### 사용 가능한 메일서버 찾기(nslookup)

1. cmd 실행
2. nslookup 입력후 enter
3. set type =mx 입력하고 enter
4. 자신이 사용하길 원하는 도메인 주소 입력 후 enter (ex:google.com/naver.com)
5. MX perference 는 우선순위로서 숫자가 낮을수록 우선순위가 높음

![image-20210816161526161](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210816161526161.png)



#### 메일 서버에 계정 인증 후 메일을 전송하는 방법



\- host : "smtp.gmail.com" 입력

 \- port : "587" 입력

 \- username : 자신의 이메일 아이디 입력

 \- password : 자신의 이메일 비밀번호 입력

 \- javaMailProperties인 < property> 태그에 삽입된 코드는 TLS 인증 코드.

 

 \* SSL이란 웹사이트와 브라우저 사이에서 전송되는 데이터를 암호화하여 인터넷 연결을 보안을 유지하는 표준 기술. TLS는 더 강력한 버전의 SSL. TLS 도한 SSL이라 불리기도 함. 
