# 👽 33장. Symbol

## 33.1 Symbol이란?

JavaScript에는 문자열, 숫자, 불리언 같은 익숙한 원시 타입들이 있다. 그런데 이들 외에도 조금은 특별한 '유일하고 변경 불가능한 값'이 존재한다. 그게 바로 Symbol이다.

Symbol이라는 단어는 본래 상징, 표식이라는 뜻을 가지고 있다. JavaScript의 Symbol도 마찬가지로 '**어떤 대상을 직접 나타내기보다 상징적으로 가리키는 고유한 식별자**' 역할을 한다. 이 말은 Symbol이 다른 값과 절대 중복되지 않는 **유일한 존재**라는 뜻이다.

예를 들어, 우리는 객체의 프로퍼티 키로 문자열을 흔히 사용한다. 하지만 여러 코드가 얽힌 환경에서 같은 이름의 키가 존재하면 쉽게 충돌이 일어난다. 이때 Symbol을 사용하면, **겉으로는 같은 이름을 가진 키처럼 보여도 실제로는 완전히 다른 것**으로 취급되어 충돌을 완벽하게 방지할 수 있다.

```js
const ID = Symbol('id');
const user = { name: 'Hyojung', [ID]: 12345 };

console.log(user[ID]); // 12345
console.log(Object.keys(user)); // ['name'] - Symbol 키는 안 나옴
```

Symbol 키는 `for...in`이나 `Object.keys()` 등 일반적인 열거 과정에서 드러나지 않는다. 즉, Symbol은 '유일하면서 감춰진 식별자'를 만들어내는 도구다. 그래서 주로 **내부 동작**, **숨김 속성**, **프레임워크 내부 식별자** 등에 활용된다.

---

## 33.2 Symbol 값의 생성

Symbol은 함수처럼 호출해야 한다.
new 키워드를 붙이면 에러가 발생하는데, 그 이유는 **생성자 함수가 아니라 단순히 값을 만드는 함수**이기 때문이다.

```js
const s1 = Symbol('token');
const s2 = Symbol('token');
console.log(s1 === s2); // false
```

위 예시처럼, description이 같더라도 두 Symbol은 절대 같지 않다. 설명 문자열은 단순히 디버깅용 정보일 뿐, 값의 정체성에는 아무런 영향을 주지 않는다.

하지만 가끔은 모듈이나 파일을 넘어 **같은 Symbol을 공유해야 하는 상황**이 있다.
이때는 `Symbol.for(key)`를 사용한다. 이 메서드는 **전역 심벌 레지스트리(Global Symbol Registry)**에서 key를 조회하고, 이미 존재하면 그것을 반환하고, 없다면 새로 생성해서 등록한다. 즉, `Symbol.for('app.config')`를 여러 번 호출하더라도 항상 같은 Symbol이 반환된다.

```js
const a = Symbol.for('app.config');
const b = Symbol.for('app.config');
console.log(a === b); // true
```

반대로 `Symbol.keyFor(sym)`을 사용하면, Global Symbol Registry에 등록된 Symbol의 key 문자열을 얻을 수 있다. 이때 `Symbol()`로 만든 Symbol은 Registry에 등록되지 않았으므로 `undefined`가 반환된다.

```js
console.log(Symbol.keyFor(a)); // 'app.config'
console.log(Symbol.keyFor(Symbol('x'))); // undefined
```

요약하자면, `Symbol()`은 '지역적이고 완전히 유일한 Symbol'을 만들고, `Symbol.for()`는 '전역적으로 공유 가능한 Symbol'을 만든다. 즉, 둘 다 Symbol이지만 존재의 범위(scope)가 다르다고 보면 된다.

---

## 33.3 Symbol과 상수

우리가 코드를 작성할 때, 자주 문자열 상수를 사용한다. 예를 들어 상태를 구분할 때 'READY', 'RUNNING', 'DONE' 같은 문자열을 쓰곤 한다. 하지만 이런 방식은 오타나 중복 선언으로 인해 버그를 일으키기 쉽다. 이럴 때 Symbol을 사용하면 훨씬 안전하다.

```js
const READY = Symbol('READY');
const RUNNING = Symbol('RUNNING');
const DONE = Symbol('DONE');

function next(state) {
  switch (state) {
    case READY:
      return RUNNING;
    case RUNNING:
      return DONE;
    default:
      throw new Error('Unknown state');
  }
}
```

위 코드는 같은 이름의 문자열이라도 Symbol이기 때문에 충돌할 수 없다. 또한, 누군가 의도치 않게 동일한 문자열을 재사용해도 Symbol은 절대 같지 않기 때문에 안전하다. 이처럼 Symbol은 '**매직 문자열(magic string)**'을 대체하는 안정적인 식별자로 자주 쓰인다. 🪄

이 원리는 Redux, Zustand 같은 상태 관리 라이브러리의 내부에서도 자주 활용된다. 내부에서 액션 타입이나 시스템 식별자를 Symbol로 정의하면, 외부 사용자가 실수로 같은 이름을 정의해도 충돌하지 않기 때문이다.

---

## 33.4 Symbol과 프로퍼티 키

객체의 프로퍼티 키는 원래 문자열로만 존재할 수 있다. 그런데 문자열은 충돌이 너무 쉽다. 누군가 같은 이름을 쓰면 기존 프로퍼티를 덮어쓸 수도 있기 때문이다. 이 문제를 완전히 해결하는 방법이 바로 **Symbol을 키로 사용하는 것**이다.

Symbol은 유일하기 때문에 같은 이름의 Symbol을 두 번 만들더라도 실제로는 서로 다른 키로 동작한다. 따라서 의도치 않은 덮어쓰기를 완벽히 막을 수 있다.

```js
const PRIVATE = Symbol('PRIVATE');
const user = { name: 'Hyojung', [PRIVATE]: 123 };

console.log(user.name); // 'Hyojung'
console.log(Object.keys(user)); // ['name'] - Symbol 키는 보이지 않음
```

이렇게 하면 PRIVATE이라는 키는 외부에서 접근하기 어렵고 일반적인 열거에서도 노출되지 않는다. Symbol은 내부 로직에서만 사용하는 '**은밀한 식별자**'로서 훌륭한 도구다. 이 덕분에 라이브러리나 프레임워크는 내부용 데이터를 숨겨두되, 필요할 때만 꺼내 쓸 수 있다.

---

## 33.5 Symbol과 프로퍼티 은닉

앞서 살펴본 것처럼, Symbol로 정의된 프로퍼티는 `for...in`이나 `Object.keys()`에 포함되지 않는다. 이 덕분에 의도치 않은 접근을 막고, 객체의 내부 상태를 **부분적으로 은닉**할 수 있다.

물론 완전한 보안 수단은 아니다. `Object.getOwnPropertySymbols()`를 사용하면 Symbol 키를 직접 조회할 수 있다. 그럼에도 불구하고 평범한 접근 방법으로는 보이지 않기 때문에 라이브러리 내부 구현을 외부에 노출시키지 않으려는 목적에는 충분하다.

```js
const SECRET = Symbol('SECRET');
const cat = { name: 'Miyuk', [SECRET]: { health: 'good' } };

console.log(JSON.stringify(cat)); // {"name":"Miyuk"} (Symbol 키 제외)
```

JSON 직렬화에서도 Symbol 프로퍼티는 자동으로 무시된다. 즉, 데이터를 외부로 보낼 때 내부 Symbol 정보는 감춰진다. 이런 특성 때문에 보조 정보나 메타데이터를 감추는 용도로 자주 사용된다.

---

## 33.6 Symbol과 표준 빌트인 객체 확장

Symbol은 단순한 '숨김 키' 그 이상이다. JavaScript는 여러 내장 동작을 Symbol 기반으로 정의하고 있다. 이 Symbol들은 '**Well-known Symbol**'이라고 부르며, 이를 구현하면 기본 동작을 사용자 정의 방식으로 확장할 수 있다.

대표적인 예는 `Symbol.iterator`다. 이걸 구현하면 그 객체는 **이터러블(iterable)**이 되어 `for...of`문으로 순회할 수 있다.

```js
const numbers = {
  data: [1, 2, 3],
  [Symbol.iterator]() {
    let i = 0;
    return {
      next: () => ({ value: this.data[i++], done: i > this.data.length }),
    };
  },
};

for (const num of numbers) console.log(num); // 1 2 3
```

이 외에도

- `Symbol.toStringTag` → `Object.prototype.toString`에서 표시되는 객체 이름 커스터마이즈
- `Symbol.toPrimitive` → 숫자나 문자열 변환 시 동작 정의
- `Symbol.match`, `Symbol.replace` → 정규 표현식 커스터마이즈

이런 기능들을 통해 Symbol은 단순한 '식벌자'를 넘어 **언어 동작 자체를 확장하는 인터페이스**로 쓰인다.

---

## 33.7 Well-known Symbol

JavaScript 엔진은 여러 내장 기능을 특정 Symbol로 구분해 동작한다. 이 Symbol들은 'Well-known Symbol'이라 부르며, 전역 Symbol 객체의 프로퍼티로 미리 정의되어 있다.

대표적인 것들을 살펴보면 다음과 같다.

| **심벌 이름**                                                        | **역할 / 설명**                                                                         |
| -------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `Symbol.iterator`                                                    | `for...of` 루프나 전개 연산자(`...`)에서 순회의 기준이 되는 메서드.                     |
| `Symbol.asyncIterator`                                               | `for await...of` 구문을 위한 비동기 이터레이션 기준.                                    |
| `Symbol.toStringTag`                                                 | `Object.prototype.toString.call(obj)` 실행 시 표시되는 객체 이름을 커스터마이즈.        |
| `Symbol.toPrimitive`                                                 | 객체가 숫자나 문자열 등 원시값으로 변환될 때의 동작을 정의.                             |
| `Symbol.match` / `Symbol.replace` / `Symbol.search` / `Symbol.split` | 정규 표현식과 관련된 메서드의 동작 방식을 커스터마이즈.                                 |
| `Symbol.hasInstance`                                                 | `instanceof` 연산자 동작을 사용자 정의할 때 사용.                                       |
| `Symbol.species`                                                     | 파생 객체를 생성할 때 사용할 생성자를 지정 (예: `Array` 메서드들이 반환할 클래스 지정). |
| `Symbol.unscopables`                                                 | `with` 문에서 제외할 프로퍼티를 정의 (일반적으로 직접 사용할 일은 거의 없음).           |
| `Symbol.isConcatSpreadable`                                          | 배열 합칠 때(`concat`) 내부 요소를 펼칠지 여부를 지정.                                  |

예를 들어 `Symbol.toStringTag`를 사용하면 객체가 `Object.prototype.toString()`으로 출력될 때 이름을 원하는 대로 바꿀 수 있다.

```js
class Database {
  get [Symbol.toStringTag]() {
    return 'PawBuddyDB';
  }
}

console.log(Object.prototype.toString.call(new Database())); // [object PawBuddyDB]
```

Symbol은 이렇게 언어의 규약을 재정의하는 통로로 쓰인다.

이제부터 이어질 34장은 그 대표적인 예 중 하나인 `Symbol.iterator` 기반의 이터러블 구조를 본격적으로 다룬다.

---

# 🌕 34장. 이터러블(Iterable)

## 34.1 이터레이션 프로토콜

Iteration Protocol은 데이터를 순회하기 위한 약속된 규칙이다. JavaScript의 모든 순회 가능한 값(Array, String, Map 등)은 이 규칙을 따른다. 이 규칙은 두 부분으로 구성된다.

① 이터러블(Iterable) - `Symbol.iterator` 메서드를 가지고 있으며, 이 메서드를 호출하면 Iterator를 반환해야 한다.
② 이터레이터(Iterator) - `next()` 메서드를 가지고 있으며, 호출할 때마다 `{ value, done }` 객체를 반환한다.

```js
const arr = [10, 20];
const iterator = arr[Symbol.iterator]();

console.log(iterator.next()); // { value: 10, done: false }
console.log(iterator.next()); // { value: 20, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

이처럼 배열, 문자열, Map, Set 등은 기본적으로 이터러블이다. 이 덕분에 `for...of`, 전개 연산자(`...`), `Array.from` 등에서 같은 방식으로 순회할 수 있다. 즉, 이터레이션 프로토콜은 모든 자료구조가 같은 언어적 문법으로 순회되게 해주는 통일된 인터페이스다.

---

## 34.2 빌트인 이터러블

JavaScript에는 이미 여러 **내장 이터러블 객체**가 존재한다. 배열(Array), 문자열(String), 집합(Set), 맵(Map), 그리고 일부 DOM 컬렉션들이 대표적이다.

```js
for (const char of 'JS') console.log(char); // 'J', 'S'

const map = new Map([
  ['a', 1],
  ['b', 2],
]);
for (const [k, v] of map) console.log(k, v); // a 1 / b 2
```

각 객체는 자신만의 `Symbol.iterator` 구현을 가지고 있어서 동일한 `for...of` 문법으로 다룰 수 있다는 점이 핵심이다. 즉, 자료형이 다르더라도 **순회 방식은 하나로 통일**되어 있다.

---

## 34.3 for...of 문

`for...of` 문은 이터러블 전용 순회문이다.
객체의 키를 순회하는 `for...in`과 달리, `for...for`는 이터러블의 값(value)을 순회한다.

```js
const arr = ['a', 'b', 'c'];
for (const v of arr) console.log(v); // a b c
```

객체 리터럴은 이터러블이 아니므로 `for...of`로 순회할 수 없다.. 그럴 때는 `Object.keys()`나 `for...in`을 사용해야 한다. 이 구분은 JavaScript 순회 구조의 가장 중요한 차이점 중 하나다.

---

## 34.4 이터러블과 유사 배열 객체

유사 배열 객체(array-like object)는 인덱스(0, 1, 2, ...)와 length 프로퍼티를 갖지만 `Symbol.iterator`를 가지고 있지 않다. 대표적으로 arguments나 NodeList 같은 DOM 컬렉션이 그렇다.

```js
function test() {
  console.log(arguments.length); // 예: 3
}
```

이런 객체는 겉보기엔 배열처럼 생겼지만 실제로는 이터러블이 아니다. 따라서 전개 연산자나 for...of로 바로 순회할 수 없다. 이럴 때는 Array.from()을 사용해 이터러블로 변환하는 것이 일반적이다.

```js
function doubleAll() {
  const arr = Array.from(arguments);
  return arr.map(x => x * 2);
}
console.log(doubleAll(1, 2, 3)); // [2, 4, 6]
```

즉, 배열처럼 보인다고 모두 이터러블인 것은 아니다. 이 차이를 이해하면 JavaScript의 내부 데이터 흐름을 정확히 잡을 수 있다.

---

## 34.5 이터레이션 프로토콜의 필요성

왜 이렇게 복잡한 프로토콜이 필요한 걸까? 그 이유는 간단하다. 자료구조가 달라도 공통된 순회 방법을 제공하기 위해서다.

배열, 문자열, Set, Map은 모두 다른 구조를 갖고 있지만, 이터레이션 프로토콜을 따름으로써 **같은 문법(`for...of`, 전개 연산자 등)**으로 접근할 수 있다. 이 덕분에 다양한 데이터 구조를 하나의 추상화 레벨에서 다룰 수 있다.

```js
function sum(iterable) {
  let total = 0;
  for (const v of iterable) total += v;
  return total;
}

console.log(sum([1, 2, 3])); // 6
console.log(sum(new Set([1, 2]))); // 3
```

즉, 이터레이션 프로토콜은 데이터 소비(consumer) 측의 코드를 단순하게 만들어주는 '공통 언어'다.

---

## 34.6 사용자 정의 이터러블

### 1️⃣ 직접 구현하기

개발자가 만든 객체도 `Symbol.iterator`를 정의하면 스스로 이터러블이 될 수 있다.

```js
const range = (start, end) => ({
  [Symbol.iterator]() {
    let cur = start;
    return {
      next() {
        return cur <= end ? { value: cur++, done: false } : { done: true };
      },
    };
  },
});

console.log([...range(3, 6)]); // [3, 4, 5, 6]
```

이처럼 Symbol.iterator를 직접 구현하면 **커스텀 순회 규칙**을 만들 수 있다.

### 2️⃣ 제너레이터 함수로 단축하기

직접 `next()`를 구현하는 대신 제너레이터(function\*)를 쓰면 간결해진다. `yield`를 사용할 때마다 자동으로 value와 done 구조를 반환해준다.

```js
function* odds(n) {
  for (let i = 1; i <= n; i += 2) yield i;
}
console.log([...odds(7)]); // [1, 3, 5, 7]
```

### 3️⃣ 이터러블이면서 이터레이터인 객체

이터레이터 자신이 `Symbol.iterator`를 구현하여 자기 자신을 반환하면 그 객체는 이터러블이자 이터레이터가 된다.

```js
const countdown = from => ({
  cur: from,
  next() {
    return this.cur >= 0 ? { value: this.cur--, done: false } : { done: true };
  },
  [Symbol.iterator]() {
    return this;
  },
});

for (const n of countdown(3)) console.log(n); // 3 2 1 0
```

이런 구조는 한 번만 순회되는 스트림(stream) 구조를 표현할 때 자주 사용된다.

### 4️⃣ 무한 이터러블과 지연 평가(Lazy Evaluation)

이터레이터의 또 다른 장점은 지연 평가(lazy evaluation)다. `next()`를 호출할 때마다 값을 생성하기 때문에 이론적으로 무한한 시퀀스도 안전하게 표현할 수 있다.

```js
function* naturals() {
  let i = 1;
  while (true) yield i++;
}

function* take(n, iterable) {
  let count = 0;
  for (const x of iterable) {
    if (count++ >= n) return;
    yield x;
  }
}

console.log([...take(5, naturals())]); // [1, 2, 3, 4, 5]
```

무한 이터러블은 데이터 스트림, 이벤트 시퀀스, 비동기 흐름 제어 등 다양한 영역에서 활용된다. 값이 실제로 필요할 때만 계산하기 때문에 **메모리 낭비 없이 효율적**이다.

---

# 요약

Symbol과 Iterable은 서로 밀접하게 연결되어 있다. Symbol은 **유일한 식별자**로서 객체의 키나 내부 동작을 정의하고, 그 중 `Symbol.iterator`는 **이터레이션 프로토콜의 핵심 엔트리 포인트**다.

즉, Symbol이 언어의 확장성과 은닉성을 담당한다면, Iterable은 그 확장된 객체들이 같은 문법으로 함께 작동할 수 있게 만드는 규칙이다.

이 두 개념을 이해하면, JavaScript의 내부 동작 즉 값이 어떻게 순회되고, 언제 생성되며, 어떤 규칙으로 비교되는가를 언어의 근본적인 수준에서 바라볼 수 있게 된다.
