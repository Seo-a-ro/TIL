### [쇼핑몰 프로젝트] 주소록 API 연동

출처: [[Spring\][쇼핑몰 프로젝트][5] 주소록 API 연동 (tistory.com)](https://kimvampa.tistory.com/110?category=771727)

---

#### 목표

주소 검색 API를 활용하여 주소를 검색하여 검색된 주소가 회원가입 페이지의 주소 필드에 입력되는 것을 목표



주소록 API 사용 방법 : **[postcode.map.daum.net/guide](http://postcode.map.daum.net/guide)**

1. '주소찾기 ' 버튼 클릭

2. 주소찾기 팝업창에서 자신이 찾고자 하는 주소 검색 및 선택

3. 선택된 주소 회원가입페이지 주소 필드에 입력

   

#### 순서

0. 수정

1. 외부 스크립트 파일 연결 코드 추가

2. 주소 검색 기능 메서드 메서드 추가

3. 다음 주소 팝업창 코드 추가

4. [주소찾기]버튼과 메서드 연결

5. 테스트

6. 주소 입력 예제 코드 추가

7. 예제코드 수정

8. 테스트

---

#### 0. 수정

 주소입력 필드 3개 모두 readonly 속성 추가

주소 입력 필드에 사용자가 임의로 변경할 수 없도록 주소 입력 필드 3개 모두 disabled속성 추가하려 했지만 ,

속성이 추가된 태그(요소)는 form 태그에 쌓여서 Controller로 전송이 되어야 함

하지만 disabled설정 할 경우 데이터 자체가 전송이 되지 않기 때문에 변경함 

![image-20210819161602803](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819161602803.png)



#### 1. 외부 스크립트 파일 연결 코드 추가

다음 주소록 API를 사용하기 위해 join.jsp에 외부 스크립트 파일을 연결하는 코드를 추가하자.

- 자바스크립트 메서드를 포함하는 스크립트 태그 바로 위에 추가해줌
-  다음 주소록 API 설명 홈페이지를 보시면 로딩 방식으로 '통합 로딩 방식' / '동적 로딩방식' / '정적 로딩 방식'이 있음.  이중에 '통합 로딩 방식'을 사용

![image-20210819165542678](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819165542678.png)



#### 2. 주소 검색 기능 메서드 추가 

주소 API기능을 사용하기 위한 메서드 추가하기 이름은 execution_daum_adress로 정함

![image-20210819170726954](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819170726954.png)



#### 3. 다음 주소 팝업창 코드 추가

메서드 내용에 주소를 검색하는 팝업창을 띄우는 코드 추가

![image-20210819170915274](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819170915274.png)



#### 4. [주소찾기] 버튼과 메서드 연결

주소찾기 버튼(div태그)을 클릭할 시 추가한 메서드가 실행이 되도록 onclick속성을 추가하고 값을 앞에서 추가한 메서드 [execution_daum_address()]를 넣기

![image-20210819171123663](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819171123663.png)



#### 5. 테스트 

![image-20210819172243537](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819172243537.png)



#### 6. 주소 입력 예제 코드 추가

사용자가 선택한 값 이용하기 예제코드 추가

![image-20210819172555042](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819172555042.png)



#### 7. 예제 코드 수정

예제 코드를 바로 사용 불가. 우리가 사용하고자 하는 의도에 맞게 수정해야 함

※function(data)에서 data는 팝업창에서 선택한 주소에 대한 정보를 반환받는 객체 변수

객체에 어떠한 데이터(속성)가 있는지는 설명 홈페이지에서 아래의 스크린숏에서 보이는 구간에 있음

![img](https://blog.kakaocdn.net/dn/bRJfpj/btqNCxxA6gb/QqHbYtbyQVAQV9Ilx2KR80/img.png)



##### 7-1 주소필드, 참고 항목 필드 합치기

현 예제 코드의 경우는 사용자가 도로명주소를 선택하였을 때 추가적으로 입력되어야 할 정보를 참고 항목 필드 입력되도록 되어있음.  하지만 저는 참고항목 필드를 따로 만들어 놓지 않았고 따라서 저는 주소가 입력되는 필드에 추가 항목 필드에 입력될 정보가 함께 입력되도록 만들 것임.

- 예제 코드 중 아래의 코드를 지우고 주소 데이터와 추가 항목 데이터가 합쳐지도록 코드 추가

```jsp
// 제거해야할 코드
                // 조합된 참고항목을 해당 필드에 넣는다.
                document.getElementById("sample6_extraAddress").value = extraAddr;
 
// 추가해야할 코드
                // 주소변수 문자열과 참고항목 문자열 합치기
                  addr += extraAddr;
```

![image-20210819174336568](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819174336568.png)



두번째 if문 else 내부의 코드를 지우고 새로운 코드 추가하기

(기존의 코드는 추가 항목 필드에 아무것도 입력되지 않게 하기 위한 코드. 하지만 추가항목 필드가 따로 없기 때문에 제거함.)

```jsp
// 제거해야할 코드
                document.getElementById("sample6_extraAddress").value = '';
 
// 추가해야할 코드
                addr += ' ';
```

![image-20210819174525646](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819174525646.png)



#### 7-2 우편번호와 주소 정보를 해당 필드에 넣어주는 코드를 수정해줌 (제이쿼리에 맞는 코드로 수정해줌)

```jsp
// 제거해야할 코드
            // 우편번호와 주소 정보를 해당 필드에 넣는다.
            document.getElementById('sample6_postcode').value = data.zonecode;
            document.getElementById("sample6_address").value = addr;
// 추가해야할 코드
            $(".address_input_1").val(data.zonecode);
            //$("[name=memberAddr1]").val(data.zonecode);    // 대체가능
            $(".address_input_2").val(addr);
            //$("[name=memberAddr2]").val(addr);            // 대체가능
```

![image-20210819174934035](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819174934035.png)



#### 7-3 상세주소 필드를 사용할 수 있도록 변경해주는 코드를 추가해준 뒤, 커서를 상세주소 필드로 이동하는 코드를 수정해줌

```jsp
// 제거해야할 코드
            // 커서를 상세주소 필드로 이동한다.
            document.getElementById("sample6_detailAddress").focus();
 
// 추가해야할 코드
            // 상세주소 입력란 disabled 속성 변경 및 커서를 상세주소 필드로 이동한다.
           $(".address_input_3").attr("readonly",false);
            $(".address_input_3").focus();
 

```

![image-20210819175150447](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210819175150447.png)



#### 8. 테스트 

주소 찾기 클릭

![img](https://blog.kakaocdn.net/dn/biuTWf/btqNJwpSg8T/5EpNVQ9IUcuIelDVn0tYuk/img.png)



찾고자 하는 주소 입력하기

![image-20210820091515744](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210820091515744.png)

주소 입력 및 커서 이동 확인

![image-20210820091627869](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20210820091627869.png)
