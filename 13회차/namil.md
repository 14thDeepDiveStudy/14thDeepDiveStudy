# 38장 브라우저의 렌더링 과정

Node.js의 등장으로 자바스크립트는 웹이라는 틀을 벗어나 범용으로 사용이 가능한 만능 엔터테이너가 되었다.  
하지만 사람이 여러가지를 하더라도 본업 모먼트가 멋있는 것 처럼 자바스크립트도 마찬가지.  
본업인 웹 브라우저 환경에서 동작하는 웹페이지/애플리케이션의 클라이언트 사이드에서 빛이 난다.

자바스크립트는 브라우저에서 HTML, CSS와 함께 실행되기 때문에 브라우저는 이 세 친구들을 파싱(해석)하여 브라우저에 시각적으로 나타내준다(렌더링).

## 38.1 요청과 응답

브라우저 렌더링은 **서버와의 대화**에서 시작된다.
사용자가 주소창에 URL을 입력하면 브라우저는 먼저 **DNS(Domain Name System)** 를 통해 도메인 이름을 IP 주소로 변환한다.
그다음 해당 서버에 HTTP 요청(Request) 을 보내고, 서버는 HTML, CSS, JS 등의 파일을 응답(Response) 으로 돌려준다.
이 과정에서 브라우저는 받은 HTML 문서를 해석하며, 내부적으로 **“다음에 어떤 리소스를 요청해야 할까?”**를 즉석에서 판단한다.
즉, 브라우저는 **‘필요할 때마다 요청하는 즉시형 통신자’**다.

```html
<!DOCTYPE html>
<html lang="ko-KR">
  <head>
    <meta charset="UTF-8" />
    <title>document</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <h1>Hello World!</h1>
    <img src="logo.png" alt="브라우저 로고" />
    <script src="app.js"></script>
  </body>
</html>
```

위 HTML을 요청하면 브라우저는
① index.html →
② style.css →
③ logo.png →
④ app.js
순으로 리소스를 요청한다.

---

### 브라우저 내부에서는..?

1. DNS 조회로 IP 주소를 확인한다.

2. 서버로 HTTP 요청 전송.

3. HTML 문서를 응답으로 받는다.

4. HTML을 파싱하면서 외부 리소스 태그(link, script 등)를 만나면 추가 요청을 보낸다.

> HTML 파싱 중 발견되는 외부 리소스는 필요 시점에 즉시 요청된다.

---

## 38.2 HTTP 1.1과 HTTP 2.0

브라우저와 서버는 **HTTP** 로 대화한다.  
HTTP/1.1에서는 한 커넥션당 한 요청·응답만 가능했기 때문에 리소스가 많을수록 병목이 발생했다.  
HTTP/2에서는 **멀티플렉싱(multiplexing)** 기술을 도입해 **여러 요청을 동시에 병렬 전송**할 수 있다.  
그 결과 렌더링 속도는 대폭 향상되었다.

> 렌더링 최적화에서 중요한 것은 네트워크 병목 제거.

---

## 38.3 HTML 파싱과 DOM 생성

HTML은 단순한 텍스트다. 브라우저는 이 텍스트를 파싱(parsing) 하여 DOM 으로 변환한다.  
이때 HTML은 문자 → 토큰 → 노드 → 트리(DOM) 순서로 변환된다.  
DOM은 자바스크립트가 접근할 수 있는 브라우저 내 문서 객체 구조다.

```html
<ul>
  <li id="apple">Apple</li>
  <li id="banana">Banana</li>
</ul>

<script>
  const fruit = document.querySelector('#apple');
  console.log(fruit.textContent);
</script>
```

1. HTML 문서 → 바이트 → 문자 → 토큰으로 변환.

2. 토큰 → 노드로 변환하여 부모-자식 관계를 트리로 구성.

3. 완성된 DOM은 자바스크립트가 조작할 수 있는 객체 모델이 된다.

---

## 38.4 CSS 파싱과 CSSOM 생성

HTML 파싱 중 `<link>`나 `<style>` 태그를 만나면 브라우저는 CSS 파싱을 시작한다.
CSS는 HTML처럼 문자 → 토큰 → 노드 → CSSOM(CSS Object Model) 으로 변환된다.
CSSOM은 스타일 상속과 우선순위를 반영한 구조로, 최종 렌더링 시 스타일 계산의 기반이 된다.

```css
body {
  font-size: 18px;
  color: #333;
}

ul li {
  list-style: none;
  color: #fff;
}
```

1. HTML 파서가 CSS 리소스를 발견 → CSS 요청.

2. CSS 파서가 규칙을 토큰화하고 상속 관계를 트리로 구성.

3. CSSOM이 생성되면 HTML 파싱이 재개된다.

> `display: none`과 같은 비가시 요소는 렌더 트리에 포함되지 않는다.

---

## 38.5 렌더 트리(Render Tree) 생성

DOM과 CSSOM이 결합해 렌더 트리(Render Tree) 를 만든다.  
이 트리는 실제 화면에 표시될 요소만 포함하며, `meta`, `script`, `display: none` 등은 제외된다.  
이후 배치 계산(Layout) 과 그리기(Paint) 과정으로 이어진다.

```html
<style>
  .hidden { display: none; }
</style>

<div class="hidden">이거 보면 1억</div>
<div class="visible">잘 보이쥬?</div>
```

1.브라우저는 `display: none` 요소를 렌더 트리에 넣지 않는다.

2. 하지만 `visibility: hidden`은 공간을 차지하므로 포함된다.

3. 이후 렌더 트리를 기반으로 배치(Layout) → 그리기(Paint)가 순차적으로 진행된다.

> 렌더 트리 → 레이아웃 → 페인트 = 화면 표시 3단계.  
> 렌더링 성능 개선의 핵심은 불필요한 렌더 트리 재계산 방지.

## 38.6 자바스크립트 파싱과 실행

HTML 파싱 중 `<script>`를 만나면 렌더링이 일시 중단된다.  
JS 엔진(V8 등)이 코드를 토큰화 → 파싱 → AST 생성 → 실행하며 DOM을 조작한다.  
JS 실행이 끝나야 다시 HTML 파싱이 재개된다.

```html
<ul>
  <li id="item">JS 실행 전</li>
</ul>
<script>
  const li = document.querySelector('#item');
  li.textContent = 'JS 실행 후';
</script>
```
1. HTML 파싱 중 JS 발견 시 렌더링 엔진이 멈춘다.

2. JS 엔진으로 제어권이 넘어감.

3. JS 실행 후 DOM 조작 → 렌더 트리 갱신 → 다시 파싱 재개.

> JS는 렌더링을 블로킹(blocking) 할 수 있다. 또한 JS 실행 중에는 HTML 파싱이 멈춘다. `defer`로 해결 가능

---

## 38.7 리플로우와 리페인트

DOM이나 스타일이 변경되면 브라우저는 다시 렌더링을 해야 한다.  
레이아웃이 변경되면 리플로우(Reflow), 시각적 속성만 바뀌면 리페인트(Repaint) 가 발생한다.  
리플로우는 연산 비용이 크기 때문에 최대한 줄이는 것이 성능 핵심 포인트.

```js
// 리플로우 발생 (크기, 위치 변경)
element.style.width = '200px';
element.style.height = '100px';

// 리페인트만 발생 (색상 변경)
element.style.backgroundColor = 'red';
```

1. 리플로우: 레이아웃 재계산 후 다시 페인트.

2. 리페인트: 색상, 그림자 등 시각적 요소만 다시 그림.

3. 연속된 스타일 변경 시 cssText나 `classList`로 한 번에 처리하면 효율적이다.

> 리플로우는 “레이아웃 공사”, 리페인트는 “페인트 칠하기”. 성능 최적화의 핵심은 **리플로우 최소화**.
> `transform`, `opacity`를 활용하면 GPU 렌더링으로 리플로우를 줄일 수 있다.

---

## 38.8 자바스크립트 파싱에 의한 HTML 파싱 중단

JS는 DOM을 조작할 수 있으므로 HTML 파싱을 잠시 멈춘다.
파싱 중단 시점에 DOM이 완성되지 않았다면 `null` 에러가 발생할 수 있다.
이 문제는 script를 body 하단에 배치하거나 `defer` 속성을 사용해 해결한다.

```html
<head>
  <script>
    console.log(document.getElementById('title')); // null
  </script>
</head>
<body>
  <h1 id="title">렌더링 중...</h1>
</body>
```

1. 브라우저가 `<script>` 태그를 만나면 HTML 파싱을 멈춤.

2. JS 실행 중 DOM 접근 시 아직 생성되지 않은 요소는 `null`.

3. 해결법: <body> 끝에 배치 or `defer` 사용.

> 스크립트 실행 시점이 중요하다. **DOM 접근 전에 파싱이 끝났는지 항상 확인하자.**

---

## 38.9 script 태그의 async / defer 어트리뷰트

HTML5에서 추가된 `async`와 `defer` 속성은 JS의 렌더링 차단 문제를 해결한다.
둘 다 JS를 비동기로 로드하지만, 실행 시점이 다르다.

`async`: HTML 파싱과 병렬로 다운로드하며, 다운로드 완료 시 즉시 실행된다. 실행 시점이 불규칙하지만 빠르다.

`defer`: HTML 파싱 완료 후 실행되며, `DOMContentLoaded` 이벤트 직전에 실행된다.
즉, `defer`는 실행 타이밍이 안정적이므로 대부분의 경우 권장된다. 이 두 속성은 브라우저의 렌더링 차단 문제를 근본적으로 완화해준다.

```html
<!-- async: 다운로드 완료 즉시 실행 -->
<script async src="analytics.js"></script>

<!-- defer: HTML 파싱 완료 후 실행 -->
<script defer src="main.js"></script>
```

1. `async`: HTML 파싱과 병렬로 JS 로드 → 로드 완료 즉시 실행.

2. `defer`: HTML 파싱과 병렬 로드 → 파싱 완료 후 실행.

3. `type="module"` 스크립트는 기본적으로 `defer`처럼 동작한다.

---

> `async`: 독립적인 스크립트에 적합 (예: 광고, 분석 코드).
>
> `defer`: DOM 의존 스크립트에 이상적.
>
> `type="module"`은 자동 `defer` + 독립 스코프 + `import`/`export` 지원.

---