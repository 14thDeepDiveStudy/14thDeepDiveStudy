# JS DeepDive

## 45. 프로미스

### 콜백 지옥(callback hell)

#### 콜백 지옥이란?

콜백 함수를 통해 비동기 처리 결과에 대한 후속 처리를 수행하는 비동기 함수가 처리 결과를 가지고 또다시 비동기 함수를 호출하는 콜백 함수가 중첩되는 현상이다.

> 즉, `비동기 처리`를 하는 `콜백함수`의 인수로 `콜백함수`의 인수로 `콜백함수`를 받는 콜백함수가 중첩되는 현상이다.

```jsx
setTimeout(function () {
  console.log("1초 후 실행");
  setTimeout(function () {
    console.log("2초 후 실행");
    setTimeout(function () {
      console.log("3초 후 실행");
    }, 1000);
  }, 1000);
}, 1000);

//1초 후 실행
//2초 후 실행
//3초 후 실행
```

#### 이런 콜백 지옥이 발생하는 이유는?

비동기 함수는 비동기 처리 결과를 외부로의 반환이나 상위 스코프의 변수에 할당할 수 없다. 그래서 함수의 처리 결과에 대한 후속 처리를 비동기 함수 안에서 수행해야 된다.
이때 비동기 함수의 처리결과에 대한 후속처리를 콜백 함수에 전달하는 것이 일반적이다.

---

### 프로미스(Promise)

#### 프로미스란?

콜백 지옥의 단점을 보완한 비동기 작업을 효율적으로 처리할 수 있도록 도와주는 내장 객체이다.
Promise 객체는 비동기 작업이 맞이할 미래의 완료 또는 실패와 그 결과 값을 나타낸다.

**무엇을 약속(promise)하는건가?**

> 비동기 연산이 종료된 이후에 결과 값과 실패 사유의 결과를 **제공**하겠다는 '프로미스(promise)'를 반환한다.

Promise 생성자 함수를 new 연산자와 함께 호출하면 Promise 객체를 호출한다,
이때 비동기 처리를 수행할 콜백 함수를 인수로 전달받는데, 이 함수는 resolve와 reject 함수를 인수로 전달받는다.

```jsx
const promise = new Promise((resolve, reject) => {
  if (/*비동기 처리 성공*/) {
    resolve('성공 결과')
  } else { // 비동기 처리 실패
    reject('실패 이유')
    }
 });

```

Promise는 비동기 처리의 진행 여부에 대한 `상태 정보`를 갖는다.

| Promise의 상태 정보 | 의미                                  | 상태 변경 조건                   |
| ------------------- | ------------------------------------- | -------------------------------- |
| pending             | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
| **fulfilled**       | 비동기 처리가 수행된 상태 - **성공**  | resolve 함수 호출                |
| **rejected**        | 비동기 처리가 수행된 상태 - **실패**  | reject 함수 호출                 |

인수로 전달받은 콜백 함수의 비동기 처리를 수행의 결과

- 비동기 처리 성공 -> `resolve` 함수를 호출해 프로미스를 fulfilled 상태로 변경
- 비동기 처리 실패 -> `reject` 함수를 호출해 프로미스르 rejected 상태로 변경

![](https://velog.velcdn.com/images/wjinss/post/b63c043d-8a21-4865-8b15-322c89b1130c/image.png)

pending이 아닌, fulfilled/rejected 여부 상관없이 비동기 처리가 수행된 상태를 `settled` 상태라고 한다.

Promise는 비동기 처리 결과도 상태로 갖는다.(위 그림의 result)

> 즉, Promise는 비동기 처리 상태와 처리 결과를 관리하는 객체다.

#### Promise의 후속 처리 메서드

Promise의 비동기 처리 상태가 변화하면 이에 따른 후속 처리를 해야된다.
이때 사용되는 메서드가 `then`, `catch`, `finally`이다.

- Promise의 비동기 처리 상태가 변화하면 후속 처리 메서드에 인수로 전달할 콜백 함수가 선택적으로 호출된다.
- 후속 처리 메서드의 콜백 함수에 Promise의 처리 결과가 인수로 전달된다.
- 모든 후속 처리 메서드는 Promise를 반환하며, 비동기로 동작한다.

**Promise.prototype.then**
then 메서드는 두개의 콜백 함수를 인수로 받는다

- `fulfilled`상태가 되면 호출되며, 프로미스의 비동기 처리 결과를 인수로 받는다 > **성공 처리 콜백 함수**
- `rejected`상태가 되면 호출되며, 프로미스의 비동기 처리 결과를 인수로 받는다 > **실패 처리 콜백 함수**

```jsx
//fulfilled
new Promise((resolve) => resolve("fulfilled")).then((value) =>
  console.log(value)
); // fulfilled

//rejected
new Promise((_, reject) => reject(new Error("rejected"))).then((err) =>
  console.error(err)
); //Error:rejected
```

- then 메서드는 언제나 Promise를 반환한다.
- then 메서드의 콜백 함수가 Promise가 아닌 값을 반환하면 그 값을 암묵적으로 resolve / reject하여 Promise를 생성해 반환한다.

**Promise.prototype.catch**
catch 메서드는 reject 상태에서만 호출되며, 한 개의 콜백 함수를 인수로 받는다.

```jsx
//rejected
new Promise((_, reject) => reject(new Error("rejected"))).catch((err) =>
  console.error(err)
); //Error:rejected
```

- catch 메서드는 언제나 Promise를 반환한다.

**Promise.prototype.finally**
finally 메서드는 성공/실패 여부와 상관없이 무조건 한 번 호출되며, 한 개의 콜백 함수를 인수로 받는다.

```jsx
new Promise(() => {}).finally(() => console.log("finally")); // finally
```

- finally 메서드는 언제나 Promise를 반환한다.

#### Promise의 에러 처리

비동기 처리에서 발생한 에러는 다음과 같은 방법으로 해결한다

- then의 두번째 콜백 함수 (비효율적)
- catch 메서드 (권장)

```jsx
promise("대충 URL")
  .then((res) => console.xxx(res))
  .catch((err) => console.error(err)); // TypeError: console.xxx is not a function
```

#### 프로미스 체이닝(Promise chaining)

then, catch, finally 메서드는 언제나 promise를 반환하므로 연속적으로 호출할 수 있다.
이를 프로미스 체이닝이라고 한다.

프로미스 체이닝은 비동기 처리 결과를 전달받아 후속 처리를 하므로 **콜백 지옥이 발생하지 않는다**.
다만 콜백 패턴은 동일하게 사용하므로 가독성이 좋지 않다.

#### Promise의 정적 메서드

Promise는 주로 생성자 함수로 사용되지만, 함수 === 객체이므로 메서드를 가질 수 있다.
Promise는 5가지의 정적 메서드를 제공한다.

**Promise.resolve/Promise.reject**

- resolve 메서드는 인수로 받은 값을 resolve하는 Promise를 생성한다.
- reject 메서드는 인수로 받은 값을 reject Promise를 생성한다.

```jsx
//resolve
const resPromise = Promise.resolve([1, 2, 3]);
resPromise.then(console.log); // [1,2,3]

//reject
const rejPromise = Promise.reject(new Error("에러났어유"));
rejPromise.catch(console.log); // Error : 에러났어유
```

**Promise.all**
Promise.all 메서드는 여러 개의 비동기 처리를 모두 병렬로 처리할 때 사용한다.
병렬로 작업하기 때문에 앞선 비동기 처리 결과를 다음 비동기 처리가 사용하지 않는다.

- 모든 Promise가 fulfilled 상태가 되면 종료되며, 이때 처리 순서는 보장된다
- Promise가 하나라도 rejected 상태가 되면 즉시 종료한다.(에러를 reject하는 Promise 반환)
- Promise를 요소로 갖는 배열 등의 이터러블을 인수로 받는다.

```jsx
const data1 = () => {
  return new Promise((resolve) => setTimeout(() => resolve(1), 3000));
};

const data2 = () => {
  return new Promise((resolve) => setTimeout(() => resolve(2), 2000));
};

const data3 = () => {
  return new Promise((resolve) => setTimeout(() => resolve(3), 1000));
};

Promise.all([data1(), data2(), data3()])
  .then(console.log) // [1, 2, 3]
  .catch(console.error);
```

**Promise.race**

- Promise를 요소로 갖는 배열 등의 이터러블을 인수로 받는다.
- 가장 먼저 fulfilled 상태가 된 Promise의 처리 결과를 resolve하는 Promise를 반환한다.
- Promise가 하나라도 rejected 상태가 되면 즉시 종료한다.(에러를 reject하는 Promise 반환)

```jsx
Promise.race([
  new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
  new Promise((resolve) => setTimeout(() => resolve(2), 2000)),
  new Promise((resolve) => setTimeout(() => resolve(3), 1000)),
])
  .then(console.log) //3
  .catch(console.error);
```

**Promise.allSettled**

- Promise를 요소로 갖는 배열 등의 이터러블을 인수로 받는다.
- 받은 Promise가 모두 settled 상태가 되면 처리 결과를 배열로 반환한다.

```jsx
Promise.allSettled([
  new Promise((resolve) => setTimeout(() => resolve(1), 2000)),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error("에러에유")), 1000)
  ),
]).then(console.log);
/*
[
{status: 'fulfilled', value: 1}
{status: 'rejected', reason: Error: 에러에유 at <anonymous>:4:29}
]
*/
```

allSettled 메서드가 반환한 배열엔 프로미스들의 처리 결과가 모두 담겨있다.

- fulfilled 상태인 경우 status 프로퍼티(처리 상태), value 프로퍼티(처리 결과)를 갖는다.
- rejected 상태인 경우 status 프로퍼티(처리 상태), reason 프로퍼티(에러)를 갖는다.

#### 마이크로태스크 큐

> 프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 마이크로 태스크 큐에 저장된다.

- 태스크 큐 : 비동기 함수의 콜백 함수, 이벤트 핸들러가 일시 저장되는 곳

마이크로 태스크 큐는 태스크 큐보다 우선순위가 **높다**.
이벤트 루프는 콜 스택이 비면 마이크로태스크 큐에 대기중인 함수를 먼저 가져와 실행한다.

#### fetch

> HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 WEB API

프로미스를 지원하기 때문에 비동기 처리를 위한 콜백 패턴의 단점에서 자유롭다.

```jsx
const promise = fetch(url [,options])
```

반환값: `Response 객체`를 래핑한 `Promise 객체`를 반환

- Response 객체는 HTTP 응답 정보를 담고 있으며, 이를 래핑한 Promise가 반환됨.
- 네트워크 요청 자체가 실패하면 Promise는 reject 상태가 된다.

첫 번째 인수 (url): 요청을 전송할 리소스의 주소
두 번째 인수 (options): HTTP 요청 관련 옵션 객체

- method: HTTP 메서드 (GET, POST, PUT, DELETE 등)
- headers: 요청 헤더 설정
- body: 요청 본문 (POST나 PUT 등에서 사용)

---

## 46. 제너레이터와 async/await

### 제너레이터

#### 제너레이터란?

> 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수

제너레이터와 일반 함수의 차이

- **제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.**
- **제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.**
- **제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.**

#### 제너레이터 함수의 정의

- `function*`로 선언
- 하나 이상의 `yield`표현식을 포함
- 그 외 일반 함수와 동일

```jsx
// 제너레이터 함수선언문
function* genFn() {
  yield 1;
}

// 제너레이터 함수 표현식
const genFn2 = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  *genMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class genClass {
  *genMethod() {
    yield 1;
  }
}
```

- `*`의 위치는 `function` 키워드 뒤에 붙이는 것이 권장된다.
- 화살표 함수로는 정의할 수 없다.
- new 연산자와 함께 생성자 함수로 호출할 수 없다.

#### 제너레이터 객체

> 제너레이터 함수를 호출하면 제너레이터 객체를 생성해 반환한다.
> 이때 제너레이터 객체는 `이터러블`이면서 `이터레이터`이다.

> **이터러블(Iterable)**

- 반복 가능한 객체(Array, String, Map, Set)
- Symbol.iterator 메서드를 가지고 있어서, 호출하면 이터레이터를 반환하는 객체

> **이터레이터(Iterator)**

- next() 메서드를 가지고 있고, 호출할 때마다 { value, done } 객체를 반환하는 객체
- 이터러블의 Symbol.iterator() 호출 결과물이 바로 이터레이터

제너레이터 객체는 이터레이터에는 없는 `return`, `throw` 메서드를 갖는다.

- next 메서드

  - 제너레이터 함수의 yiled 표현식까지 코드 블록을 실행하고 **yield된 값을 value 값**으로, **false를 done 값으로 반환**

- return 메서드

  - **인수로 전달받은 값을 value 값으로, true를 done 값으로 반환**

- throw 메서드
  - 인수로 전달받은 에러를 발생 및 undefined를 value 값으로, true를 done 값으로 반환

```jsx
function* genFn() {
  try {
    yield 1;
    yield 2;
  } catch (e) {
    console.error(`e`);
  }
}

const generator = genFn();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.next()); // {value: 2, done: false}
console.log(generator.next()); // {value: undefined, done: true}
console.log(generator.return("끝이에여!")); // {value: '끝이에여!', done: true}
console.log(generator.throw("에러에여!!")); // {value: undefined, done: true}
```

#### 제너레이터의 일시 중지와 재개

제너레이터 객체의 next 메서드를 호출하면 yiled 표현식까지 실행되고 일시 중지된다.
이때 함수의 제어권이 호출자(caller)로 양도된다.
이때 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다.

- value 프로퍼티에는 yield의 값이 할당
- done 프로퍼티는 함수가 끝까지 실행됐는지 나타내는 논리형 값이 할당
- next 메서드에 전달된 인수는 yield 표현식을 할당받는 변수에 할당

```jsx
function* genFn() {
  const x = yield 1;
  const y = yield x + 10;
  return x + y;
}

const generator = genFn(0);

console.log(generator.next()); // {value: 1, done: false}
// yield 1만 실행
console.log(generator.next(10)); // {value: 20, done: false}
// x에 10이 할당되어 yield (10 + 10) = 20
console.log(generator.next(20)); // {value: 30, done: true}
// y에 20이 할당되어 10+20 = 30 & 함수 끝남
```

그 외의 제너레이터의 활용으로 promise를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있다.
즉, then, catch, finally 메서드 없이 비동기 처리 결과를 반환하도록 구현할 수 있다.
하지만 이는 코드가 장확해지고 가독성이 나빠진다.
ES8에서 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 구현할 수 있는 **async/await**가 도입되었다.

### async/await

async/await는 promise를 기반으로 동작하며, 비동기 처리 결과를 동기 처리처럼 promise를 사용할 수 있다.

> then, catch, finally 메서드 없이 **동기 처리** 처럼 Promise가 처리 결과를 반환하게 한다.

#### async 함수

async 키워드를 사용해 정의하며, 언제나 Promise를 반환한다.

- 명시적으로 Promise를 반환하지 않더라도, async 함수는 암묵적으로 반환값을 resolve하는 Promise를 반환

```jsx
// async 함수 선언문
async function fn(v) {
  return v;
}
fn("함수 선언문").then((v) => console.log(v));

// async 함수 표현식
const fn = async function (v) {
  return v;
};
fn("함수 표현식").then((v) => console.log(v));

// async 화살표 함수
const fn = async (v) => {
  return v;
};
fn("화살표 함수").then((v) => console.log(v));

// async 메서드
const obj = {
  async fn(n) {
    return n;
  },
};
obj.fn("메서드").then((v) => console.log(v));

// async 클래스 메서드
class AsyncClass {
  async fn(n) {
    return n;
  }
}
const asyncClass = new AsyncClass();
asyncClass.fn("클래스 메서드").then((v) => console.log(v));
```

#### await 키워드

- async 함수 내부에서만 사용해야되며, Promise 앞에서 사용해야한다.
- 대기하다가 Promise가 settled 상태가 되면 Promise가 resolve한 처리 결과를 반환한다.

```jsx
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function sayHello() {
  console.log("인사를 시작합니다...");

  await delay(3000);

  console.log("Hello, world!");
}

sayHello();
// 인사를 시작합니다...
// (3초 뒤) delay가 settled상태가 됨(resolve)
// Hello, world!
```

- 개별적으로 수행되는 비동기 처리에선 모든 promise에 await를 사용하는 것 주의
  - await promise.all을 사용
- 비동기 처리의 결과를 가지고 다음 비동기 처리를 수행해야 될 때는 예외 (처리 순서가 보장되어야 할 때)
  - 모든 promise에 await 사용

---

## 47. 에러 처리

> 에러가 발생하지 않는 코드? 있을 수 없다.

발생한 에러를 대처하지 않고 방치하면 프로그램은 강제 종료된다.
우리가 작성한 코드는 언제나 에러가 발생할 것이라고 생각하고 이에 대응하는 코드를 작성해야 된다.

### try...catch...finally문

```jsx
try {
  // 실행할 코드(에러가 발생할 가능성이 있는 코드)
} catch (err) {
  // try 코드 블록에서 에러가 발생하면 이 코드 블록의 코드가 실행
  // catch()의 인수로는 try 코드 블록에서 발생한 Error 객체가 전달
} finally {
  // 에러 발생과 상관없이 반드시 한 번 실행되는 코드(생략가능)
}
```

**예시**

```jsx
const wrongFn = async () => {
  const res = await fetch(`http://wrong.url`);
  const data = await res.json();
  return data;
};

wrongFn().then(console.log).catch(console.error); // TypeError: Failed to fetch.
```

### Error 객체

Error 생성자 함수는 에러 객체를 생성한다. 해당 함수에는 에러를 상세히 설명하는 에러 메시지를 인수로 전달할 수 있다.

```jsx
const error = new Error("에러 메시지");
```

Error 생성자 함수가 생성한 에러 객체는 두개의 프로퍼티를 갖는다.

- message 프로퍼티의 값 : Error 생성자 함수에 인수로 전달한 에러 메시지
- stack 프로퍼티의 값 : 에러를 발생시킨 콜 스택의 호출 정보를 나타내는 문자열(디버깅 목적)

JS는 Error 생성자 함수를 포함해 7가지의 에러 객체를 생성할 수 있다.

| 생성자 함수    | 인스턴스                                                                        |
| -------------- | ------------------------------------------------------------------------------- |
| Error          | 일반적 에러 객체                                                                |
| SyntaxError    | JS 문법에 맞지 않는 문을 해석할때 발생하는 에러 객체(문법 오류)                 |
| ReferenceError | 참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체(참조 오류)               |
| TypeError      | 피연산자 or 인수의 데이터 타입이 유효하지 않을 때 발생하는 에러 객체(타입 오류) |
| RangeError     | 숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체                             |
| URIError       | encodeURI or decodeURI 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체    |
| EvalError      | eval 함수에서 발생하는 에러 객체                                                |

### throw 문

> Error 생성자 함수로 에러 객체 생성 !== 에러 발생

에러 객체 생성과 에러 발생은 의미가 다르다.
에러를 발생시키려면 try 코드 블록에서 throw문으로 에러 객체를 던져야 한다.

```jsx
throw 표현식;
```

throw 표현식은 일반적으로 에러 객체를 지정한다.
에러를 던지면 catch문의 에러 변수가 생성되고 던져진 에러 객체가 할당되며 catch문이 실행된다.

> 즉, 에러를 throw해서 에러를 catch한다

```jsx
try {
  throw new Error("에러 던집니다~!");
} catch (err) {
  console.log(err);
}

// Error: 에러 던집니다~!
```

### 에러의 전파

에러는 호출자(caller)방향으로 전파된다.
콜 스택의 아래 방향으로 전파된다.

```jsx
const foo = () => {
  throw new Error("foo에서 발생한 에러");
};

const bar = () => {
  foo();
};

const baz = () => {
  bar();
};

try {
  baz();
} catch (err) {
  console.error(err);
}
/*
  Error: foo에서 발생한 에러
    at foo (<anonymous>:2:9)
    at bar (<anonymous>:6:3)
    at baz (<anonymous>:10:3)
    at <anonymous>:14:3*/
```

try 코드 블록에서 baz() 호출 => bar() 호출 => foo() 호출 > foo()에서 에러 throw
이때 throw한 에러는 호출자에게 전파되어 전역에서 캐치된다.

![](https://velog.velcdn.com/images/wjinss/post/5b118d34-2a21-4a76-aa52-34d42f33e016/image.png)

throw된 에러를 캐치하지 않으면 호출자 방향으로 전파되는데, 이때 에러를 어디에서도 캐치하지 않으면 프로그램은 강제 종료된다.

이때 setTimeout이나 promise 후속 처리 메서드의 콜백 함수는 호출자가 없다. 두 함수는 태스크 큐나 마이크로태스크 큐에 일시 저장되었다가 콜 스택이 비면 이벤트 루프에 의해서 콜 스택으로 푸시되어 실행된다.

이때 콜 스택에 푸시된 콜백 함수의 실행 컨텍스트는 콜 스택의 가장 하부에 존재하게 되기 때문에, 에러를 전파할 호출자가 존재하지 않는다.

setTimeout나 Promise의 콜백 함수의 예외를 잡으려면, 반드시 콜백 함수 내부에 try...catch를 구현해야 한다.
