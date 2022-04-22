# HTTP 메서드 활용
## 클라이언트에서 서버로 데이터 전송
* 데이터 전달 방식(크게 두 가지)
	* 1. 쿼리 파라미터를 통한 데이터 전송
		* GET
		* 주로 정렬 필터(검색어)
	* 2. 메시지 바디를 통한 데이터 전송
		* POST, PUT, PATCH
		* 회원 가입, 상품 주문, 리소스 등록, 리소스 변경

* 클라이언트에서 서버로 데이터를 전송하는 상황
	* 정적 데이터 조회
		* 이미지, 정적 텍스트 문서를 조회할 때  GET 사용
		* 일반적으로 쿼리 파라미터 없이 리소스 경로로 단순하게 조회 가능
		* 예)  `GET /static/star.jpg HTTP/1.1`
	* 동적 데이터 조회
		* 주로 검색, 게시판 목록에서 정렬필터(검색어)
		* 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 사용됨
		* 조회는 GET 사용 - 쿼리 파라미터 사용해서 데이터를 전달
			* 서버는 쿼리 파라미터를 기반으로 정렬 필터에서 결과를 동적으로 생성함
		*  예)  `GET /search?q=hello&hl=ko HTTP/1.1`
	* HTML Form을 통한 데이터 전송
		* 회원가입, 상품 주문, 데이터 변경 등
	* HTTP API를 통한 데이터 전송
		* 회원가입, 상품 주문, 데이터 변경 등
		* 서버 to 서버, 앱 클라이언트, 웹 클라이언트(Ajax)

### HTML Form을 통한 데이터 전송
* HTML Form을 submit할 때 POST 전송하는 경우
	* 회원 가입, 상품 주문, 데이터 변경
	* Content-Type: application/x-www-form-urlencoded 사용
		* form의 내용을 메세지 바디를 통해 전송함(key=value,  쿼리 파라미터 형식)
		* 전송 데이터를 url encoding으로 처리
			* 예) abc김 —> abc%EA%B9%80
* HTML Form을 submit할 때 GET 전송도 가능
	* Content-Type: multipart/from-data
		* 파일 업로드 같은 바이너리 데이터 전송시 사용함
		* 다른 종류의 여러 파일과 폼의 내용 함께 전송 가능(그래서 이름이 multipart)	
* 참고) HTML 폼 전송은  GET, POST만 지원함
	
* POST 전송 - 저장
```
HTML Form(method가 post!)
<form action="/save" method="post">
	<input type="text" name="username" />
	<input type="text" name="age" />
	<button type="submit">전송</button>
</form>

웹브라우저가 Form의 데이터를 읽어서 HTTP 요청 메세지를 생성해준다. 
POST /save HTTP/1.1
HOST: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20
```

* GET 전송 - 저장
```
HTML Form(method가 get!)
<form action="/save" method="get">
	<input type="text" name="username" />
	<input type="text" name="age" />
	<button type="submit">전송</button>
</form>	

웹브라우저가 Form의 데이터를 읽어서 HTTP 요청 메세지를 생성해준다.
username=kim&age=20을 
	POST일 때는 메세지의 바디에 넣어주었는데,
	GET일 때는 URL경로에 넣어준다.
GET /save?username=kim&age=20 HTTP/1.1
Host: localhost:8080
```

* GET 전송 - 조회
```
HTML Form(method가 get!)
<form action="/members" method="get">
	<input type="text" name="username" />
	<input type="text" name="age" />
	<button type="submit">전송</button>
</form>	

웹브라우저가 Form의 데이터를 읽어서 HTTP 요청 메세지를 생성해준다.
GET /members?username=kim&age=20 HTTP/1.1
Host: localhost:8080
```

* multipart/from-data

### HTML API를 통한 데이터 전송
* 서버 to 서버
	* 백엔드 시스템 통신
* 앱 클라이언트
	* 아이폰, 안드로이드
* 웹 클라이언트
	* HTML에서 Form 전송 대신 자바스크립트를 통한 통신에 사용(AJAX)
	* 예) React, VueJS 같은 웹 클라이언트와 API 통신
* POST, PUT, PATCH - 메시지 바디를 통해 데이터 전송
* GET - 조회, 쿼리 파라미터로 데이터 전달
* Content-Type: application/json을 주로 사용(사실상 표준)
	* TEXT, XML, JSON 등 

## HTTP API 설계 예시
* HTTP API -  컬렉션
	* POST 기반 등록 - 서버가 리소스 URI 결정
* HTTP API - 스토어
	* PUT 기반 등록 - 클라이언트가 리소스 URI 결정
* HTML Form 사용
	

* POST - 신규 자원 등록의 특징
	* 클라이언트는 등록될 리소스의 URI를 모름
	* 서버가 새로 등록된 리소스 URI를 생성해줌
	* 컬렉션(Collection)
		* 서버가 관리하는 리소스 디렉토리
		* 서버가 리소스의 URI를 생성하고 관리

* PUT - 신규 자원 등록의 특징
	* 클라이언트가 리소스 URI를 알고 있어야 함
	* 클라이언트가 직접 리소스의 URI를 지정
	* 스토어(Store)
		* 클라이언트가 관리하는 리소스 저장소
		* 클라이언트가 리소스의 URI를 생성하고 관리

* HTML Form 사용
	* 순수 HTML + HTML Form 사용
		* GET, POST만 지원하므로 제약이 있음 —> 컨트롤 URI 필요
		* 컨트롤 URI
			* 동사로 된 리소스 경로 사용
			* POST의 /new, /edit, /delete가 컨트롤 URI
			* HTTP메서드로 해결하기 애매한 경우 사용(HTTP API 포함)

* 참고하면 좋은 URI 설계 개념
	* 문서(document)
		* 단일 개념(파일 하나, 객체 인스턴스, 데이터 베이스 row)
		* 예) /members/100, /files/star.jpg
	* 컬렉션(collection)
		* 서버가 관리하는 리소스 디렉터리
		* 예)/members
	* 스토어(store)
		* 클라이언트가 관리하는 자원 저장소
		* 예)files
	* 컨트롤러(controller), 컨트롤 URI
		* 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
		* 동사를 직접 사용
		* 예)/members/{id}/delete
