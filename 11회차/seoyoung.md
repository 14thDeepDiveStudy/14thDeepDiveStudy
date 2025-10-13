## 1. Symbol

### 심벌(Symbol)이란?

심벌은 자바스크립트의 원시형인  
`string`, `number`, `boolean`, `undefined`, `null`, `object` 를 뒤이어  
ES6에 7번째로 추가된 원시 자료형 값이다.

다른 값과 절대 중복되지 않으며 변경이 불가능한 고유한 데이터로,  
주로 식별자 충돌 없는 고유한 프로퍼티 키를 만들기 위해 사용한다.

심벌 값은 여타 원시형처럼 리터럴 선언이 불가하며 `Symbol` 함수를 호출해야만 생성할 수 있다.  
이 때 인수로는 선택적으로 설명자 문자열을 넘길 수 있으며,  
설명자는 심볼 값에 직접적인 영향을 끼치지 않고 디버깅 용도로만 사용된다.

함수에 의해 생성된 심벌은 절대 외부로 노출되지 않으며, 중복되지 않는 고유 값을 가진다.

- 얼핏 생성자 함수처럼 보이지만 `Symbol` 은 심볼형 값을 반환하는 함수이다. 따라서 `new` 키워드를 쓰지 않는다.

```tsx
const symbolA = Symbol('symbol');
typeof symbolA; // 'symbol'

const symbolB = Symbol('symbol');
symbolA === symbolB; // false
```

### 심벌 자료형의 특징

심벌 값 역시 객체처럼 접근 시 암묵적으로 래퍼 객체를 생성한다.

```tsx
const mySymbol = Symbol('mySymbol');

console.log(mySymbol.description); // mySymbol
console.log(mySymbol.toString()); // Symbol(mySymbol)
```

심벌은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않는다.  
단 불리언 타입으로는 암묵적 타입 변환이 가능하다.

```tsx
Symbol() + ''; // TypeError: Cannot convert a Symbol value to a string
+Symbol(); // TypeError: Cannot convert a Symbol value to a number

!!Symbol(); // true
```

### 전역 심벌 레지스트리와 심벌 메서드

- `Symbol.for(key)`
  인수로 전달된 문자열을 키로 가지는 새로운 심벌을 생성,  
   전역 심벌 레지스트리에 저장 후 반환하는 메서드.  
   식별자를 키로 가진 심벌이 이미 있다면 해당 심벌 값을 반환한다.
  ```tsx
  const s1 = Symbol.for('mySymbol');
  const s2 = Symbol.for('mySymbol');

  s1 === s2; // true
  ```
- `Symbol.keyFor(symbol)`
  전역 심벌 레지스트리에서 인수로 전달된 심볼을 검색한 후  
   이에 매핑되어 있는 키를 반환하는 메서드.  
   검색 결과가 없을 경우 `undefined` 를 반환한다.
  ```tsx
  const s1 = Symbol.for('foo');
  Symbol.keyFor(s1); // mySymbol

  const s2 = Symbol('bar');
  Symbol.keyFor(s2); // undefined
  ```

## 2. Symbol 활용

### 상수(enum) 대체

아래와 같이 사방을 나타내는 `Direction` 상수를 정의했다고 가정해 보자.  
이 경우 각각의 value보단 상수 이름 자체에만 의미가 있다.

```tsx
const Direction = {
  UP: 1,
  DOWN: 2,
  LEFT: 3,
  RIGHT: 4,
};

const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
  console.log('You are going UP.');
}
```

하지만 이 때 상수의 값이 변경될 수 있는 값(숫자형)이고  
이는 다른 변수와 값이 중복될 수 있어 상수로써의 의미가 약해진다.

따라서 이런 경우 유일무이하고 변경 불가능한 심벌 자료형을 쓸 수 있다.

```tsx
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right'),
};
```

나아가 아래와 같이 `Object.freeze` 메서드를 활용하면  
객체의 변경을 방지하여 **enum(열거형, 숫자 상수 집합)** 데이터를 표현할 수도 있다.

```tsx
const Direction = Object.freeze({
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right'),
});
```

### 프로퍼티 키로 사용

객체 프로퍼티 키로 문자열 대신 심벌 값을 지정하여  
다른 프로퍼티의 키와 절대 충돌하지 않는 프로퍼티를 만들 수 있다.

이 경우 프로퍼티를 읽거나 쓸 땐 키로 사용할 심벌을 대괄호로 묶어 접근해야 한다.

```tsx
const obj = {
  [Symbol('secret')]: 1,
};

obj[Symbol('secret')]; // 1
```

### 프로퍼티 은닉

심벌 키는 `for...in` , `Object.keys` , `Object.getOwnPropertyNames` 메서드 등의  
순회 대상에 포함되지 않기 때문에 외부에 쉽게 노출되지 않는다.

이를 통해 외부 노출 필요가 없는 프로퍼티의 키에 심벌을 할당하면 은닉 프로퍼티를 만들 수 있다.

```tsx
const obj = {
	[Symbol('secret')]: 1
};

fot (const key in obj) {
	console.log(key); // 아무것도 출력되지 않음
}

Object.keys(obj); // []
Object.getOwnPropertyNames(obj); // []
```

예외로 `Object.getOwnPropertySymbols` 메서드를 사용하면  
심벌 값을 키로 사용한 프로퍼티들을 모두 순회하여 얻을 수 있다.

```tsx
const obj = {
  [Symbol('secret')]: 1,
};

const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
obj[symbolKey1]; // 1
```

### 표준 빌트인 객체 확장

표준 빌트인 객체는 충돌을 방지하기 직접 사용자 코드로 확장하기보단  
읽기 전용으로 사용하는 것을 권장한다.

하지만 이 때 중복 가능성이 없는 심볼형 값으로 프로퍼티 키를 생성한다면  
표준 빌트인 객체의 기존 프로퍼티 키와 더불어 앞으로 추가될 프로퍼티들의 키와도  
충돌할 위험 없이 빌트인 객체 확장이 가능하다.

```tsx
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2][Symbol.for('sum')](); // - 3
```

### Well-known Symbol

자바스크립트가 기본 제공하는 빌트인 심벌 값들이다.  
이러한 심벌들은 `Symbol` 함수의 프로퍼티로 할당되어 있다.

- `Symbol.iterator` : 호출 시 이터레이터를 반환하는 메서드 심볼. `for...of` 동작을 정의함.
- `Symbol.toStringTag` : 객체 → 문자열 형변환 포맷 (`[object Object]`)을 변경하는 심볼
- `Symbol.toPrimitive` : 객체의 원시형 변환 규칙 지정
- `Symbol.match`, `Symbol.replace` : 정규표현식 관련 심볼

위와 같은 심벌들은 주로 내부 동작에서 사용하기 때문에  
이를 명시적으로 호출하거나 값을 바꿔 주면 일반 객체에 빌트인 로직 추가 등의 동작이 가능하다.

## 2. 이터러블

### 이터레이션 프로토콜

- **이터레이션 프로토콜(Iteration Protocol)**
  순회 가능한 자료구조를 만들기 위해 명세서에 정의한 규칙.  
   ES6 이전 제각기 다른 순회 방법을 가지던 자료구조들을  
   ’순회 가능한’이라는 의미의 **이터러블(iterable)** 데이터로 통일하기 위해 해당 프로토콜이 등장하게 되었으며, 이후 순회, 반복이 가능한 객체를 정의하기 위해선 이를 지키도록 규정하고 있다.
  이터레이션 프로토콜은 하위 규약인 이터러블, 이터레이터 프로토콜로 구성되어 있다.
- **이터러블 프로토콜(Iterable Protocol)**
  빌트인 심볼 `Symbol.iterator` 메서드를 호출하면  
   이터레이터 프로토콜을 준수한 이터레이터를 반환하는데  
   이러한 규약을 이터러블 프로토콜이라고 한다.
  이 프로토콜을 준수하여 `Symbol.iterator` 메서드를 가지고 있는 객체를 **이터러블(Iterable)**이라고 하며,  
   이터러블은 `for...of` 문 순회 및 스프레드 문법, 배열 구조 분해 할당의 대상으로 사용할 수 있다.
- **이터레이터 프로토콜(Iterator Protocol)**
  `next` 메서드를 소유하며, 해당 메서드를 호출 시 이터러블을 순회하며
  `value` 와 `done` 을 프로퍼티로 갖는 이터레이터 리절트 객체를 반환하는 것을  
   이터레이터 프로토콜이라고 한다.
  이를 준수한 객체를 **이터레이터(Iterator)**라고 한다.

### 이터러블

이터러블은 이터러블 프로토콜을 준수한 객체이다.

즉, `Symbol.iterator` 를 프로퍼티 키로 사용한 메서드를 직접 구현하거나  
프로토타입 체인을 통해 상속받은 객체를 말한다.

빌트인 데이터 중 이를 준수하여 이터러블이라고 말할 수 있는 객체들은 대표적으로 다음과 같다.

| 타입                          | Symbol.iterator 메서드 위치             |
| ----------------------------- | --------------------------------------- |
| **Array**                     | `Array.prototype[Symbol.iterator]`      |
| **String**                    | `String.prototype[Symbol.iterator]`     |
| **Map**                       | `Map.prototype[Symbol.iterator]`        |
| **Set**                       | `Set.prototype[Symbol.iterator]`        |
| **TypedArray**                | `TypedArray.prototype[Symbol.iterator]` |
| **arguments**                 | `arguments[Symbol.iterator]`            |
| **NodeList / HTMLCollection** | 프로토타입에 정의                       |

```tsx
const isIterable = (v) =>
  v !== null && typeof v[Symbol.iterator] === 'function';

isIterable([]); // true
isIterable(''); // true
isIterable(new Map()); // true
isIterable(new Set()); // true
isIterable({}); // false
```
