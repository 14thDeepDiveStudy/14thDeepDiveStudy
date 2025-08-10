## 1️⃣ Promise, 저는 이렇게 이해했어요!

### 1. 콜백 헬과 Promise의 등장!

#### 1-1. 콜백 헬이란 무엇일까?

**콜백 헬(callback hell)**은 비동기 함수를 처리하기 위해 콜백 함수를 계속 중첩해서 작성하다 보니
코드가 피라미드처럼 오른쪽으로 점점 밀려 **가독성이 떨어지고**, **에러 처리와 유지보수가 어려워지는 문제**를 말한다.

예를 들면,
잡지에서 특집 기사를 만들려고 여러 기자가 한 명의 유명인을 차례로 인터뷰한다고 생각해보자.
기자 A가 질문 1을 마치면 기자 B에게 연결하고,
기자 B가 끝나면 기자 C에게 연결하고,
기자 C가 끝나면 기자 D에게 연결하고...

중간에 한 기자라도 인터뷰 시간이 오래 걸리면 뒤 기자들은 계속 대기해야 하고,
질문과 답변이 엮이면서 전체 흐름이 복잡해진다.
이렇게 단계가 깊게 이어져서 한 줄로 종속되는 구조가 콜백 헬이다.

이런 구조는 코드가 길어질수록 가독성이 떨어지고,
중간에 이러가 발생했을 때 처리 로직이 단계마다 흩어져 관리하기가 어렵다.
그래서 이를 해결하기 위해 등장한 것이 바로 **Promise**다. 🤩

#### 1-2. Promise의 등장! ✨

Promise는 "결과를 나중에 알려주겠다"는 약속을 객체로 표현한 것이다.
비동기 작업이 끝나면 성공(fulfilled) 또는 실패(rejected) 상태로 바뀌고, 그에 맞는 처리 로직이 실행된다.

예를 들면,
택배를 주문하면 바로 물건을 주지 않지만, 운송장(약속)을 준다.
운송장(약속)을 받는 순간이 Promise 객체를 만든 시점이다.
이때는 아직 물건(결과)이 도착하지 않았으니 상태가 pending(대기) 상태다.
시간이 지나서 택배가 정상적으로 도착하면, Promise가 fulfilled(이행) 상태로 변한다.
이 순간, "문 앞에 놔주세요!"라고 미리 적어둔 요청이 실행되고, 이게 `.then()`에 등록한 콜백이 실행되는 것이다.
반대로, 배송 도중 문제가 생기면, Promise가 rejected(거부) 상태로 변한다.
이때는 "문제가 있으면 여기로 전화해주세요."라고 적어둔 요청이 실행되고, 이게 `.catch()`에 등록한 콜백이 실행되는 것이다.

#### 1-3. Promise와 콜백 헬의 관계

과거에는 비동기 작업을 처리할 때 콜백 함수만 사용했다.
예를 들어, 다음과 같이 작업이 여러 개 연속으로 필요할 때는 콜백을 중첩하여 작성했다.

```js
doTask1(result1 => {
  doTask2(result1, result2 => {
    doTask3(result2, result3 => {
      doTask4(result3, result4 => {
        console.log('모든 작업 끝..!');
      });
    });
  });
});
```

이 구조는 피사의 사탑처럼 오른쪽으로 기울어져서
가독성이 떨어지고, 중간에 에러 처리도 어렵고, 재사용이 거의 불가능..ㅎㅏㄷㅏ..

이게 바로 **콜백 헬**이다.

Promise는 이런 문제를 해결하기 위해 등장했고,
콜백을 중첩하는 대신 `.then()` / `.catch()` 같은 후속 처리 메서드로 연결한다.
이렇게 하면 수평적이고 깔끔한 코드를 만들 수 있고,
에러 처리 로직도 한 군데에서 관리할 수 있다.

이제 Promise의 이런 장점을 가능하게 해주는 핵심 기능이 바로 **후속 처리 메서드**다.

### 2. Promise의 후속 처리 메서드

> Promise에서 후속 처리 메서드란,
> 비동기 작업이 끝난 뒤(성공 또는 실패) 결과를 처리하기 위해 미리 등록해 두는 메서드를 말한다.
> 대표적으로 `.then()`, `.catch()`, `.finally()`가 있다.

#### 2-1. `.then()` - 성공했을 때의 약속 👏

`.then()`은 Promise가 fulfilled 상태가 되었을 때 실행할 콜백 함수를 등록한다.

```js
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => response.json()) // 성공 시 실행
  .then(data => console.log('받은 데이터: ', data));
```

#### 2-2. `catch()` - 실패했을 때의 약속 😔

`.catch()`는 Promise가 rejected 상태가 되었을 때 실행할 콜백 함수를 등록한다.

```js
fetch('https://jsonplaceholder.typicode.com/404') // 존재하지 않는 주소
  .then(response => response.json())
  .catch(error => console.error('에러 발생: ', error));
```

#### 2-3. `finally()` - 성공/실패 상관없이 마지막에 실행

`.finally()`는 fulfilled나 rejected 상태와 관계없이 무조건 실행된다.
주로 로딩 화면 종료, 리소스 해제 등에 사용된다.

```js
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => response.json())
  .catch(error => console.error('에러 발생: ', error))
  .finally(() => console.log('작업 완료!'));
```

#### 2-4. 왜 후속 처리 메서드가 중요할까?

콜백 헬에서는 작업 순서를 맞추기 위해 콜백 함수를 안쪽으로 계속 중첩해야 했다.
하지만 Promise의 후속 처리 메서드를 사용하면 작업을 수평으로 나열할 수 있다.

```js
// 콜백 헬
doTask1(result1 => {
  doTask2(result1, result2 => {
    doTask3(result2, result3 => {
      console.log(result3);
    });
  });
});

// Promise
doTask1()
  .then(result1 => doTask2(result1))
  .then(result2 => doTask3(result2))
  .then(result3 => console.log(result3))
  .catch(error => console.error(error));
```

이렇게 하면 코드가 보기 깔끔하고, 에러 처리도 `.catch()` 하나로 한 번에 가능하다.
결국 *Promise의 후속 처리 메서드는 콜백 헬을 해결하는 핵심 장치*라고 할 수 있다.

### 3. Promise의 생성

#### 3-1. Promise 객체 만들기 🏭

Promise는 `new Promise()` 생성자로 만든다.
생성자 함수의 인자로 **executor(실행자) 함수**를 넣는데, 이 함수는 두 개의 매개변수 `resolve`와 `reject`를 받는다.

```js
const promise = new Promise((resolve, reject) => {
  // 비동기 작업 수행
  const success = true; // 성공/실패 여부

  if (success) {
    resolve('result');
  } else {
    reject('failure reason');
  }
});
```

· resolve(value) → 작업 성공 시 호출
· reject(reason) → 작업 실패 시 호출

resolve와 reject를 일상 생활에 비유해보자면,
resolve는 "택배 잘 도착했습니다!"를 알리는 신호, reject는 "배송 중 문제가 생겼습니다..."를 알리는 신호로 생각해볼 수 있다.

### 4. Promise의 에러 처리

#### 4-1. `.catch()`로 에러 처리

Promise에서 에러는 `.catch()` 메서드를 통해 처리할 수 있다.
이 `.catch()`는 비동기 작업에서 발생한 모든 에러를 한 번에 처리할 수 있는 통로 역할을 한다.

#### 기본 에러 처리 방법

`.catch()`를 사용하면 체인 전체에서 발생한 에러를 한 곳에서 처리할 수 있다.

```js
new Promise((resolve, reject) => {
  reject('문제 발생!');
})
  .then(data => console.log(data)) // 실행 안 됨
  .catch(error => console.error('에러:', error)); // 여기서 처리
```

#### `try...catch`처럼 동작

동기 코드에서 `try...catch`가 예외를 잡는 것처럼,
Promise 체인에서도 중간에 에러가 발생하면 나머지 `.then()`을 건너뛰고 `.catch()`로 바로 이동한다.

```js
Promise.resolve()
  .then(() => {
    throw new Error('중간에 에러 발생');
  })
  .then(() => console.log('여기는 실행 안 됨'))
  .catch(error => console.error(error.message));
// 출력: '중간에 에러 발생'
```

### 5. Promise Chaining

> Promise의 `.then()`은 새로운 Promise 객체를 반환하기 때문에,
> 여러 개의 `.then()`을 이어 붙여 순차적으로 작업을 처리할 수 있다.
> 이를 Promise Chaining이라고 한다.

예를 들면,
레고 블록을 위에 하나씩 쌓듯,
앞에서 만든 결과를 다음 `.then()`으로 넘겨주며 차례차례 완성해가는 방식이다.

```js
fetchUser() // 1. 사용자 정보 요청
  .then(user => fetchPosts(user.id)) // 2. 해당 사용자의 게시글 요청
  .then(posts => fetchComments(posts[0].id)) // 3. 첫 게시글의 댓글 요청
  .then(comments => console.log('댓글 목록: ', comments))
  .catch(error => console.error(error));
```

이렇게 하면 비동기 작업이 순서대로 깔끔하게 이어진다! 🙂

### 6. Promise의 정적 메서드

> Promise에는 여러 정적 메서드가 있다.
> 이 메서드들은 Promise 인스턴스를 만들지 않고도, 다양한 비동기 작업 제어를 가능하게 해준다.

#### 6-1. `Promise.all()`

모든 Promise가 성공해야만 다음 `.then()`이 실행된다.
하나라도 실패하면 `.catch()`로 간다.

```js
Promise.all([taskA(), taskB(), taskC()])
  .then(results => console.log('모두 완료: ', results))
  .catch(error => console.error('실패: ', error));
```

#### 6-2. `Promise.race()`

가장 먼저 완료(fulfilled)된 Promise의 결과를 반환한다.

```js
Promise.race([fastTask(), slowTask()]).then(result =>
  console.log('가장 빠른 작업: ', result)
);
```

메서드 이름부터 race인 것처럼 달리기 시합을 떠올리면 좋은데,
누가 먼저 결승선을 통과하든, 1등이 곧 결과다.

#### 6-3. `Promise.allSettled()`

모든 Promise가 성공/실패 상관없이 완료될 때까지 기다리고,
각 결과를 객체로 반환한다.

```js
Promise.allSettled([taskA(), taskB()]).then(results => console.log(results));
```

### 6. 마이크로태스크 큐 (Microtask Queue)

> JavaScript의 이벤트 루프에서 마이크로태스크 큐는 태스크 큐보다 **우선순위가 높은 작업 목록**을 담는 곳이다.
> Promise의 .then()이나 .catch() 같은 후속 처리 콜백이 여기에 들어간다.

예를 들면,
카페에서 손님이 음료를 주문하고 기다리고 있다. (태스크 큐 작업)
그런데 갑자기 커피 머신에서 원두가 떨어졌다는 시스템 알람이 떴다. (마이크로태스크 큐 작업)
바리스타는 새로운 음료 제조를 시작하기 전에, 이 알람을 먼저 처리한다.
즉, **중간에 끼어드는 우선순위 높은 알림을 처리**하는 게 **마이크로태스크 큐**다.

```js
setTimeout(() => console.log('태스크 큐'), 0);

Promise.resolve().then(() => console.log('마이크로태스크 큐'));

// 출력 순서:
// 마이크로태스크 큐
// 태스크 큐
```

### 7. `fetch`

> `fetch()`는 네트워크 요청을 보내고 Promise를 반환하는 함수다.
> XMLHttpRequest보다 간결하며, 비동기 코드를 작성하기 쉽다는 장점이 있다.

예를 들면,
편지를 보낼 때 예전에는 직접 우체국(XMR)에 가서 접수하고 결과를 계속 확인해야 했다면,
fetch는 앱으로 편지를 보내고 자동으로 알림(Promise)을 받는 것과 같다.

```js
fetch('https://api.example.com/data')
  .then(response => {
    if (!response.ok) throw new Error('네트워크 응답 실패');
    return response.json();
  })
  .then(data => console.log('데이터:', data))
  .catch(error => console.error('에러:', error));
```

#### 7-1. 에러 처리 주의! ⚠️

fetch()는 네트워크 오류가 발생했을 때만 `catch()`로 한다.
HTTP 상태 코드가 404나 500이어도 성공으로 간주하고 `.then()`이 실행된다.
따라서 `.then()` 내부에서 `response.ok`를 직접 확인해야 한다!

```js
fetch('https://example.com/data')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP 오류! 상태: ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error('에러:', err));
```

#### 7-2. 요청 방식

#### 7-2-1. GET 요청 - 데이터 조회

· 서버에서 **데이터를 읽어오는 용도**
· URL에 쿼리 파라미터를 붙여서 요청 가능
· 기본 메서드라 method 옵션을 생략 가능

```js
request
  .get('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todos => console.log(todos))
  .catch(err => console.error(err));
// { userId: 1, id: 1, title: "delectus aut autem", completed: false }
```

#### 7-2-2. POST 요청 - 데이터 생성

· 서버에 **새로운 데이터 생성 요청**
· body에 JSON 형식 데이터 포함
· headers에 Content-Type 지정 필수

```js
request.post('https://jsonplaceholder.typicode.com.todos', {
  userId: 1,
  title: 'JavaScript,
  completed: false
}).then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todos => console.log(todos))
  .catch(err => console.error(err));
// { userId: 1, title: "JavaScript", completed: false. id: 201 }
```

#### 7-2-3. PATCH 요청 - 데이터 일부 수정

· 기존 데이터 중 일부만 업데이트
· PUT은 전체 덮어쓰기, PATCH는 일부만 변경

```js
request
  .patch('https://jsonplaceholder.typicode.com/todos/1', {
    completed: true,
  })
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todos => console.log(todos))
  .catch(err => console.error(err));
// { userId: 1, id: 1, title: "delectus aut autem", completed: true }
```

#### 7-2-4. DELETE 요청 - 데이터 삭제

· 서버에 데이터 삭제 요청

```js
request
  .delete('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todos => console.log(todos))
  .catch(err => console.error(err));
// {}
```

---

## 2️⃣ 제너레이터와 async/await, 저는 이렇게 이해했어요!

시작하기 전에,
_제너레이터는 왜 필요할까?_ 🤔

JavaScript의 일반 함수는 **한 번 호출하면 중간에 멈출 수 없이 끝까지 실행**된다.
하지만 복잡한 로직, 무한 시퀀스, 대규모 데이터 처리, 또는 비동기 제어 같은 상황에서는
"여기서 잠깐 멈췄다가, 조건이 되면 다시 실행"하는 방식이 필요하다.
이럴 때 등장한 것이 **제너레이터 함수**다.

### 1. 제너레이터란 무엇일까?

> 위에서 말했듯,
> 제너레이터(Generator)는 함수 실행을 중간에 멈췄다가 다시 이어서 실행할 수 있는 함수이다.
> 보통의 함수는 한 번 실행하면 끝까지 달리지만, 제너레이터는 중간에 "잠깐 멈춤! ✋"을 할 수 있다.

예를 들면,
일반 함수는 한 번 실행하면 처음부터 끝까지 쭉 재생되는 자동 재생 영상으로 비유할 수 있다.
제너레이터 함수는 내가 원하는 순간에 멈추고 다시 재생이 가능한 멈춤과 재생 버튼이 있는 영상으로 비유할 수 있다.

### 2. 제너레이터 함수의 정의

제너레이터 함수는 `function*` 키워드로 정의하며,
중간에 멈출 지점에서 `yield` 키워드를 사용한다.

```js
function* myGenerator() {
  yield 1; // 첫 번째 멈춤 지점
  yield 2; // 두 번째 멈춤 지점
  return 3; // 종료
}
```

### 3. 제너레이터 객체

제너레이터 함수를 호출하면 **즉시 실행되지 않고**, **제너레이터 객체가 반환**된다.
이 객체는 next() 메서드를 통해 한 단계씩 실행할 수 있다.

```js
const generator = myGenerator();

console.log(generator.next()); // { value: 1, done: false }
console.log(generator.next()); // { value: 2, done: false }
console.log(generator.next()); // { value: 3, done: true }
```

· `value` → `yield`나 `return` 뒤의 값
· `done` → 함수가 끝났는지 여부

### 4. 제너레이터의 일시 중지와 재개

yield 키워드에서 함수 실행이 **일시 중지**되고,
next()를 호출하면 **중단된 위치부터 다시 실행**된다.

예를 들면,
게임을 하다가 세이브 포인트에서 저장하고.
다음에 그 세이브 지점부터 이어서 플레이하는 것과 같다.

### 5. 제너레이터의 활용

#### 5-1. 이터러블 구현

제너레이터는 `Symbol.iterator`를 자동으로 구현하기 때문에,
`for...of` 문, 스프레드 문법 등에서 바로 사용할 수 있다.

```js
function* counter() {
  yield 1;
  yield 2;
  yield 3;
}

for (const num of counter()) {
  console.log(num); // 1, 2, 3
}
```

#### 5-2. 비동기 처리

제너레이터를 이용하면 비동기 코드를 동기 코드처럼 작성할 수 있다.
다만, 직접 제너레이터로 비동기를 다루는 경우는 많지 않고,
이 개념이 발전해서 나온 게 바로 **async/await**이다.

### 6. async/await

#### 6-1. async 함수

async 키워드를 붙이면 항상 **Promise를 반환하는** 함수를 만들 수 있다.
함수 내부에서 값을 반환하면, 자동으로 **Promise.resolve()**로 감싸진다.

```js
async function hello() {
  return '안뇽';
}

hello().then(console.log); // 안뇽
```

#### 6-2. await 키워드

await은 **Promise가 처리될 때까지 기다렸다가**, 결과를 반환한다.
오직 async 함수 내부에서만 사용할 수 있다.

```js
async function fetchData() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/1');
  const data = await res.json();
  console.log(data);
}
```

#### 6-3. 에러 처리

await에서 에러가 발생하면, try...catch로 처리할 수 있다.

```js
async function fetchData() {
  try {
    const res = await fetch('https://wrong.url');
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error('에러 발생: ', err);
  }
}
```

_그래서,
제너레이터란?_

제너레이터는 함수 실행을 **중단/재개**할 수 있는 특별한 함수!
`yield`로 멈춤 지점을 지정하고, `next()`로 재개!
**async/await**은 제너레이터 개념을 발전시켜, **비동기 코드를 더 간결하게 작성**할 수 있게 만든 문법임!

---

## 3️⃣ 에러 처리, 저는 이렇게 이해했어요!

### 1. 에러 처리의 필요성

코드가 실행되는 동안 예상치 못한 문제가 발생할 수 있다.

예를 들어,
서버에서 데이터를 가져올 때 네트워크 오류가 발생하거나
사용자가 입력해야 할 값을 비워서 제출하거나
존재하지 않는 변수를 참조하거나

이런 상황을 아무 대비 없이 두면
프로그램이 갑자기 멈추거나 사용자에게 이상한 동작을 보여줄 수 있다.
그래서 JavaScript에서는 에러가 발생했을 때,
프로그램이 완전히 죽지 않도록 에러를 잡아 처리하는 장치가 필요하다.

일상 생활에 비유를 해보자면,
자동차 주행 중에 타이어가 펑크가 났다.

이때,
스페어 타이어로 교체하거나(에러 처리)
최소한 안전한 곳까지 이동(프로그램 종료 방지)하는 것과 같다.

### 2. `try ... catch ... finally` 문

JavaScript에서 **동기 코드의 에러를 처리**하는 기본 문법이다.

```js
try {
  // 에러가 발생할 수 있는 코드
  JSON.parse('잘못된 JSON');
} catch (error) {
  console.error('JSON 파싱 실패: ', error.message);
} finally {
  // 에러 발생 여부와 상관없이 항상 실행됨
  console.log('파싱 시도 끝');
}
```

· try : 실행을 시도할 코드
· catch : 에러가 발생했을 때 실행할 코드
· finally : 성공하든 실패하든 마지막에 실행할 코드 (예: 리소스 해제, 연결 종료 등)

### 3. Error 객체

JavaScript의 모든 에러는 Error 객체를 기반으로 한다.
이 객체는 **에러의 정보**를 담고 있다.

```js
const err = new Error('에러 발생!');

console.log(err.name); // Error
console.log(err.message); // 에러 발생!
console.log(err.stack); // 에러가 발생한 위치와 호출 스택
```

대표적인 에러 객체의 종류
· `Error` : 일반적 에러 객체
· `SyntaxError` : JavaScript 문법에 맞지 않는 문을 해숙할 때 발생하는 에러 객체
· `ReferenceError` : 참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체
· `TypeError` : 피연산자 또는 인수의 데이터 타입이 유효하지 않을 때 발생하는 에러 객체
· `RangeError` : 숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체
· `URIError` : encodeURI 또는 decodeURI 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체
· `EvalError` : eval 함수에서 발생하는 에러 객체

### throw 문

throw를 사용하면 의도적으로 에러를 발생시킬 수 있다.
"이건 여기서 멈춰야 해!"라고 명령하는 것과 같다.

```js
function divide(a, b) {
  if (b === 0) {
    throw new Error('0으로 나눌 수 없음!');
  }
  return a / b;
}

try {
  console.log(divide(4, 0));
} catch (e) {
  console.error('에러: ', e.message);
}
```

### 에러의 전파

에러가 발생했을 때,
해당 스코프에서 잡히지 않으면 상위 호출 스택으로 전파된다.
최종적으로 아무 곳에서도 잡히지 않으면 프로그램이 종료된다.

```js
function a() {
  throw new Error('문제 발생!');
}

function b() {
  a(); // 여기서 에러 발생
}

function c() {
  try {
    b();
  } catch (e) {
    console.error('c에서 에러 처리: ', e.message);
  }
}

c();
```

위 코드에서는 `a()`에서 던진 에러가 `b()`를 거쳐 `c()`의 `catch`에서 잡히게 된다.

그래서..
에러 처리가 뭔데요

**에러 처리**는 선택이 아니라 **필수**다!
try...catch는 **동기 코드**에서 주로 사용된다.
_(비동기 코드에서는 Promise의 .catch() 또는 async/await + try...catch 사용)_
throw를 이용해 **의도적으로 에러를 발생**시킬 수 있다.
에러는 상위로 전파되며, 아무도 잡아주지 않으면 프로그램이 멈춘다.
