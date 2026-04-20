# Spring Annotation 정리
## 1. Annotation이란?
`@`를 붙여 **코드에 기능이나 정보를 부여하는 메타데이터**

- Spring이 스캔 후 자동 처리 -> 직접 코드 작성 불필요
- XML 설정 제거, 객체 관리 자동화, 가독성 향상

## 주요 Annotation
### DI (의존성 주입)
|Annotation|Role|
|--|--|
|`@Autowired`|타입 기준 자동 주입|
|`@Qualifier`|같은 타입 중 특정 Bean 선택|
|`@Resource`|이름 기준 주입|

> 객체를 직접 생성하지 않고 주입받음

### Bean 등록
|Annotation|Role|
|--|--|
|`@Component`|기본 Bean 등록|
|`@Controller`|요청 처리|
|`@Service`|비즈니스 로직|
|`@Repository`|DB 접근|

> 객체를 Spring 컨테이너에 등록

### 웹 / REST
|Annotation|Role|
|--|--|
|`@RequestMapping`|URL 매핑|
|`@GetMapping` / `@PostMapping`|요청 방식 구분|
|`@RequestParam`|쿼리 파라미터 수신|
|`@PathVariable`|URL 경로 값 수신|
|`@ResponseBody`|JSON 반환|
|`@RestController`|`@Controller` + `@ResponseBody`|

> 요청을 메서드와 연결하고 데이터를 반환

### 기타
|Annotation|Role|
|--|--|
|`@Transactional`|트랜잭션 처리|
|`@ExceptionHandler`|예외 처리|

## 3. 동작 원리
```
Annotation 선언 -> Spring 스캔 -> Reflection 처리 -> 객체 생성 -> 의존성 주입 -> 요청 처리
```

- 실행 전 Spring이 미리 스캔하여 준비
