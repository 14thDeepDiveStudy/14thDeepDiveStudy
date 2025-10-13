### 33장: 7번째 데이터 타입 Symbol

  - `Symbol`은 ES6에서 도입된 7번째 데이터 타입임.
  - 변경 불가능한 원시 값이고, 다른 값과 중복되지 않는 **유일무이한 값**이다.
  - 이름 충돌 위험이 없는 유일한 프로퍼티 키를 만들 때 주로 사용함.

#### Symbol 값 생성

  - `Symbol()` 함수를 호출해서 생성함. `new` 연산자는 사용하지 않음.
  - `Symbol()` 함수에 전달하는 문자열은 디버깅용 설명일 뿐, 값 자체에는 영향을 주지 않음.

```javascript
// 설명이 같아도 서로 다른 유일무이한 심벌 값임
const mySymbol1 = Symbol('mySymbol');
const mySymbol2 = Symbol('mySymbol');

console.log(mySymbol1 === mySymbol2); // false
```

  - `Symbol`은 문자열이나 숫자로 암묵적 변환이 안 되지만, 불리언으로는 변환됨.
  - `if (mySymbol)` 같은 형태로 존재를 확인할 수 있음.

#### `Symbol.for` / `Symbol.keyFor`

  - `Symbol.for(key)`는 **전역 심벌 레지스트리**를 사용함.
  - 같은 키를 사용하면 항상 같은 심벌 값을 반환해서, 애플리케이션 전역에서 공유 가능함.


```javascript
// 전역 심벌 레지스트리에서 'mySymbol' 키를 검색
const s1 = Symbol.for('mySymbol');
// 이미 있으므로 같은 심벌 값을 반환
const s2 = Symbol.for('mySymbol');

console.log(s1 === s2); // true
```

  - `Symbol.keyFor(symbol)`는 전역 심벌 레지스트리에 저장된 심벌의 키를 추출함.

#### Symbol의 활용

  - **상수 값으로 사용**: 의미 없는 숫자 대신 중복 가능성 없는 심벌 값을 사용하면 더 안전함.


```javascript
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right')
};
```

  - **프로퍼티 키로 사용**: 다른 어떤 프로퍼티 키와도 충돌하지 않음을 보장함.
  - **프로퍼티 은닉**: 심벌을 키로 갖는 프로퍼티는 `for...in`, `Object.keys` 등으로 찾을 수 없음.
  - 단, `Object.getOwnPropertySymbols(obj)` 메서드를 사용하면 찾을 수 있음.
  - **표준 빌트인 객체 확장**: 기존 메서드나 미래에 추가될 메서드와 충돌 없이 안전하게 객체를 확장할 수 있음.

-----

### 34장: 이터러블

  - ES6에서 도입된 `이터레이션 프로토콜`은 순회 가능한 자료구조를 만들기 위한 규칙이다.
  - 이 규칙을 따르면 `for...of` 문, 스프레드 문법, 배열 디스트럭처링 할당 등을 사용할 수 있게 됨.

#### 이터레이션 프로토콜

  - **이터러블 프로토콜**:

      - `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 가져야 함.
      - 이 메서드는 `이터레이터`를 반환해야 함.
      - 이 프로토콜을 지킨 객체가 `이터러블`임.

  - **이터레이터 프로토콜**:

      - `next` 메서드를 가져야 함.
      - `next` 메서드는 `value`와 `done` 프로퍼티를 가진 `이터레이터 리절트 객체`를 반환함.
      - 이 프로토콜을 지킨 객체가 `이터레이터`임.

  - `for...of` 문은 이터러블의 `Symbol.iterator`를 호출해 이터레이터를 얻고, `done`이 `true`가 될 때까지 `next`를 호출하며 `value` 값을 사용함.

#### 사용자 정의 이터러블

  - 일반 객체도 이터레이션 프로토콜을 따르면 이터러블이 될 수 있음.


```javascript
// 피보나치 수열을 구현한 사용자 정의 이터러블
const fibonacci = {
  // Symbol.iterator 메서드를 구현
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1];
    const max = 10;

    // next 메서드를 가진 이터레이터를 반환
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        // value와 done 프로퍼티를 가진 이터레이터 리절트 객체를 반환
        return { value: cur, done: cur >= max };
      }
    };
  }
};

for (const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}
```

#### 이터레이션 프로토콜의 필요성

  - 데이터 공급자(`Array`, `String`, `Map` 등)와 데이터 소비자(`for...of`, 스프레드 문법 등)를 연결하는 **인터페이스** 역할을 함.
  - 다양한 데이터 소스가 하나의 통일된 순회 방식을 갖게 되어 데이터 소비자가 효율적으로 데이터를 사용할 수 있도록 함.

#### 무한 이터러블과 지연 평가

  - 이터러블은 데이터가 필요한 시점에만 데이터를 생성하는 \*\*지연 평가(Lazy Evaluation)\*\*가 가능함.
  - 이를 통해 불필요한 메모리 낭비를 줄이고, 무한 이터러블 같은 것도 표현할 수 있음.


```javascript
// 무한 이터러블을 생성하는 함수
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];
  return {
    [Symbol.iterator]() { return this; },
    next() {
      [pre, cur] = [cur, pre + cur];
      return { value: cur }; // done 프로퍼티를 생략하여 무한 구현
    }
  };
};

// 배열 디스트럭처링 할당을 통해 필요한 만큼만 데이터를 취득
const [f1, f2, f3] = fibonacciFunc();
console.log(f1, f2, f3); // 1 2 3
```