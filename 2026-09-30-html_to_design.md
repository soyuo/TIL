# HTML to Figma

**`플러그인`** : [`HTML to Figma`](https://www.figma.com/community/plugin/1687017576392405867/html-to-figma)

**`소스코드`** : [`soyuo/html-to-figma`](https://github.com/soyuo/html-to-figma)

Figma 플러그인은 DOM이 있는 UI(iframe) 와 Figma API가 있는 메인 스레드(sandbox) 로 나뉘어 있고, 둘은 postMessage로만 대화한다.

HTML → Figma 변환은 이 경계를 어떻게 나눌지가 설계의 전체다.

## 구조도

```
html-to-figma/
├─ manifest.json   # 플러그인 메타 (진입점, 네트워크 권한)
├─ code.js         # 메인 스레드: Figma API로 노드 생성
├─ ui.html         # UI iframe: 파일 업로드 + HTML 렌더링 + DOM 분석
├─ icon.png        # 게시용 아이콘 (128×128)
└─ thumbnail.png   # 게시용 썸네일 (1920×1080)
```

### 분리 사유

html 내에 javascript 를 배치하면 되지, 왜 둘이 구분되어 있을까?

가장 큰 이유로는 sandbox 환경에선 Figma API (figma.*) 를 지원하기 때문이다.

iframe 환경에선 DOM/getComputedStyle 및 네트워크, 파일 읽기가 가능하나 Figma API 사용이 불가하다.

## 파이프라인

```
HTML 파일
  │  1. 숨겨진 iframe에 렌더링
  ▼
브라우저가 계산한 레이아웃
  │  2. DOM을 돌면서 getBoundingClientRect / getComputedStyle 수집
  ▼
중간 트리(JSON)   { type, x, y, w, h, fill, layers, layout, children }
  │  3. postMessage
  ▼
code.js
  │  4. createFrame / createText / createNodeFromSvg 로 노드 생성
  ▼
Figma 캔버스 (파일마다 오른쪽 20px 간격으로 배치)
```

로직의 가장 큰 핵심은 HTML을 직접 파싱하지 않는 것이다.

브라우저가 이미 계산해 놓은 결과를 읽어서 Figma 노드로 번역하기만 하면 된다.

### 요소 → Figma 매핑

- 레이어 이름: 태그#id.class (예: div.card). 원하면 data-figma-name으로 덮어쓴다.
- Auto Layout: display:flex의 방향, gap, padding, 정렬을 그대로 옮긴다. 블록 요소는 세로로 균일하게 쌓인 경우에만 변환한다.
- 텍스트: 텍스트 노드마다 Range.getClientRects()로 위치를 재고, 한 줄이면 자동 너비로 만든다.
- 폰트: CSS font-family 목록에서 Figma에 있는 첫 폰트를 고른다. 없으면 Inter로 대체한다.

## 반 자동 요소

- 브라우저 기본 컨트롤은 DOM에 그림이 없다.
  - 체크박스, 라디오 버튼은 CSS로 그려진 게 아니라 브라우저가 직접 그린다.
  - 변환하면 사라지기에 상태(checked, disabled, accent-color)를 읽어서 직접 도형과 체크 표시를 그려 넣었다.
- ::before / ::after는 측정할 수 없다.
  - getBoundingClientRect가 안 되는 가상 요소는, 같은 스타일의 진짜 <span>으로 바꿔 끼운 뒤 측정했다.
  - 원래 가상 요소는 content:none !important로 끈다.
- 폰트 폭이 다르면 줄바꿈이 깨진다.
  - 텍스트 너비를 고정해서 만들었더니, Figma의 폰트가 조금만 넓어도 강제로 줄바꿈됐다.
  - 한 줄 텍스트는 너비를 자동으로 두어서 해결했다.
- flex-wrap은 실제로 여러 줄일 때만 켠다.
  - 항상 켜 두면 폭이 조금만 달라져도 한 줄짜리 목록이 두 줄로 넘어간다.
- CSS 그라디언트 각도는 Figma 행렬로 변환해야 한다.
  - Figma는 각도가 아니라 2×3 변환 행렬(gradientTransform)을 받는다.
  - CSS의 각도와 그라디언트 선 길이로 계산해서 만들었다.
- 목록 기호(•, 1.)는 ::marker라서 DOM에 없다.
  - 목록 종류와 순서를 직접 계산해서 텍스트로 만들어 붙였다.
- 마우스 커서 위치는 알 수 없다.
  - 플러그인 API에는 커서 좌표를 알려주는 기능이 없어서, 현재 화면 중앙(figma.viewport.center)을 시작점으로 썼다.
