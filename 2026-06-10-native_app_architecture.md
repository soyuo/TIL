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

### Classic (Active) / Passive MVC
- 공통 로직
  - Controller 로 사용자한테 입력을 받음, Model 에게 명령
- 이후 처리
  - |Active|Passive|
    |:-:|:-:|
    |Model은 비즈니스 로직 수행, 상태 변경, 이벤트 발생 -> View에 알림|Model은 비즈니스 로직 수행, 상태 변경|
    |View는 Model에 정보 요청, 상태 변경|Controller는 View에 상태 변경 알림|
    ||View는 Model에 정보 요청, 상태 변경| 

### Application Model MVC (AM-MVC)
- Ui 복잡섭 증가에 따라 Presentation logic 구현이 필요
  - Application model 이 추가된 해당 MVC 탄생

 - Model은 완전히 독립적
 - View 와 Controller 는 Model에 대한 의존성
 - 각 View는 하나의 Controller를 반드시 소유, 하나의 Controller는 여러개의 View에 공유 가능

> [!CAUTION]
> **안드로이드의 경우**
>
> 위의 모델로 구현할 수 없는 문제가 존재
>
> View와 COntroller의 역할, 즉 Ui와 사용자 입력을 받는 Controller의 역할을
> Widget이 위임 받아 Activity/Fragment 처리가 가능하기 때문임

## MVP
- MVC의 가장 큰 문제인 View와 Model간의 의존성, 시대 변화에 따라 Controller의 역할이 이미 위젯에 포함돼 불필요
  - 이를 개선하고자 사용자의 입력을 View에서 처리, 불필요한 Controller 대신 Presenter 컴포넌트 추가

### Supervising controller MVP
 - View와 Model간의 Observer 패턴 적용
   - Model 상태 변경시, View 업데이트

### Presenter
 - View - 사용자 입력을 전달 받고 호출
 - Model - 비즈니스 로직 수행 및 데이터 수신
   - View - 다시 전달 받아 Ui 업데이트

## MVVM
 - Reactive Programming으로 패러다임이 변함에 따라, Ui 를 가진 프로그램에서 변경사항 관찰 및 개별적 문제 해결 가능이 증명됨
   - Controller가 ViewModel로 대체된 Presentation logic과 Business logic을 분리하는 또 다른 모델
 - 현대 GUI 프로그램 개발에 Controller의 책임을 View에서 담당

 - View는 ViewModel을 알고 있으나, ViewModel은 View를 모름
   - ViewModel은 Model을 알고 있으나, Model은 ViewModel의 존재를 모름 (참조 X)
 - ViewModel의 데이터를 트리거해 View를 자동으로 업데이트하기 위해 Observer 패턴 구현 라이브러리 사용
 - View를 업데이트하는 트리거 방법 미존재
   - 외부에서 View의 업데이트 호출 불가

### ViewModel
 - View - ViewModel의 데이터를 바인딩, Ui 업데이트
 - ViewModel - Model을 바인딩, 데이터 가져오기

> [!CAUTION]
> **안드로이드의 경우**
>
> Xml 방식의 Ui 구성이면 Data binding을 통해
> ViewModel의 객체 변화를 Ui에 반영,
> 사용자의 입력을 ViewModel에 전달 가능
>
> Compose 방식의 경우
> Flow, LiveData 등을 활용해 Composable 내에서
> 데이터 수집 및 Ui 반영, 사용자 입력 등의 이벤트 발생 시 ViewModel의 메소드 호출
