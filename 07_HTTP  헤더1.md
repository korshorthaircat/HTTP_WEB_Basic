# HTTP 헤더1 - 일반 헤더
## HTTP헤더 개요
* header-field
	* field-name”:” OWS field-value OWS (OWS:  띄어쓰기 허용)
	* field-name은 대소문자 구분 없음	
	* 예)
		* Host: www.google.com
		* Content-Type: text/html;charset=UTF-8
		* Content-Length: 3423
* HTTP 헤더의 용도
	* HTTP 전송에 필요한 모든 부가정보를 기재함
	* 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보 …
	* 표준 헤더가 매우 많음
	* 필요시 임의의 헤더 추가 가능
		* 예) Helloworld: hihi
	
* **1999년(과거)** RFC2616의 HTTP 표준
	* 헤더의 분류
		* General 헤더 - 메시지 전체에 적용되는 정보
			* 예) Connection: close
		* Request 헤더 - 요청 정보
			* 예) User-Agent: Mozilla/5.0 (Macintosh; ..)
		* Response 헤더 - 응답 정보
			* 예) Server: Apache
		* Entity 헤더 - 엔티티 바디 정보
			* 엔티티 헤더는 엔티티 본문의 데이터를 해석할 수 있는 정보를 제공함
				* 데이터 유형(html, json), 데이터 길이, 압축 정보 등 
			* 예) Content-Type: text/html, Content-Length: 3423
	* 메시지 본문(message body)는 엔티티 본문(entity body)을 전달하는데 사용
	* 엔티티 본문은 요청이나 응답에서 전달할 실제 데이터

* **2014년(최신)** RFC7230~7235의 HTTP 표준
	* RFC7230~7235의 등장으로 1999년의 RFC2616은 폐기됨
	* 변화된 것
		* 단어의 변화: 엔티티(Entity) —> 표현(Representation)
			* 표현은 요청이나 응답에서 전달할 실제 데이터
			* 표현 헤더는 표현 데이터를 해석할 수 있는 정보를 제공함
				* 데이터 유형(html, json), 데이터 길이, 압축 정보 등
			* 참고: 표현 헤더는 표현 메타데이터와 페이로드 메시지를 구분해야하지만 여기서는 생략			
	* 	표현(Representation) = 표현 메타데이터(Representation Metadata) + 표현 데이터(Representation Data)
	* 메시지 본문(message body)을 통해 표현 데이터 전달
		* 메시지 본문 = 페이로드(payload)
	
## 표현(Representation)
* 표현 헤더는 전송, 응답 둘 다 사용
* Content-Type: 표현 데이터의 형식
	* 미디어 타입, 문자 인코딩
	* 예)
		* Content-Type: text/html; charset=utf-8
		* Content-Type: application/json
		* Content-Type: image/png
* Content-Encoding: 표현 데이터의 압축 방식
	* 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가함
	* 데이터를 읽는 쪽에서 인코뎅 헤더의 정보로 압축 해제함
	* 예)
		* Content-Encoding: gzip
		* Content-Encoding: deflate
		* Content-Encoding: identity(압축을 하지 않은 상태, 보낸 데이터와 받는 데이터의 상태가 동일하다는 뜻)
* Content-Language: 표현 데이터의 자연 언어
	* 예)
		* Content-Language: ko
		* Content-Language: en
		* Content-Language: en-US
* Content-Length: 표현 데이터의 길이
	* Content-Length는 엄밀히 말해 페이로드지만, 그러한 구분은 너무 복잡해질 수 있으니 일단은 여기서 소개함
	* 바이트 단위
	* Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨
			
## 콘텐츠 협상
* 클라이언트가 선호하는 표현을 요청함 —> 서버가 반드시 이 요청을 수행하는 것은 아니지만, 가능한 한 클라이언트가 선호하는 우선순위에 맞추어 표현 데이터를 만들어 전송한다.
* 협상 헤더는 요청 시에만 사용 가능하다.
* 종류
	* Accept: 클라이언트가 선호하는 미디어 타입 전달
	* Accept-Charset: 클라이언트가 선호하는 문자 인코딩
	* Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
	* Accept-Language: 클라이언트가 선호하는 자연 언어
* 협상과 우선 순위
	* Quality Values(q) 값을 사용하여, 클수록 높은 우선 순위 (0~1, 생략하면 1)
		* 예) Accept-Language: ko-KR,ko;q=0.9, en-US;q=0.8, en;q=0.7
	* 구체적인 것이 우선한다. 
		* 예) Accept: text/*, text/plain, text/plain;format=flowed, */*
	* 구체적인 것을 기준으로 미디어 타입을 맞춘다.
		* 예) Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1, text/html;level=2;q=0.4, */*;q=0.5
		
## 전송 방식
* 단순 전송
	* 예) Content-Length: 3423
* 압축 전송
	* 예) Content-Encoding: gzip
* 분할 전송
	* 예) Transfer-Encoding: chunked
	* 분할 전송을 할 때는 Content-Length헤더를 쓰면 안된다.
* 범위 전송
	* 예) Content-Range: bytes 1001-2000 / 2000
	
## 일반 정보
* From: 유저 에이전트의 이메일 정보
	* 일반적으로 잘 사용되지 않음. 
	* 검색엔진 같은 곳에서, 요청에서 사용
* Referer: 이전 웹 페이지 주소
	* 현재 요청된 페이지의 이전 웹페이지 주소
	* A —> B로 이동하는 경우 B를 요청할 때 Referer: A를 포함해서 요청
	* Referer를 사용해서 유입 경로 분석 가능
	* 요청에서 사용
	* 참고) ‘referer’는 ‘referrer’의 오타이다. 
* User-Agent: 유저 에이전트 애플리케이션 정보
	* 클라이언트의 애플리케이션 정보(웹 브라우저 정보 등)
	* 통게 정보
	* 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
	* 요청에서 사용
	* 예) User-Agent: Mozilla/5.0 (Macintosh; Intel Mac Os X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36
* Server: 요청을 처리하는 오리진 서버의 소프트웨어 정보
	* 응답에서 사용
	* 예) Server: Apache/2.2.22 (Debian)
* Date: 메시지가 생성된 날짜
	* 응답에서 사용
	* 예) Date: Tue, 15 Nov 1994 08:12:31 GMT

## 특별한 정보
실제 애플리케이션에 영향을 주는 특별한 정보를 제공하는 헤더들

* Host: 요청한 호스트 정보(도메인)
	* 필수!
	* 요청에서 사용
	* 하나의 서버가 여러 도메인을 처리해야 할 때
	* 하나의 IP 주소에 여러 도메인이 적용되어 있을 때
	* 예) Host: www.google.com
* Location: 페이지 리다이렉션
	* 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)
	* 201 (Created) - Location 값은 요청에 의해 생성된 리소스 URI
	* 3xx (Redirection) - Location 값은 요청을 자동으로 리다이렉션하기 위한 대상 리소스를 가리킴
* Allow: 허용 가능한 HTTP 메서드
	* 405 (Method Not Allowed)에서 응답에 포함해야 함
	* 서버에서 실제로 그렇게 많이 구현되어 있지는 않음
* Retry-After: 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
	* 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
	* 예) Retry-After: Fri, 31 Dec 1999 23:59:59 GMT (날짜 표기)
	* 예) Retry-After: 120(초 단위 표기)
	
## 인증
* Authorization: 클라이언트 인증 정보를 서버에 전달
	* Authorization: Basic xxxxxxxxxxxxxxxxx
* WWW-Authenticate: 리소스 접근시 필요한 인증 방법 정의
	* 401 (Unauthorized) 응답과 함께 사용
	* 예) WWW-Authenticate: Newauth realm=“apps”, type=1, title=“Login to \”apps\””, Basic realm=“simple”

## 쿠키
* 쿠키를 미사용할 경우…
	* HTTP는 기본적으로 무상태(Stateless) 프로토콜이다. 클라이언트와 서버는 상태를 유지 하지 않는다. 따라서 (쿠키를 미사용할 경우) 사용자는 모든 요청과 링크에 사용자 정보를 포함해야만 한다. 
	* 모든 요청에 사용자 정보가 포함되도록 개발하는 일은 까다롭다.
* 쿠키를 사용할 경우…
	* 모든 요청에 쿠키 정보가 자동 포함된다. 

* 쿠키
	* 사용처
		* 사용자 로그인 세션 관리
		* 광고 정보 트래킹
	* 특징
		* 쿠키 정보는 항상 서버에 전송됨
		* 네트워크 트래픽을 추가 유발함
		* 최소한의 정보만 사용해야 함(세션 ID, 인증 토큰)
		* 서버에 전송하지 않고 웹 브라우저 내부에 데이터를 저자하고 싶다면 웹 스토리지(localStorage, sessionStorage) 참고
	* 주의
		* 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등)
 
* Set-Cookie: 서버에서 클라이언트로 쿠키 전달(응답)
* Cookie: 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달

* 쿠키 - 생명 주기(Expires, max-age)
	* 만료일이 되면 쿠키 삭제
		* 예) Set-Cookie: **expires**=sat, 26-Dec-2020 04:39:21 GMT
	* 0이나 음수를 지정하면 쿠키 삭제
		* 예) Set_Cookie: **max-age**=3600 (3600초)
	* 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시까지만 쿠키 유지
	* 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지
	
* 쿠키 - 도메인(Domain)
	*  예) domain=example.org
	* **명시: 명시한 문서 기준 도메인 + 서브 도메인 포함**
		* 예) domain=example.org를 지정해서 쿠키를 생성할 경우 example.org는 물론이고 dev.example.org도 쿠키 접근
	* **생략: 현재 문서 기준 도메인만 적용**
		* 예) example.org에서 쿠키를 생성하고 domain 지정을 생략할 경우 example.org에서만 쿠키 접근 가능하고 dev.example.org는 쿠키 미접근

* 쿠키 - 경로(Path)
	* 예) path=/home
	* 이 경로를 포함한 하위 경로 페이지만 쿠키 접근 가능함
	* 일반적으로 path=/루트로 지정
	* 예)
		* path=/home 지정
		*  /home —> 가능
		* /home/level1 —> 가능
		* /home/level1/level2 —> 가능
		* /hello —> 불가능
