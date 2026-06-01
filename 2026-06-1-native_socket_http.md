# socket & HTTP communication

## Socket
 - 프로세스가 네트워크를 통해 데이터를 송수신
 - 네트워크 상에서 두 호스트 간의 통신 (다양한 프로토콜)

 ### Concept
 - 데이터를 주고받기 위한 엔드포인트
 - IP + PORT 로 결합해 네트워크 상의 특정 위치를 지정
 - *Client ↔ Server 를 기반*
 - **Client** 서버에 연결을 요청
 - **Server** 요청을 수락해 데이터 통신 시작

### Variety
> [!NOTE]
> ### TCP Socket
> **신뢰성 있는 연결 지향 통신** 제공
> 
> 데이터의 순서와 무결성 보장
> 
> 연결을 설정 → 데이터를 주고받음 → 연결 종료

> [!NOTE]
> ### UDP Socket
> **비연결 지향 통신** 제공
> 
> 데이터그램 단위로 데이터를 전송
> 
> 신뢰성보단 빠른 전송 속도를 중시 (데이터 순서 및 무결성 미보장)

> [!NOTE]
> ### Raw Socket
> **IP 프로토콜** 직접 사용해 패킷 주고받음
>
> 네트워크 프로토콜의 구현 및 테스트에서 주로 사용

## HTTP
 - HyperText Transfer Protocol 의 약어
 - 웹상에서 클라이언트와 서버가 데이터를 주고받기 위해 사용하는 프로토콜
 - 요청과 응답 구조로 구성 / 클라이언트 → 서버 (응답 반환)

### How it works
 - Client
   - 헤더 설정
   - 바디 설정
   - 메시지 요청
 - Server
   - 요청 처리
   - 응답 코드 설정
   - 응답 반환

### Typical
 - Connectionless
   - HTTP 연결은 용건이 있을 때만 연결되고 끝나면 해제됨
     - 서버에 여유가 생겨 더 많은 접속 요구 대응
 - Stateless
   - 서버가 클라이언트 식별 불가
     - Connectionless 의 특성
     - 클라이언트를 기억해야 할 경우, Cookie/Token/Session 사용

### HTTP Method
|Name|Description|
|:-:|:-:|
|GET|서버에 리소스 요청, 수정하지 않기에 safe method|
|HEAD|GET과 같지만 서버가 본문(body)을 미포함|
|POST|클라이언트에서 요청한 URL 에 새로운 리소스 생성|
|PUT|클라이언트가 요청한 내용으로 전체 리소스 수정|
|PATCH|해당하는 리소스의 일부만 수정|
|DELETE|서버의 리소스 삭제|
|CONNECT|요청 리소스에 대해 양방향 연결 수립|
|OPTIONS|어떤 HTTP 메소드를 지원하는지 물어봄|
|TRACE|메시지의 변조여부 확인을 위해 수신한 클라이언트의 메시지 반환 요청|

## REST
 - Representational State Transfer 의 약어
 - 자원을 이름으로 구분해 해당 자원의 상태를 주고 받는 모든 것
 - HTTP 기반으로 필요한 자원에 접근하는 방식을 정해놓은 네트워크 아키텍쳐

### Principle
 - 클라이언트와 서버의 분리
 - 각 요청은 독립적이고, 서버는 클라이언트의 상태를 저장하지 않음 - *무상태*
 - 서버는 응답에 캐싱 가능 여부를 명시해 클라이언트가 캐싱할 수 있도록 지원 - *캐시 처리*
 - 클라이언트는 서버의 내부 구조를 알 필요 없이 API를 통해서만 상호 작용 - *시스템의 계층화*
 - HTTP와 같은 표준 프로토콜과 표준 데이터 형식을 사용 - *일관성 있는 인터페이스*

### Method
|Name|Description|
|:-:|:-:|
|POST|서버에 데이터 전송|
|GET|서버의 지정된 URL에 있는 리소스 액세스|
|PUT|서버의 기존 리소스 업데이트|
|DELETE|서버의 리소스 제거|

> RESTful API 는 HTTP 메서드를 사용해 자원을 조작
>
> 이때, URL를 통해 자원 식별

#### URI (URL)
- **URI** - 인터넷상의 자원을 식별하는 데 사용되는 문자열
  - `https://example.com:443/test.png?key=value#title`
  - **Scheme** - 자원에 접근하는 데 사용되는 프로토콜 (https)
  - **Domain** - 자원이 위치한 서버의 정보 (example.com)
  - **Port** - 자원이 위치한 서버의 접속 번호 (443)
  - **Path** - 서버 내에서 자원의 위치 (/test.png)
  - **query** - 추가적인 정보를 전달하기 위한 쿼리 문자열 (?key=value)
  - **anchor** - 문서 내 특정 부분을 가리키는 단편 식별자 (#title)
- **URL** - URI 의 한 종류, 네트워크상에서 리소스의 위치를 나타내기 위한 규약
- `https://example.com/test.png` - URI & URL
- `https://example.com/test.png?key=value`  - URI

# Android HTTP Communication Library
## HTTPClient
 - Apache 에서 제작한 안드로이드 초기에 가장 많이 쓰인 라이브러리
 - 실사용에선 DefaultHttpClient / AndroidHttpClient 사용

### 단점
 - API 가 복잡함
 - 변경점을 안드로이드 SDK 일괄적으로 즉시 반영 불가

### 결과
 - Android 5.1
   - Deprecated
 - Android 6.0
   - Deleted

## HttpURLConnection
 - 구글에서 HTTPClient를 삭제하면서 제시한 대안
 - 기존 URLConnection에 HTTP 다루는 데 필요한 메소드를 추가한 클래스
 - `url.openConnection` 으로 얻어진 HttpURLConnection 으로 데이터 송수신하고 연결 종료

### 다점
 - HttpURLConnection 은 ARN을 피하기 위해
   - 백그라운드 스레드 생성
   - 버퍼를 통한 입출력 준비
   - 캐시나 예외 처리들을 처리

## [Volley](https://google.github.io/volley/)
 - 구글에서 개발된 안드로이드 네트워킹 라이브러리
   - Google I/O 2013 에서 공개
 - *HttpURLConnection*의 여러 단점들을 보완
   - 디버깅 및 추적 도구
   - 다중 동시 네트워크 연결
   - 요청 우선순위 지정을 지원
   - 네트워크 요청의 자동 예약
   - 재시도 및 백오프 등을 위한 사용자 정의 용이
   - 표준 HTTP 캐시 일관성을 갖춘 투명한 디스크 및 메모리 응답 캐싱
   - 네트워크에서 비동기적으로 가져온 데이터로 UI를 올바르게 채우기 가능
   - 취소 요청 API, 단일 요청을 취소하거나 취소한 요청의 블록 또는 범위 설정 가능

## [OkHTTP](https://square.github.io/okhttp/)
 - Okio + Kotlin 을 활용해 만들어진 라이브러리
 - HTTP/2 지원을 통해 동일한 호스트에 대한 모든 요청이 소켓 공유 가능
 - connection polling 으로 요청 대기 시작 감소 (HTTP/2 사용 불가 경우)
 - Transparent GZIP으로 다운로드 크기 감소
 - 응답 캐싱으로 반복 요청에 대해 네트워크를 완전히 회피
   - **접속을 안정적이게 하면서도 속도를 개선**
 - 통신 동기화/비동기 처리 선택 가능
   - 스레드를 넘나들 순 없기에 Handler를 사용

## [Retrofit](https://square.github.io/retrofit/)
 - REST API 통신을 위해 구현된 OkHTTP 라이브러리의 상위 구현체
 - Volley가 HttpUrlConnection 랩핑이라면, Retrofit은 OkHttp 랩핑
 - syncTask 없이 백그라운드 스레드 실행
 - Callback을 통해 Main Thread에서 Ui 업데이트
 - DTO, Interface, Instance로 구성
   - **DTO (Data Transfer Object)** - Json 형식에 맞게 구현된 모델
   - **Interface** - CRUD의 동작을 어노테이션을 통해 정의한 인터페이스
   - **Instance** - 실제 레트로픽을 구현한 Retrofit Builder 클래스
     - 인스턴스, baseUrl, 컨버터 등을 설정
     - `addConverterFactory` - HTTP 통신을 통해 가져온 JSON 파일 형식을 MoshiConver을 통해 변환
     - `baseUrl` - 기본 URL (뒤에 path 를 붙여 통신)
     - `build` - retrofit 인스턴스 구성
     - `create` - 서비스를 retrofit에 연결해 인스턴스 생성
     - `.client(okHttpClient)` - okHttp 를 넣어 클라이언트 설정
