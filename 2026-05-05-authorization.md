# Spring 보안 개념 정리
## Spring Security
인증, 권한 관리, 데이터 보호 등 보안 관련 기능을 체계적으로 제공하는 프레임워크.
### 동작 방식
Spring Security는 **Filter Chain** 기반으로 동작한다.
HTTP 요청이 Controller에 도달하기 전, 여러 필터를 순서대로 통과하면서 인증·인가 처리가 이루어진다.
MVC(Model-View-Controller)와 완전히 분리된 계층에서 동작하기 때문에 비즈니스 로직에 영향을 주지 않고 보안 정책을 적용할 수 있다.
```
HTTP 요청 → Filter Chain → DispatcherServlet → Controller
              (보안 처리)
```
### 특징
- 보안과 관련된 기능을 체계적으로 제공한다 (인증, 인가, CSRF 방어, XSS 방어, 세션 관리 등)
- Filter 기반으로 동작하여 MVC와 분리되어 관리된다
- 기본적으로 **세션 & 쿠키** 방식으로 인증한다 (로그인 성공 시 서버에 세션 생성, 클라이언트는 세션 ID를 쿠키에 담아 전달)
- Stateful 구조이므로 서버가 상태를 유지해야 한다
- JWT, OAuth2 등과 통합하여 다양한 인증 방식을 지원한다
---
## JWT (Json Web Token)
서버와 클라이언트가 JSON을 이용하여 가볍고 안전하게 정보를 전달하는 방법.
### 특징
- **Stateless**하다 — 세션과 달리 서버가 상태를 저장하지 않는다
- 토큰 자체에 필요한 정보를 인코딩해 저장한다
- 비밀키를 통해 위조·변조 여부를 감지한다
- DB에 별도로 상태를 저장하지 않아 자원 절약이 된다
- Scale-out(수평 확장)이 용이하다
### 세션 vs JWT
| 항목 | 세션 | JWT |
|--|--|--|
| 상태 | Stateful | Stateless |
| 저장 위치 | 서버 메모리 / DB | 클라이언트 |
| 확장성 | 어려움 (세션 공유 필요) | 쉬움 (분산 서비스 적합) |
| 강제 만료 | 즉시 가능 | 어려움 (블랙리스트 필요) |
| 보안 | 서버 측 관리 | 서명으로 무결성 보장 |
### 구조
JWT는 `.`으로 구분된 세 파트로 구성된다: `Header.Payload.Signature`
#### Header
토큰 타입 및 서명 알고리즘 정보를 담는다.
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
- Base64URL로 인코딩된다
- `alg`: 서명에 사용할 알고리즘 (HS256, RS256 등)
- `typ`: 토큰 타입 (`JWT`)
#### Payload
실제 정보(Claim)를 담는다.
```json
{
  "name": "Satori",
  "exp": 1716892800
}
```
- Base64URL로 인코딩된다 (암호화가 아님)
- **누구나 디코딩할 수 있으므로 민감한 정보(비밀번호 등)는 절대 담으면 안 된다**
- Claim 종류: Registered (표준), Public (공개), Private (사용자 정의)
#### Signature
헤더와 페이로드를 비밀키로 서명하여 위조를 감지한다.
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  비밀키
)
```
- 비밀키 없이는 유효한 서명을 생성할 수 없다
- 서버는 이 서명을 검증해 토큰의 신뢰 여부를 판단한다
- 토큰이 중간에 변조되면 서명이 달라져 즉시 감지된다
### 용도
- 사용자 인증 — 로그인 후 발급된 토큰으로 신원 확인
- 분산 서비스에서 하나의 토큰으로 여러 서비스 접근 권한 부여 (SSO)
- 정보 교환 시 데이터 무결성 보장
- 무상태 서버를 만들고 싶을 때
- Access Token + Refresh Token 전략으로 보안과 UX 균형 유지

---

## 인증(Authentication)과 인가(Authorization)
> **순서가 있다.** 인증이 먼저다.
> 신원을 확인한 뒤에 권한을 판단한다.
> 인증 없이 인가는 불가능하다.
### 인증 (Authentication) — 신원 확인
대상이 **'누구인지'** 확인하는 신원 검증 과정.
- 로그인 시 아이디 + 비밀번호를 검증하는 것이 대표적인 예
- 성공 시 JWT 발급 또는 세션 생성
- 실패 시 `401 Unauthorized` 응답
- 예시: 사원증 발급, 공항 여권 검사, 지문 인식
### 인가 (Authorization) — 접근 권한 결정
어떤 대상이 **어떤 자원에 접근할 수 있는지** 판단하는 과정.
- 인증된 사용자의 역할(ROLE)이나 권한을 기반으로 판단
- `ROLE_ADMIN`만 접근 가능한 API, `ROLE_USER`는 본인 데이터만 조회 가능한 API 등에 적용
- 실패 시 `403 Forbidden` 응답
- 예시: 사원증은 있지만 임원실 출입은 불가, 일반 회원은 관리자 페이지 접근 불가
### Spring Security에서의 처리 흐름
```
요청 수신
  → 인증 필터 (UsernamePasswordAuthenticationFilter 등)
  → AuthenticationManager → UserDetailsService
  → SecurityContext에 인증 정보 저장
  → 인가 필터 (AuthorizationFilter)
  → 권한 확인 후 Controller 진입 허용/거부
```
---
## Spring Bean · IoC · DI
> 세 개념은 분리된 것이 아니다.
> **IoC라는 원칙을 구현하는 방법이 DI이고, DI의 대상이 되는 객체가 Bean이다.**
### IoC (Inversion of Control) — 제어의 역전
객체의 생성과 의존성 관리를 **개발자가 아닌 스프링 컨테이너가 담당**하는 설계 원칙.

기존 방식에서는 개발자가 직접 객체를 생성하고 의존 관계를 설정했다:
```java
// developer control
UserRepository repo = new UserRepository();
UserService service = new UserService(repo);
```
IoC에서는 스프링 컨테이너가 대신 관리한다:
```java
// container control
@Service
public class UserService {
    private final UserRepository repo; // 컨테이너가 주입해준다
}
```
제어권이 코드에서 프레임워크로 이동하기 때문에 "제어의 역전"이라고 한다.
### DI (Dependency Injection) — 의존성 주입
IoC를 구현하는 방법. 필요한 의존 객체를 외부(컨테이너)에서 주입해주는 방식이다.
#### 생성자 주입 (권장)
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired // Spring 4.3+ 단일 생성자는 생략 가능
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```
- `final` 선언 가능 → 불변성 보장
- 테스트 코드 작성이 쉬움
- 순환 참조를 컴파일 시점에 감지
#### Setter 주입
```java
@Service
public class UserService {
    private UserRepository userRepository;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```
- 선택적 의존성에 사용
- 나중에 의존성 변경 가능 (단점이 될 수도 있음)
#### 필드 주입 (비권장)
```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```
- 코드가 간결하지만 테스트하기 어려움
- `final` 선언 불가
- 실무에서는 지양하는 방식
### Spring Bean
스프링 컨테이너(IoC 컨테이너)가 생성, 의존성 설정, 생명주기 관리 등을 수행하는 자바 객체.
#### 빈 등록 방법
```java
// 방법 1: 어노테이션으로 등록 (컴포넌트 스캔)
@Component   // 일반 컴포넌트
@Service     // 서비스 계층
@Repository  // 데이터 접근 계층
@Controller  // 웹 계층

// 방법 2: @Configuration + @Bean으로 직접 등록
@Configuration
public class AppConfig {
    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }
}
```
#### 빈의 스코프
| 스코프 | 설명 |
|--|--|
| `singleton` | 기본값. 컨테이너당 하나의 인스턴스만 생성 |
| `prototype` | 요청할 때마다 새 인스턴스 생성 |
| `request` | HTTP 요청당 하나의 인스턴스 (웹) |
| `session` | HTTP 세션당 하나의 인스턴스 (웹) |
#### 빈의 생명주기
```
컨테이너 시작
  → 빈 정의 스캔 (@Component 등)
  → 빈 인스턴스 생성
  → 의존성 주입 (DI)
  → 초기화 콜백 (@PostConstruct)
  → [사용]
  → 소멸 콜백 (@PreDestroy)
  → 컨테이너 종료
```
#### IoC 컨테이너 종류
- **`BeanFactory`**: 기본 컨테이너. 지연 로딩(Lazy Loading) 방식으로 동작한다. 빈을 요청하는 시점에 생성한다.
- **`ApplicationContext`**: BeanFactory를 확장한 컨테이너. 즉시 로딩(Eager Loading) 방식. 메시지 국제화, 이벤트 발행, 환경 변수 처리 등 추가 기능 제공. 실무에서는 대부분 ApplicationContext를 사용한다.
### 세 개념의 관계 정리
```
IoC (원칙)
  └─ "객체 제어권을 컨테이너에게 넘긴다"

DI (구현 방법)
  └─ "컨테이너가 의존 객체를 외부에서 주입한다"

Bean (대상)
  └─ "컨테이너가 관리하는 객체"
```

> IoC 원칙 덕분에 개발자는 비즈니스 로직에만 집중할 수 있고, 객체 간 결합도가 낮아져 테스트와 유지보수가 쉬워진다.
