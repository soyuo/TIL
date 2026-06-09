# Mobile App Architecture Pattern
> [!NOTE]
> Architecture Pattern
>
> 공통적으로 자주 발생하는 문제를 해결하는 재사용 가능한 해결 방법
>
> 여기서는 MVC, MVP, MVVM 을 다룰 예정

## Component
### View
색상, 모양, 클릭 이벤트 수신 등 화면과 레이아웃을 처리

이때, 정보는 Model 을 기반함

### Model
View 가 이곳의 데이터에 따라 렌더링하고 작동하게 하려는 비즈니스 로직 관리

데이터의 수정, 갱신에 필요한 API(or query) 제공

## 3-tier architecture
### Presentation layer
소프트웨어의 최상위 계층에 위치

사용자에게 보여주는 인터페이스 및 결과 표시를 위해 필요한 데이터를 내보내는 다른 계층과 통신

### Business layer
세부적인 처리(비즈니스 로직 등) 기능을 제어

클라이언트 요청에 대해선 정보를 제공,
Data layer에게 정보를 요청

### Data layer
Data persistence mechanism (DB 서버, 파일 공유)과
이러한 데이터를 노출하는 Dataacess layer 를 포함

## MVC
UI, 데이터 및 비즈니스 로직 구현에 흔히 쓰는 디자인 패턴

"관심사 분리"가 중점

[MVC pattern 정리하기](https://daryeou.tistory.com/310)
