# 1️⃣ 비동기 프로그래밍, 저는 이렇게 이해했어요!

## 0. 비동기란 무엇일까?

**비동기(asynchronous)**란,  
작업이 요청된 즉시 결과가 주어지는 것이 아니라 **그 결과가 준비되었을 때 나중에 처리되는 방식**을 의미한다.

쉽게 말하면,  
어떤 작업을 요청하고 그 결과가 올 때까지 기다리지 않고, 그 사이에 다른 작업을 먼저 처리할 수 있도록 프로그래밍 방식이다.

예를 들어,  
점심시간에 김밥집에 가서 김밥을 주문한 후 기다리지 않고, 다이소에 들러 공책을 사 오는 행동은 비동기적인 처리 방식이라고 볼 수 있다.

이처럼 작업 요청과 작업 처리의 타이밍이 분리되는 것이 비동기의 핵심이다.  
반대로, 동기(synchronous) 방식은 주문한 후 아무것도 하지 않고 계속 기다리는 구조이다.

이때 싱글 스레드 방식으로 동작하는 것은 브라우저가 아니라 브라우저에 내장된 JavaScript 엔진이라는 것에 주의해야 한다.
만약 모든 JavaScript 코드가 JavaScript 엔진에서 싱글 스레드 방식으로 동작한다면 JavaScript는 비동기로 동작할 수 없다.
즉, JavaScript 엔진은 싱글 스레드로 동작하지만 브라우저는 멀티 스레드로 동작한다.

## 1. JavaScript는 왜 비동기가 필요할까?

JavaScript는 **싱글 스레드** 언어이다.  
즉, 한 번에 오직 한 가지 작업만 처리할 수 있다.  
이런 구조에서는 어떤 작업이 오래 걸리면 다른 작업들이 모두 대기 상태에 놓이게 된다.  
즉, **블로킹(작업 중단)**이 발생한다.

예를 들어, 서버에 데이터를 요청했는데 응답이 3초 걸린다면, 그동안 JavaScript는 아무 일도 하지 못한 채 기다리게 된다.
이러한 상황은 사용자 경험을 떨어뜨릴 수 있으므로 **기다리지 않고도 다른 작업을 처리할 수 있는 구조**가 필요하다.
이렇게 해서 등장한 개념이 비동기 프로그래밍이다.

## 2. JavaScript의 비동기 구조: 이벤트 루프

비동기 처리를 가능하게 해주는 구조는 다음과 같다:  
· 콜 스택(Call Stack) : 현재 실행 중인 작업들이 쌓이는 공간.  
· 테스크 큐(Task Queue) : 나중에 실행될 콜백 함수들이 대기하는 공간.  
· 이벤트 루프(Event Loop) : 콜 스택이 비었을 때, 테스크 큐에서 대기 중인 작업을 하나씩 꺼내 실행시켜주는 역할.

이 구조 덕분에 JavaScript는 싱글 스레드임에도 불구하고 멈추지 않는 처리 흐름을 만들어낼 수 있는 것이다.

## 3. callback ➡️ Promise ➡️ async/await로의 진화

### 3-1. callback 함수

callback 함수는 개념적으로 단순하며 사용하기도 쉬웠다고 한다.
하지만 여러 개의 비동기 작업이 중첩되거나 순차적으로 이루어져야 할 경우, callback 함수들이 계속해서 안쪽으로 중첩되는 이른바 "콜백 지옥(callback hell)" 문제가 발생하게 되었다.

```js
loginUser('Henry', function (user) {
  getProfile(user, function (profile) {
    getPosts(profile, function (posts) {
      console.log(posts);
    });
  });
});
```

이처럼 중첩 구조가 깊어지면 가독성이 떨어지고, 에러 핸들링도 어렵고, 중간 단계에서 흐름을 분기하거나 멈추는 것도 까다로워진다.

### 3-2. Promise

이러한 callback 함수의 한계를 극복하기 위해 등장한 것이 Promise이다.
Promise는 비동기 작업의 결과를 나중에 사용할 수 있는 객체 형태로 감싸는 방식이다.

```js
loginUser('Henry')
  .then(getProfile)
  .then(getPosts)
  .then(console.log)
  .catch(console.error);
```

Promise는 `.then()`을 사용한 체이닝 방식으로 비동기 흐름을 평면적으로 표현할 수 있다.
이로 인해 코드의 가독성이 크게 개선되고, `.catch()`를 통한 일괄적인 에러 처리도 가능해졌다.

하지만 Promise도 여러 비동기 흐름이 조건에 따라 달라지는 경우, 여전히 체이닝이 길어지고 복잡해질 수 있으며, 비동기 흐름을 마치 동기 코드처럼 직관적으로 작성하기에는 아쉬운 점이 있었다.

## 3-3 async / await

이러한 불편함을 해결하기 위해 등장한 것이 async / await 문법이다.
async 키워드를 붙인 함수 내부에서는 await 키워드를 사용할 수 있으며, 이 키워드는 Promise가 처리될 때까지 기다렸다가 결과를 반환해준다.

```js
async function showUserPosts() {
  try {
    const user = await loginUser('Henry');
    const profile = await getProfile(user);
    const posts = await getPosts(profile);
    console.log(posts);
  } catch (e) {
    console.error(e);
  }
}
```

async / await은 동기 코드처럼 보이지만 내부적으로는 비동기적으로 동작한다.
이로 인해 가독성이 극적으로 향상되고, 로직 흐름도 훨씬 직관적으로 표현할 수 있다.
또한 `try...catch`문을 사용한 예외 처리로 에러 흐름 제어도 동기 코드처럼 가능하다.

---

# 2️⃣ AJAX, 저는 이렇게 이해했어요!

## 0. AJAX란 무엇이고, 왜 생겼을까?

### 🔙 AJAX의 등장 배경

과거의 웹은 사용자의 모든 행동이 **페이지 전체의 새로고침**을 유발하는 방식이었다.

예를 들어:

> SNS 사이트에 접속하면, 브라우저는 주소창의 URL을 기반으로 서버에 요청을 보낸다.
> 서버는 HTML, CSS, JavaScript 파일과 이미지 등 모든 리소스를 다시 내려보낸다.
> 브라우저는 이 파일들을 받아 전체 페이지를 다시 그리고(re-render) 사용자에게 보여준다.

사용자가 게시글의 **좋아요 버튼 하나만 눌러도**, 브라우저는 서버로 해당 요청을 보내고, 서버는 좋아요 상태가 반영된 전체 페이지의 리소스를 다시 전송한다.

결국, 바뀐 것은 단 하나의 버튼 상태일 뿐인데도, 웹페이지는 마치 **문서를 닫고 새 문서를 여는 것처럼** 모든 걸 갈아엎어야 했다.

### 🚀 AJAX의 해결 방식

AJAX는 이런 불필요한 새로고침과 리소스 낭비를 없애기 위해 등장하였다.

> 사용자가 좋아요 버튼을 누르면, 브라우저는 서버에 **좋아요 상태 변경 요청(HTTP)만** 보낸다.
> 서버는 데이터베이스 작업을 처리한 뒤, **업데이트된 데이터(예: 좋아요 수, 상태)만**을 응답으로 보낸다.
> 브라우저는 이 데이터만 받아서 해당 UI만 **부분적으로 업데이트**한다.

이처럼 AJAX는 **페이지 전체를 다시 로드하지 않고**, 필요한 데이터만 주고받으며, 사용자와의 **실시간 상호작용**을 가능하게 한다.

## 1. AJAX는 어떤 식으로 작동하는가?

AJAX는 내부적으로 XMLHttpRequest 객체 또는 **fetch API**를 사용하여 비동기 요청을 보낸다.
아래는 XMLHttpRequest를 사용하는 기본 구조이다:

```js
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://api.example.com/data');
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(JSON.parse(xhr.responseText));
  }
};
xhr.send();
```

이 코드는 서버에 GET 요청을 보내고, 응답을 받아서 파싱한 후 콘솔에 출력하는 예제이다.

하지만 요즘은 `fetch()` 함수가 더 간편하게 사용된다:

```js
fetch('https://api.example.com/data')
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

### 😲 내가 느낀 점

AJAX를 배우고 나서, "우리가 버튼 하나 눌렀을 때도 페이지가 바뀌지 않고 바로 반영되는 이유가 이거였구나." 하고 이해하게 되었다.

## 2. JSON이란 무엇인가?

JSON(JavaScript Object Notation)은 클라이언트와 서버 간에 데이터를 주고받을 때 사용하는 텍스트 기반의 데이터 포맷이다.
JavaScript 객체와 매우 유사한 구조를 가지고 있으며, 간결하고 읽기 쉬워서 대부분의 API와 통신 형식으로 사용되고 있다.

### JSON 표기 방식

· 반드시 **큰따옴표**를 사용해야 한다.  
· number, boolean, array, object 등의 값도 포함 가능하다.

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

### JSON.stringify

· 객체를 문자열로 변환할 때 사용하는 메서드이다.

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis'],
};

const json = JSON.stringify(obj);
console.log(json);
// "{"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}"
```

### JSON.parse

· 서버에서 받은 JSON 응답을 다시 JavaScript 객체로 변환할 때 사용한다.

```js
const json = '{"name":"Lee","age":20}';
const parsed = JSON.parse(json);

console.log(parsed.name); // "Lee"
```

💡 JSON.stringify()와는 반대 방향의 작업이다.

### XMLHttpRequest

AJAX를 구현하는 고전적인 방식 중 하나로, JavaScript에서 직접 HTTP 요청을 보낼 수 있게 해주는 객체이다.

#### 객체 생성

```js
const xhr = new XMLHttpRequest();
```

#### HTTP 요청 전송 순서

XMLHttpRequest 객체를 이용해 서버로 요청을 보내는 과정은 총 3 단계로 이루어진다:

🔸 1 단계 : `open()` 메서드로 HTTP 요청을 초기화한다.

```js
xhr.open(method, url, async);
```

이 메서드는 HTTP 요청을 어떤 방식으로 보낼지를 미리 설정하는 단계이다.
이때 실제로 요청이 보내지는 것은 아니고, "나 이 주소로 이런 방식으로 요청을 보낼 거야!"라고 설정만 해두는 단계이다.

| 매개변수 | 설명                                                                      |
| -------- | ------------------------------------------------------------------------- |
| method   | 요청 방식 지정 (예: `"GET"`, `"POST"`, `"PUT"`, `"DELETE"` 등)            |
| url      | 요청을 보낼 대상 URL (예: `"https://example.com/api/data"`)               |
| async    | 비동기 여부 설정 (`true`면 비동기, `false`면 동기 실행 / 기본값은 `true`) |

```js
xhr.open('GET', 'https"//example.com/data', true);
```

이 코드는 GET 방식으로 example.com/data 주소에 비동기 요청을 보낼 준비를 한다는 뜻이다.

🔸 2 단계 : (필요에 따라) `setRequestHeader()` 메서드로 HTTP 요청 헤더 설정

```js
xhr.setRequestHeader('Content-Type', 'application/json');
```

요청 본문(body)이 포함되는 POST/PUT 요청 등에서는 서버가 받을 데이터의 타입(MIME 타입)을 미리 알려줘야 한다.  
이걸 설정해두지 않으면 서버가 제대로 해석하지 못할 수 있다.

🔸 3 단계 : `send()` 메서드로 요청 전송

```js
xhr.send(); // GET 요청처럼 body가 없을 때
```

```js
xhr.send(JSON.stringify({ id: 1, content: 'Hi' })); // POST 요청처럼 body가 있을 때
```

이 메서드를 호출하면 실제로 서버로 HTTP 요청이 전송된다.  
`send()`는 위에서 `open()`과 `setRequestHeader()`로 설정한 값들을 기반으로 요청을 조합해서 보내는 역할을 한다.

🛜 요청 전송 흐름 전체 예시

```js
const xhr = new XMLHttpRequest();

// 1. 요청 설정
xhr.open('POST', 'https://api.example.com/posts', true);

// 2. 헤더 설정 (JSON 데이터 전송)
xhr.setRequestHeader('Content-Type', 'application/json');

// 3. 전송
const body = JSON.stringify({ title: 'AJAX', completed: false });
xhr.send(body);
```

---

# 3️⃣ REST API, 저는 이렇게 이해했어요!

## 0. REST API란?

REST 원칙을 따르며 만들어진 API를 "REST API"라고 한다.

> 🧐 여기서 잠깐! REST는 무엇일까?  
> REST(Representational State Transfer)는 HTTP 프로토콜을 기반으로 리소스(자원)에 접근하는 방식을 정의한 아키텍처 스타일이다.

REST API를 흔히 RESTful API라는 표현도 쓰이는데, 이는 REST 아키텍처의 설계 원칙을 잘 지킨 API를 의미한다. REST API는 크게 3가지 요소로 구성된다.

| 구성 요소            | 설명                    | 표현 방법                  |
| -------------------- | ----------------------- | -------------------------- |
| 자원(Resource)       | 접근하려는 대상         | URI (예: `/todos/1`)       |
| 행위(Verb)           | 자원에 대한 요청 방식   | HTTP 메서드 (GET, POST 등) |
| 표현(Representation) | 요청/응답 데이터의 형식 | JSON, XML 등               |

## 1. REST API 설계 원칙

1. URI에는 리소스를 표현해야 한다  
   · URI는 명사형으로 리소스(자원)를 나타내야 하고, 동사를 포함하면 안 된다.  
   · 예시:  
   ❌ `/getTodos/1` ➡️ 동사 포함 🙅🏻‍♀️  
   ✅ `/todos/1` ➡️ 명사 중심의 자원 표현 🙆‍♀️

2. HTTP 요청 메서드로 행위를 표현해야 한다  
   HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법이다.  
   주로 5가지 요청 메서드(GET, POST, PUT, PATCH, DELETE 등)를 사용하여 CRUD를 구현한다.

| HTTP 메서드 | 의미      | 용도             |
| ----------- | --------- | ---------------- |
| GET         | 조회      | 리소스 가져오기  |
| POST        | 생성      | 새 리소스 생성   |
| PUT         | 전체 수정 | 리소스 전체 대체 |
| PATCH       | 부분 수정 | 리소스 일부 수정 |
| DELETE      | 삭제      | 리소스 제거      |
