# 3회차 45-프로미스, 46-async/await, 47-에러 처리

## 45. 프로미스

저번에 다루지 못한 내용 중 콜백 헬이 있었다. 말 그대로 콜백이 계속 이어져 (코드를 짜는 입장에서) 지옥을 연출하는 것이다. 질문이 꼬리에 꼬리를 문다고 생각하면 될 것 같다. 적당한 꼬꼬무는 문제의 근본에 도착할 수 있어 도움이 되지만 지나친 꼬꼬무는 고문관이다.

프로미스는 이 콜백 지옥을 뚫기 위한 해결방안이라고 보면 된다.

**왜 프로미스** 인가?

#### 45.1.2 에러 처리의 한계

- 콜백 패턴의 에러 처리가 골치아프기 때문이다. 예를 들어 `setTimeOut` 함수로 1초 뒤에 에러를 발생시키게 설계를 하면, `setTimeOut` 함수의 콜백 함수가 발생시킨 에러는 catch 블록에서 캐치되지 않기 때문이다. 왜냐면 비동기 처리를 통해 `setTimeOut`은 콜 스택에서 이미 퇴출당한 상태로 콜백 함수가 실행되기에 콜백 함수를 호출한 것은 `setTimeOut`이 아니라는 것이다.

=> 그래서 이 한계를 해결하기 위해 ES6에서 프로미스를 도입시키게 되었다.

### 45.2 프로미스의 생성

`promise`는 단어 그대로 '약속'이다. 자바스크립트에서는 아직 완료되지 않았지만, 결과를 나중에 알려주겠다고 약속한다는 의미로 보면 된다.

성공 시 `resolve()` 함수를, 실패 시 `reject()` 함수를 호출한다.

~~성공하면 `resolve`, 실패하면 `reject` 아닙니까!~~

`promise`는 기본적으로 pending 상태이지만, 비동기 처리 결과에 따라 성공 시 `fullfilled`(이행), 실패 시 `rejected`(거절) 상태가 된다.

정리

> 1.  `resolve()` 가 호출 되면 `Promise`는 fullfilled(이행) 상태가 된다.
> 2.  `reject()` 가 호출 되면 `Promise`가 rejected(거절) 상태가 된다.

### 45.3 프로미스의 후속 처리 메서드

`promise`의 상태가 변화되면 후속 처리를 해야한다. `promise`가 `fullfilled`(이행)일 경우 결과값으로 무언가를 해야하고 `rejected`(거절)일 경우 에러 처리를 해야한다.

그래서 있는 것이 `then`과 `catch`이다. `then`은 프로미스 이행 시, `catch`는 프로미스 거절 시 콜백 함수를 호츌 한다.

### 45.5 프로미스 체이닝

```js
getData(function (data) {
  getMoreData(data, function (moreData) {
    getMoreData(moreData, function (evenMoreData) {
      // 더 많은 작업...
    });
  });
});
```

- 이런 콜백 헬을 `promise`를 통해

```js
getData()
  .then((data) => getMoreData(data))
  .then((moreData) => getEvenMoreData(moreData))
  .then((evenMoreData) => {
    // 더 많은 작업...
  })
  .catch((error) => {
    console.error(error);
  });
```

- 보기도 쉽고 에러 처리도 깔끔하게 할 수 있다. **결국,** 실행이냐 거절이냐, 실행 시 어떻게 할거고 거절 시 에러를 어떻게 처리할 것이냐가 중요하다고 볼 수 있다.

### 45.6 프로미스의 정적 메서드

- 만약 `promise`를 모두 병렬 처리 하고 싶다면 `promise.all`메서드를 사용해 모든 프로미스가 fullfilled 상태가 될 때까지 돌린 다음, 모든 처리 결과를 배열에 저장해 새로운 프로미스를 반환하게 하면 된다.

### 45.7 마이크로태스크 큐

- 마이크로태스크 큐와 태스크 큐는 콜백 함수나 이벤트 핸들러를 일시적으로 저장한다는 것까지는 같다. 하지만 마이크로태스크 큐가 태스크 큐가 우선순위가 높다. 왜? 까지는 아쉽게도 딥다이브에 없다.

=> 여기서 중요한 정보는 프로미스의 후속 처리 메서드들도 **비동기**로 동작하고 이 친구들은 마이크로태스크 큐에 저장되기 때문에 이벤트 루프에서 마이크로태스크 큐에 대기하고 있는 함수를 **우선적**으로 가져와서 실행한다는 것.

> `마이크로태스크 큐` : 롯데월드 매직패스  
> `태스크 큐` : 롯데월드 자유이용권

### 45.8 fetch

- HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API.  
  => XML보다 사용법이 간단하고 프로미스를 지원한다는 이점이 있다.

`fetch` 함수가 전달하는 객체는 요청을 전송할 URL, HTTP 요청 메서드, HTTP 요청 헤더, 페이로드 등을 담는다.

```js
const promise = fetch(url, [, options]);
```

그리고 `fetch` 함수는 HTTP 응답을 나타내는 `Response` 객체를 감싼 `Promise` 객체를 반환한다.
아래처럼 `fetch`함수에 첫 번째 인수로 HTTP를 전송할 url을 넣으면 GET 요청을 전송할 수 있다.

```js
fetch("https://jsonplaceholder.typicode.com/todos/1").then((response) =>
  console.log(response)
);
```

`fetch`함수는 `Response` 객체를 감싼 프로미스를 반환하기 때문에 "후속 처리 메서드" `then`을 통해 `resolve`값을 받을 수 있다. 이 `Response`객체는 HTTP 응답을 나타내는 다양한 프로퍼티를 준다.

### fetch의 에러 처리 중 주의사항

**부적절한 URL을 지정할 경우**

```js
const ddongUrl =
  "https://jsonplaceholder.typicode.com/namdddnamdddfiowfihihi/1";

fetch(ddongUrl)
  .then(() => console.log("yesyesyes!"))
  .then(() => console.log("this is ddong;;;"));
```

변수명대로 똥같은 url이 들어갔기 때문에 원래라면 경로의 url을 찾을 수 없어 `404 Not Found`를 출력해야 하지만?? "yesyesyes!"가 출력된다.

### Why?

- `fetch`가 반환하는 프로미스는 404 에러나 500 에러가 발생해도 에러를 바로 `reject` 하지 않기 때문. "엥? 이거 실패 아닌데?" 라며 부정함. 네트워크 문제 혹은 CORS 에러에 의해 요청이 완료되지 못한 경우에만 `Promise`를 `reject`한다. 그렇기 때문에 `fetch`함수를 사용할 때 반환된 `Promise` 내부에서 `response.ok`를 찾아 에러를 처리해야 한다.

## 46. 제너레이터와 async/await

### 46.1 제너레이터란?

- 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수.

  제너레이터 함수와 일반 함수의 차이는..?

  1. 함수 호출자에게 함수 실행의 제어권을 양도.
  2. 함수 호출자와 함수의 상태를 주고 받음.
  3. 제너레이터 함수 호출 시 제너레이터 객체를 반환.

### 46.2 제너레이터 함수의 정의

### 46.3 제너레이터 객체

### 46.4 제너레이터의 일시 중지와 재개

### 46.5 제너레이터의 활용

### 46.6 async/await

## 47. 에러처리

### 47.1 에러 처리의 필요성

### 47.2 try...catch...finally 문

### 47.3 Error 객체

### 47.4 throw 문

### 47.5 에러의 전파

```

```
