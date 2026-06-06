# Python 에서 HTTP 서버 열기
 - with `http.server`

> [!NOTE]
> 해당 글은 밑의 글을 참조함 (기록용!)
>
> [로컬 웹 서버 열기](https://velog.io/@hamham/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-HTTP-%EB%A1%9C%EC%BB%AC-%EC%9B%B9-%EC%84%9C%EB%B2%84-%EB%9D%84%EC%9A%B0%EA%B8%B0http.server-%ED%99%9C%EC%9A%A9)

## http.server
- python [http.server](https://python.flowdas.com/library/http.server.html)
- 해당 모듈은 HTTP 서버(웹 서버)를 구현하기 위한 클래스를 정의한다
> 모듈의 설명은 나중에 추가될 예정!

### Example
```py
from http.server import HTTPServer, SimpleHTTPRequestHandler # http.server 에서 '서버'와 '요청 핸들' 을 가져옴

HOST = "0.0.0.0" # 모든 네트워크 인터페이스에 대해 바인딩되는 호스트
PORT = 3000 # 서브 포트

server = HTTPServer((HOST, PORT), SimpleHTTPRequestHandler) # 해당 호스트와 포트에 핸드러를 가지고 서버 설정
print(f"Serving on http://localhost:{PORT}")
server.serve_forever() # 서버 열기
```

> 2026.06.07 초안
> 이후, 추가 기제
