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
  => XML보다 사용법이 간단하고 프로미스를 지원한다는 이점이 있다. = 콜백 패턴에서 자유롭다

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

- `function*` 키워드로 선언하고 하나 이상의 `yield` 표현식을 포함해 정의한다는 부분만 빼면 일반 함수의 정의 방법과 같다. 참고로 화살표 함수로 정의할 수 없음.

> ```js
> // 함수 선언문
> function* namilc() {
>   yield 1;
> }
>
> // 함수 표현식
> const namilc2 = function* () {
>   yield 2;
> };
>
> // 제너레이터 메서드
> const obj = {
>   * genObjMeyhod() {
>     yield 1;
>   },
> };
> ```

### 46.3 제너레이터 객체
- 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 호출하는 것이 아니라 제너레이터 객체를 생성한다.  
  - 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.
  - Symbol.iterator 메서드를 상속받는 이터러블이다.
  - value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터이다.
- 제너레이터 객체는 next 메서드를 갖는 뿐만 아니라 `return`, `throw` 메서드를 갖는다.
- next 메서드를 호출하면 `yield` 표현식까지 코드 블록을 실행하고, `yield`된 값을 value 프로퍼티 값으로, `false`를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
- `return` 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, `true`를 done 프로퍼티 값으로 갖는 이터레이터 `result` 객체를 반환한다.

```js
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.return("End!")); // {value: 'End!', done: true}
console.log(generator.throw("Error!")); // {value: undefined, done: true}
```
### 46.4 제너레이터의 일시 중지와 재개

- 제너레이터는 `yield` 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개할 수 있다.
- 제너레이터 객체의 next 메서드를 호출하면 `yield` 표현식까지만 실행한다.
> 이 때, 함수의 제어권이 호출자로 양도된다.
- 호출자가 또 다시 next 메서드를 호출하면 일시 중지된 코드부터 실행을 재개하여 다음 `yield` 표현식까지 실행되고 또 다시 일시 중지된다.

### 46.6 async/await

- async/await는 프로미스 기반으로 동작한다.
- 동기 처리처럼 프로미스를 사용할 수 있다.
  - 프로미스의 후속 처리 메서드 없이 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

```js
const fetch = require("node-fetch");

async function fetchTodo() {
  const url = "https://url";

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'hello', completed: false}
}
```
### 46.6.1 async 함수
`async` 함수는 언제나 프로미스를 반환한다.

```js
async function foo(n) {
  return n;
}
foo(1).then((v) => console.log(v)); // 1

const bar = async function (n) {
  return n;
};
bar(2).then((v) => console.log(v)); // 2

const baz = async (n) => n;
baz(3).then((v) => console.log(v));

const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(4).then((v) => console.log(v)); // 4

class MyClass {
  async bar(n) {
    return n;
  }
}
const myClass = new Myclass();
myClass.bar(5).then((v) => console.log(v)); // 5
```
### 46.6.2 await 키워드
- `await` 키워드는 반드시 프로미스 앞에서 사용한다.
- `awai`t 키워드는 프로미스가 `settled` 상태가 될 때까지 대기하다가, `settled` 상태가 되면 프로미스가 `resolve`한 처리 결과를 반환한다.  

즉, `await` 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 `settled` 상태가 되면 다시 재개한다.

- 비동기 처리의 처리 순서가 보장될 때는 `await` 키워드를 써서 순차적으로 처리한다.
반면, 비동기 처리가 서로 연관이 없는 경우 `Promise.all()` 을 활용한다

```js
async function foo() {
  const res = await Promise.all([
    new Promise((resolve) => setTimeOut(() => resolve(1), 3000)),
    new Promise((resolve) => setTimeOut(() => resolve(2), 2000)),
    new Promise((resolve) => setTimeOut(() => resolve(3), 1000)),
  ]);

  console.log(res); // [1, 2, 3];
}

foo(); // 약 3초 소요
```

### 46.6.3 에러 처리

- async/await에서 에러 처리는 비동기 처리를 위한 콜백 패턴과는 다르게 `try...catch` 문을 사용할 수 있다.

- 이 때, `catch`문은 HTTP 통신에서 발생한 네트워크 에러뿐 아니라, try 코드 블록 내의 모든 문에서 발생한 일반적인 에러까지 캐치할 수 있다.

```js
const fetch = require("node-fetch");

const foo = async () => {
  try {
    const wrongUrl = "https://wrong";

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.log(err);
  }
};
```

## 47. 에러처리

- 에러가 발생하지 않는 코드를 작성하는 것은 불가능하다. 따라서 에러는 언제나 발생할 수 있다.

- 에러에 대처하지 않으면 프로그램은 강제 종료된다. 에러처리를 하지 않고 HTTP 통신을 수행하다 실제로 오류가 나면 프로그램이 강제 종료 되고 다시 실행을 해야한다.

```js
console.log('start');

foo(); // 에러 발생 프로그램 강제 종료

// 에러에 의해 아래 코드는 실행 되지 않음
console.log('end');
```

- `try... catch` 문을 사용하여 에러에 적절하게 대응하면 프로그램이 강제 종료되지 않고 계속해서 코드를 실행할 수 있다.

```js
console.log('start');

try {
    foo(); // 에러 발생
} catch (error) {
    console.log(error); // 에러 발생시 대처
}

// 에러가 발생해도 아래 코드가 종료되지 않는다.
console.log('end');
```
> 예외적인 에러 상황에 대응하지 않으면 프로그램은 강제 종료된다. 하지만 예외적인 에러 상황은 너무 다양하기 때문에 아무런 조치를 하지 않으면 원인을 파악하기도 힘들고 대응하기도 힘들다.
>
> 에러를 발생하지 않는 코드를 작성하는 것이 제일 베스트지만 그것은 불가능하다. 따라서 우리는 코드에 언제 에러나 예외적인 상황이 발생할 수 있다는 것을 전제하고 이에 대응하는 코드를 작성하는 것이 개발자로써 중요하다.

### 47.2 try... catch... finally 문

- 기본적으로 에러 처리를 구현하는 방법에는 if 문이나 단축 평가 또는 옵셔널 체이닝 연산자를 통해 확인해서 처리하는 방법과 에러 처리 코드를 미리 등록하여 에러가 발생하면 에러 처리 코드로 점프 하는 방법이 있다.

- `try... catch... finally` 문도 역시 에러 처리 방법 중 하나다. 이 방법은 에러 처리(Error handling)이라고 한다.

- `try... catch... finally` 문은 3개의 코드 블록으로 구성되지만 `finally` 문은 필요한 경우에만 작성한다. `catch` 문도 생략 가능하지만 `catch`가 없는 `try`는 의미가 없으므로 생략하지 않는다.

```js
try {
    // 실행할 코드
} catch (error) {
    // 에러 발생시 핸들링 할 로직이 실행
    // try에서 발생한 Error 객체가 전달
} finally {
    // 무조건 실행되는 로직
}
```
1. `try... catch... finally` 문 실행 시 `try` 코드 블록이 실행된다. 이때 `try` 코드 블록에서 에러가 발생하면 발생한 에러는 `catch`에 error 변수에 전달되고 `catch` 코드 블록이 실행된다.

2. `try` 코드 블록에 에러가 발생하면 에러가 생성되고 `catch` 코드 블록에서만 유효하다.

3. `finally` 코드 블록은 에러 발생과 상관없이 반드시 한 번 실행된다.

4. `try... catch... finally` 문으로 에러를 처리하면 프로그램이 강제 종료되지 않는다.

### 47.3 Error 객체

`Error` 생성자 함수를 통해 에러 객체를 생성할 수 있다.

`Error` 생성자 함수에는 에러를 설명하는 메세지를 인수로 전달할 수 있다.

```js
const error = new Error('new Error!!');
```
- `Error` 생성자 함수가 생성한 에러 객체는 `message` 프로퍼티와 `stack` 프로퍼티를 갖는다.

- `Message` 프로퍼티 값은 인수로 전달한 에러 메세지고 `stack` 프로퍼티 값은 에러를 발생시킨 콜 스택의 호출 정보를 나타내는 문자열이며 디버깅을 목적으로 사용한다.

 ### 7가지의 에러 객체

 ---

자바스크립트는 `Error` 생성자 함수를 포함해 7가지의 에러 객체를 생성할 수 있다.

| 에러 객체 | 설명 |
| --- | ----- |
| Error | 일반적인 에러 객체 |
| SyntaxError | 문법에 맞지 않는 문을 해석할 때 발생하는 에러 |
| ReferenceError | 참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체 |
| TypeError | 데이터 타입이 유효하지 않을 때 발생하는 에러 객체 |
| RangeError | 숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체 |
| URLError | `encodeURL` 또는 `decodeURL` 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체 |
| EvalError | `eval` 함수에서 발생하는 에러 객체 |
|

### 47.4 throw 문

- `Error` 생성자 함수로 에러 객체를 생성한다고 에러가 발생하는 것은 아니다. 에러 생성과 발생은 의미가 다르다. 에러를 발생시킬려면 `try` 코드 블록에서 `throw` 문으로 에러를 던져야 한다.

```js
try {
    throw new Error('new Error!');
} catch (error) {
    console.error(error.message);
}
```

- `throw` 문은 어떤 값이라도 상관없지만 일반적으로 에러 객체를 지정한다. 에러를 던지면 `catch` 문의 에러 변수가 생성되고 던져진 에러 객체가 할당된다. 그리고 `catch` 코드 블록이 실행되기 시작한다.


### 47.5 에러의 전파

> **에러는 "호출자" 방향으로 전파된다.**

=> 즉, **콜 스택의 아래 방향** (실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향으로) 전파된다.

```js

foo bar baz
function nam() {
    throw new Error('nam에서 발생한 에러');
}

function namil() {
    nam();
}

function namilc() {
    namil();
}

try {
    namilc();
} catch (error) {
    console.error(error);
}
```

1. `namilc` 함수를 호출하면 `namil` 함수가 호출되고 거기서 또 `nam` 함수가 호출되고 `nam` 함수에서 에러를 던지고 있다.

2. 이때 `nam` 함수가 던진 에러는 호출자에게 전파되어 전역에서 캐치된다.

>전역 실행 컨텍스트 -> `namilc` 실행 컨텍스트 -> `namil` 실행 컨텍스트 -> `nam` 실행 컨텍스트

=> 현재 콜 스택에 실행 컨텍스트가 쌓인 모양이다.

- `nam`에서 발생한 에러를 호출자 방향으로 `namil` -> `namilc` -> 전역으로 전파되며 어느 실행 컨텍스트에서 에러를 적절히 캐치하여 대응한다면 프로그램이 강제 종료되지 않고 복구할 수 있다.

++ 만약 어느 실행 컨텍스트에서 `throw`된 에러를 캐치하지 않으면 프로그램은 강제 종료 된다!

#### | 호출자가 없는 경우

- **주의 할 점**은 비동기 함수인 `setTimeout`이나 프로미스 후속 처리 메서드의 콜백 함수는 호출자가 없다는 것이다.

- 이때 콜백 함수는 콜 스택에 있지않고 이벤트 루프의 태스크 큐나 마이크로태스크 큐에 저장되었다가 콜 스택이 비었을 때 푸시되어 실행된다.
  - 이때 콜 스택이 비었고 콜백 함수는 가장 하부에 존재하게 되므로 전파할 호출자가 존재하지 않게 되는 것이다.

>그래서 비동기 함수를 **콜백**으로 처리한다는 것은 에러 처리에 한계를 발생하는 것이다.
