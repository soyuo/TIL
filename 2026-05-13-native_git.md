# Git과 GitHub 기본 흐름 정리
Git과 GitHub는 다름

- `Git`은 버전 관리를 위한 도구
- `GitHub`는 Git 저장소를 원격으로 올려두고 공유할 수 있는 플랫폼

`Git`만으로도 버전 관리는 가능하고,
`GitHub`를 함께 쓰면 원격 저장과 협업이 가능

## GitHub 사용 사유
크게 세 가지로 정리

- 백업
- 협업
- 안정적인 버전 공유

코드를 내 컴퓨터에만 두면 문제가 생겼을 때 복구 불가능
하지만 GitHub에 올려두면 원격 서버에 저장돼서 백업이 가능

여러 명이 협업할 때 같은 저장소를 기준으로 버전을 맞출 수 있어서 편리

## local과 remote
Git과 GitHub를 이해할 때 `local`과 `remote`를 구분

- `local`: 내 PC에 있는 저장소
- `remote`: GitHub에 있는 저장소

작업은 로컬에서 하고, 필요할 때 GitHub와 연결해서 올리거나 받아오는 방식으로 사용

## Git 관리 대상
파일을 복사만 하는 게 아닌 파일의 변화를 자동으로 관리

- 파일 추가
- 파일 수정
- 파일 삭제
- 여러 작업 내용 병합

> 버전별로 분리해 복사해둘 필요 없이, 변경 이력을 기준으로 파일 상태를 관리

## push
`push`는 로컬에서 만든 커밋을 원격 저장소로 업로드하는 것

```bash
git init
git add .
git commit -m "message"
git branch -M main
git remote add origin https://github.com/USERNAME/REPOSITORY.git
git push -u origin main
```

### 1. `git init`
```bash
git init
```

현재 폴더를 Git 저장소로 초기화

### 2. `git add .`
```bash
git add .
```

현재 폴더의 변경 파일을 스테이지에 설정
- `.`은 현재 폴더의 전체 파일을 의미

모든 파일을 바로 커밋하는 것이 아닌
먼저 "이 파일들을 커밋 대상으로 삼겠다"라고 표시하는 단계

### 3. `git commit -m "message"`
```bash
git commit -m "message"
```

스테이지에 올라간 파일들을 하나의 버전으로 저장

- `commit`: 버전 저장
- `-m`: 메시지 작성
- `"message"`: 스테이지명

> 커밋 - 현재까지의 작업 내용을 하나의 기록으로 남기는 것

### 4. `git branch -M main`
```bash
git branch -M main
```

현재 브랜치 이름을 `main`으로 변경

### 5. `git remote add origin ...`
```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

로컬 저장소와 GitHub 저장소를 연결

`origin`은 원격 저장소를 가리킴
> 실주소를 길게 쓰지 않고 `origin`이라는 이름으로 부르는 것

### 6. `git push -u origin main`
```bash
git push -u origin main
```

- `push`: 업로드
- `origin`: 연결된 원격 저장소
- `main`: 업로드할 브랜치
- `-u`: 이후 기본 연결 정보로 기억

처음 연결 뒤에는 다음과 같이 간략화 가능

```bash
git push
```

## pull의 의미
GitHub에 있는 변경 내용을 내 로컬 저장소로 가져오는 것

```bash
git pull origin main
```

- `origin`에 있는
- `main` 브랜치 내용을
- 현재 내 로컬 브랜치로 가져와 반영

- `push`: local -> remote
- `pull`: remote -> local

## stage와 commit의 의미
기본 흐름은 다음과 같다.

1. 파일 수정
2. 수정한 파일을 stage에 업로드
3. stage에 올라간 파일을 commit

```bash
git add .
git commit -m "Update README"
```

`git add .`은 커밋이 아니라 스테이징
> "이 변경사항을 다음 커밋에 포함하겠다"

`git commit` : 그 스테이징된 내용만 실제 버전 기록에 남김

## branch 흐름
브랜치는 작업을 분리해서 관리할 수 있게 해줌

- `main`: 안정적이고 배포 가능한 브랜치
- `dev`: 개발 중인 내용을 모으는 브랜치
- `feature` 또는 `fix`: 기능 개발이나 버그 수정을 위한 개별 브랜치

```text
main
└─ dev
   └─ feature/login
   └─ feature/signup
   └─ fix/security-error
```

### 요약
- `Git` 로컬에서 버전을 관리하는 도구
- `GitHub` Git 저장소를 원격으로 저장하고 공유하는 서비스
- `git add .` 커밋이 아니라 스테이징
- `git commit -m "message"` 스테이징된 내용을 하나의 버전으로 저장
- `git push` 로컬 커밋을 GitHub에 올리는 것
- `git pull` GitHub의 변경사항을 로컬로 가져오는 것
- `origin`은 원격 저장소를 가리키는 일반적인 이름이다.
- `main`, `dev`, `feature/fix` 브랜치를 나누면 협업과 버전 관리가 쉬워진다.
