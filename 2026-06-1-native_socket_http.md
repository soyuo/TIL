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
|Method|Description|
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
