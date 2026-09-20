# 일정 가능 시간대 API

일정 참여자가 날짜별 가능한 시간대를 등록하고, 참여자별 응답과 시간대별 가능 인원을 조회하는 API입니다.

## 공통 사항

- Base path: `/events/{eventId}/availability`
- 인증: `Authorization: Bearer {accessToken}`
- 응답은 프로젝트 공통 `ApiResponse` 형식을 사용합니다.
- 날짜 형식: `yyyy-MM-dd`
- 시간 형식: `HH:mm`
- 시간대는 `[startTime, endTime)` 구간으로 처리합니다.
- 같은 날짜에 다시 저장하면 해당 사용자의 기존 응답을 전체 교체합니다.

## 1. 가능 시간대 등록·수정

```http
PUT /events/{eventId}/availability
```

### Request

```json
{
  "date": "2026-09-20",
  "timeSlots": [
    {
      "startTime": "12:31",
      "endTime": "13:42"
    },
    {
      "startTime": "17:00",
      "endTime": "18:00"
    }
  ]
}
```

### Response

```json
{
  "success": true,
  "status": 0,
  "message": "가능한 시간대를 저장했습니다.",
  "data": null
}
```

일정 참여자만 등록할 수 있습니다. 동일 날짜에 재요청하면 기존 시간대가 삭제되고 새 요청으로 대체됩니다.

## 2. 내 가능 시간대 조회

```http
GET /events/{eventId}/availability/me?date=2026-09-20
```

### Response

```json
{
  "success": true,
  "status": 0,
  "message": "내 가능한 시간대를 조회했습니다.",
  "data": {
    "eventId": 1,
    "date": "2026-09-20",
    "userId": 10,
    "timeSlots": [
      {
        "startTime": "12:31",
        "endTime": "13:42"
      }
    ]
  }
}
```

## 3. 참여자별 가능 시간대 조회

```http
GET /events/{eventId}/availability?date=2026-09-20
```

### Response

```json
{
  "success": true,
  "status": 0,
  "message": "참여자별 가능한 시간대를 조회했습니다.",
  "data": {
    "eventId": 1,
    "date": "2026-09-20",
    "members": [
      {
        "userId": 10,
        "nickname": "엄",
        "colorId": "RED",
        "timeSlots": [
          {
            "startTime": "12:31",
            "endTime": "13:42"
          }
        ]
      }
    ]
  }
}
```

아직 응답하지 않은 참여자도 `timeSlots: []`로 포함됩니다.

## 4. 시간대별 가능 인원 조회

```http
GET /events/{eventId}/availability/summary?date=2026-09-20
```

### Response

```json
{
  "success": true,
  "status": 0,
  "message": "시간대별 가능한 인원을 조회했습니다.",
  "data": {
    "eventId": 1,
    "date": "2026-09-20",
    "participantCount": 3,
    "respondedCount": 3,
    "timeSlots": [
      {
        "startTime": "12:31",
        "endTime": "13:10",
        "availableCount": 1,
        "availableMemberIds": [10],
        "isAvailableForEveryone": false
      },
      {
        "startTime": "13:10",
        "endTime": "13:42",
        "availableCount": 3,
        "availableMemberIds": [10, 11, 12],
        "isAvailableForEveryone": true
      }
    ]
  }
}
```

### 겹치는 시간대 계산 규칙

각 사용자의 시작·종료 시각을 모두 경계로 사용해 구간을 분리합니다.

```text
엄: 12:31~13:42
아:  13:10~14:05

결과:
12:31~13:10 → 1명
13:10~13:42 → 2명
13:42~14:05 → 1명
```

같은 사용자가 중복 시간대를 등록한 경우에는 한 명으로만 계산합니다. 인원 구성이 동일한 인접 구간은 하나로 합쳐 반환합니다.

## 5. 가능 시간대 삭제

```http
DELETE /events/{eventId}/availability?date=2026-09-20
```

### Response

```json
{
  "success": true,
  "status": 0,
  "message": "가능한 시간대를 삭제했습니다.",
  "data": null
}
```

## 오류

| 상태 코드 | 상황 |
| --- | --- |
| 400 | 날짜·시간 형식 오류, 시작 시간이 종료 시간보다 늦거나 같은 경우 |
| 401 | 인증 정보가 없거나 만료된 경우 |
| 403 | 일정 참여자가 아닌 경우 |
| 404 | 일정을 찾을 수 없는 경우 |
