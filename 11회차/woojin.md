# 33-Symbol, 34-이터러블

## Symbol

### 심벌이란?

심벌은 js가 갖고있는 타입 중 하나이다.
js의 타입에는 `string`, `number`,`boolean`,`undefined`,`null`,`object`이 있는데 **ES6**에서 새로운 타입인 `Symbol`이 추가되며 총 7가지의 타입을 갖게된다.

심벌은 변경 불가능한 원시 타입의 값으로, 중복되지 않는 유일무이한 값이며 충돌 위험이 없는 프로퍼티 키를 만들기 위해 사용된다.

> 프로퍼티 키로 사용가능한 값은 모든 문자열 or 심볼

### 심벌 값 생성

#### Symbol 함수

심벌 값은 다른 타입과 달리 리터럴 표기법이 아닌, **Symbol 함수**를 호출해야만 생성할 수 있다.
이때 생성된 심벌 값은 외부로 노출되지 않으며, **다른 값과 중복되지 않는 유일무이한 값이다.**

```jsx
//심벌 함수를 호출해 유일무이한 심벌 값을 생성
const mySymbol = Symbol();

console.log(typeof mySymbol); // symbol
console.log(mySymbol); // Symbol()
```

생성자 함수, 클래스와 달리 Symbol 함수는 new 연산자와 함께 호출하지 않는데, 생성되는 값이 객체가 아닌 **변경 불가능한 원시 값이기 때문**

Symbol 함수에 선택적으로 문자열을 인수로 전달 가능하며, 이 문자열은 심벌 값에 대한 설명으로 심벌 값에 영향 ❌

```jsx
const mySymbol = Symbol("mySymbol");
const mySymbol2 = Symbol("mySymbol");

console.log(mySymbol === mySymbol2); // false
```

심벌 값을 `string`, `number`,`boolean`과 같이 객체처럼 접근하면 암묵적으로 래퍼 객체를 생성

```jsx
console.log(mySymbol.description); // mySymbol
console.log(mySymbol.toString()); // Symbol(mySymbol)
```

심벌값은 암묵적으로 `string`, `number` 타입 변환 불가능

```jsx
console.log(mySymbol + " "); // Cannot convert a Symbol value to a string
console.log(+mySymbol); // Cannot convert a Symbol value to a string
```

`boolean` 타입은 압묵적 타입 변환 가능

```jsx
console.log(!!mySymbol + " "); // true
if (mySymbol) console.log("논리형으로 변환 가능");
```

#### Symbol.for / Symbol.keyFor

- **Symbol.for**
  인수로 전달받은 문자열을 키로 사용해 키와 심벌 값의 쌍들이 저장된 전역 심벌 레지스트리(심벌 값 저장소)에서 해당 키와 일치하는 심벌 값을 검색
  - 검색에 성공 > 새로운 심벌 값을 생성하지 않고 검색된 심벌 값을 반환
  - 검색에 실패 > 새로운 심벌 값을 생성하고 Symbol.for의 인수로 전달된 키로 전역 심벌 레지스트리에 저장 후 생성된 심벌 값 반환

```jsx
// 전역 심볼 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for("mySymbol");

// 전역 심볼 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 있으면으면 해당 심벌 값을 반환
const s2 = Symbol.for("mySymbol");

console.log(s1 === s2); // true
```

1. Symbol 함수 호출시 유일무이한 심벌 값 생성
2. js엔진이 관리하는 전역 심벌 레지스트리에서 심벌 값을 검색할 수 있는 키를 지정하지 못하기에 전역 심벌 레지스트리에 등록되어 관리되지 않음
3. Symbol.for를 사용해 앱 전역에서 중복되지 않는 심벌 값을 생성해 전역 심벌 레지스트리를 통해 공유 가능

- **Symbol.keyFor**
  전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출 가능

```jsx
const s1 = Symbol.for("mySymbol");
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s1); // mySymbol

const s2 = Symbol("foo");
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s2); // undefined
```

### 심벌과 상수

```jsx
// 방향을 나타내는 상수를 정의
const Direction = {
  UP: Symbol("up"),
  DOWN: Symbol("down"),
  LEFT: Symbol("left"),
  RIGHT: Symbol("right"),
};

const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
  console.log("UP방향");
} // UP방향
```

상수 Direction에 심벌이 아닌 일반 값으로 들어가면 값이 변할 수 있다.(상수 이름 자체에 의미를 가짐)
이는 변경/중복될 가능성이 있기에 상수 대신 중복될 가능성이 없는 심벌 값을 사용하는게 적절하다.

> **enum**
> enum은 명명된 숫자 상수의 집합으로 열거형으로 부른다.
> js는 enum을 지원하지 않지만, **다른 프로그래밍 언어와 TS는 enum을 지원한다.**
> js에서 enum을 흉내내려면 `Object.freeze`와 심벌값을 사용한다.

### 심벌과 프로퍼티 키

앞서 말했듯 객체의 프로퍼티 키는 모든 문자열 or 심벌 값으로 만들 수 있으며, 동적으로 생성할 수 있다.

심벌 값으로 프로퍼티 키를 동적 생성하려면 프로퍼티 키로 사용할 심벌 값에 대괄호를 사용한다.(접근도 대괄호)

```jsx
const obj = {
  // 심벌 값으로 프로퍼티 키를 생성
  [Symbol.for("mySymbol")]: 1,
};

obj[Symbol.for("mySymbol")]; // 1
```

**심벌 값으로 프로퍼티 키를 만들면 다른 프로퍼티 키와 절대 충돌하지 않는다.**

### 심벌과 프로퍼티 은닉

위에서 알아본 심벌값을 프로퍼티 키를 사용해 만든 프로퍼티는 `for...in`, `Object.keys`, `Object.getOwnPropertyNames`로 찾을 수 없다.
즉, 외부에 노출할 필요가 없는 프로퍼티를 **은닉할 수 있다.**

하지만 `Object.getOwnPropertySymbols`를 사용하면 프로퍼티를 찾을 수 있다.

```jsx
// getOwnPropertySymbols는 인수로 전달한 객체의 심벌 프로퍼티 키를 배열로 반환
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(mySymbol)]

// getOwnPropertySymbols로 심벌 값도 찾을 수 있다.
const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
console.log(obj[symbolKey1]); // 1
```

### 심벌과 표준 빌트인 객체 확장

일반적으로 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가해 확장하는 것은 권장되지 않는다.
표준 빌트인 객체는 읽기 전용으로 사용하는 것이 좋다.

이는 개발자의 커스텀 메서드와 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문이다.
하지만 심벌 값으로 프로퍼티 키를 생성하여 빌트인 객체를 확장하면 표준 빌트인 객체의 기존 프로퍼티와 충돌할 일이 없기 때문이다.

### Well-known Symbol

js가 기본 제공하는 빌트인 심벌 값은 `Symbol 함수`의 프로퍼티에 할당되어 있다.

![](https://velog.velcdn.com/images/wjinss/post/d14b754a-4d4b-4ca0-9bb5-e298d38779ee/image.png)

js가 기본 제공하는 빌트인 심벌 값을 ECMAScript 사양에서는 **Well-Known Symbol**이라 부르며, js 엔진 내부 알고리즘에 사용된다.

`for...of`문으로 순회 가능한 빌트인 이터러블은 `Symbol.iterator`를 키로 갖는 메서드를 가지며, 해당 메서드를 호출하면 이터레이터를 반환하도록 ECMAScript 사양에 규정(이터레이션 프로토콜)되어 있다.
이때 빌트인 이터러블은 이터레이션 프로토콜을 준수한다.

빌트인 이터러블이 아닌 일반 객체를 이터러블처럼 동작되게 구현하려면 이터레이션 프로토콜을 따르면 된다.
즉, `Symbol.iterator`를 키로 갖는 메서드를 객체에 추가하고 이터레이터를 반환하면 그 객체는 이터러블이 된다.

```jsx
const iterable = {
  // Symbol.iterator를 구현하여 이터러블 프로토콜을 준수
  [Symbol.iterator]() {
    let cur = 1;
    const max = 5;
    // Symbol.iterator는 next 메서드르 소유한 이터레이터를 반환
    return {
      next() {
        return { value: cur++, done: cur > max + 1 };
      },
    };
  },
};

for (const num of iterable) {
  console.log(num); // 1 2 3 4 5
}
```

이터레이션 프로토콜을 준수하기 위해 일반 객체에 추가해야 하는 메서드의 키 `Symbol.iterator`는 중복될 일이 없으며, 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해 도입되었다.(하위호환성 보완)

## 이터러블

### 이터레이션 프로토콜

앞서 언급했듯 이터레이션 프로토콜은 순회 가능한 자료 구조를 만들기 위해 ECMAScript 사양에 규정된 규칙이다.

이터레이션 프로토콜 이전: 배열, 문자열, 유사 배열 객체, DOM 컬렉션 등은 통일된 방법이 없이 다양한 방법으로 순회
이터레이션 프로토콜 이후: 순회 가능한 데이터 컬렉션을 이터러블로 통일해 `for...of`문, 스프레드 문법, 배열 구조 분해 할당으로 사용 가능

#### 이터레이션 프로토콜의 종류

- **이터러블 프로토콜**

  - `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속 받은 `Symbol.iterator`를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환하는 규약
  - **이터러블 프로토콜을 준수한 객체를 이터러블이라 함**
  - 이터러블은 `for...of`문, 스프레드 문법, 배열 구조 분해 할당으로 사용 가능

- **이터레이터 프로토콜**
  - 이터레이터의 `Symbol.iterator`를 호출하면 이터레이터 프로토콜을 준수한 **이터레이터**를 반환
  - 이터레이터는 next 메서드를 소유하며, 해당 메서드 호출 시 이터러블을 순회하며 `value`, `done` 프로퍼티를 갖는 **이터레이터 리절드 객체**를 반환
  - 이터레이터는 이터러블의 요소를 탐색하기 위한 포인터 역할

![](https://velog.velcdn.com/images/wjinss/post/ac449383-cd68-47aa-b4ce-4a8aa8e1ce28/image.png)

#### 이터러블

앞서 말했듯 **이터러블 프로토콜을 준수한 객체**를 이터러블이라 한다.

객체가 이터러블인지 확인하는 함수는 다음과 같이 구현할 수 있다.

```jsx
const isIterable = (v) =>
  v !== null && typeof v[Symbol.iterator] === "function";

// 배열, 문자열, Map, Set 등은 이터러블
isIterable([]); // true
isIterable(""); // true
isIterable(new Map()); // true
isIterable(new Set()); // true
isIterable({}); // true
```

`Symbol.iterator` 메서드를 직접 구현하지 않거나 상속받지 않은 객체는 이터러블이 아니다.
즉, 일반 객체는 `for...of`문, 배열 구조 분해 할당을 사용할 수 없다.

```jsx
const obj = { a: 1, b: 2 };

// 일반 객체는 Symbol.iterator 메서드를 구현하거나 상속받지 않는다.
// 따라서 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다.
console.log(Symbol.iterator in obj); // false

// 이터러블이 아닌 일반 객체는 for...of 문으로 순회 ❌
for (const item of obj) {
  console.log(item); // TypeError: obj is not iterable
}

// 이터러블이 아닌 일반 객체는 배열 구조 분해 할당 사용 ❌
const [a, b] = obj; // TypeError: obj is not iterable
```

예외적으로 스프레드 문법은 일반 객체에서 사용할 수 있다.

```jsx
console.log({ ...obj }); // {a: 1, b: 2}
```

일반 객체도 이터러블 프로토콜을 준수하도록 구현하면 이터러블이 된다. 이에 대해선 밑에서 자세히 알아보자

#### 이터레이터

이터러블의 `Symbol.iterator`를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환하며, **`next`메서드를 갖는다.**

```jsx
// 배열은 이터러블 프로토콜을 준수한 이터러블
const array = [1, 2, 3];

// Symbol.iterator는 이터레이터를 반환
const iterator = array[Symbol.iterator]();

// Symbol.iterator가 반환한 이터레이터는 next 메서드를 갖는다
console.log("next" in iterator);
```

next 메서드는 이터러블의 각 요소를 순회하는 포인터 역할을 하며, 한 단계씩 순회하며 결과를 나타내는 **이터레이터 리절드 객체**를 반환

**이터레이터 리절드 객체**

- `value` : 현재 순회 중인 이터러블의 값
- `done` : 이터러블의 순회 완료 여부

```jsx
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
console.log(iterator.next()); // {value: 3, done: false}
console.log(iterator.next()); // {value: undefined, done: true}
```

### 빌트인 이터러블

| 빌트인 이터러블 | Symbol.iterator 메서드                                                         |
| --------------- | ------------------------------------------------------------------------------ |
| Array           | Array.prototype[Symbol.iterator]                                               |
| String          | String.prototype[Symbol.iterator]                                              |
| Map             | Map.prototype[Symbol.iterator]                                                 |
| Set             | Set.prototype[Symbol.iterator]                                                 |
| TypedArray      | TypedArray.prototype[Symbol.iterator]                                          |
| arguments       | arguments[Symbol.iterator]                                                     |
| DOM 컬렉션      | NodeList.prototype[Symbol.iterator], HTMLCollection.prototype[Symbol.iterator] |

### for...of문

`for...of`문은 이터러블을 순회하면서 이터러블의 요소를 변수에 할당한다.

```jsx
for(변수선언문 of 이터러블){...}
```

`for...of`은 `for...in`문과 매우 유사하다.

```jsx
for(변수선언문 in 객체){...}
```

`for...in`은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중 `[[Enumerable]]`의 값이 **true**인 프로퍼티를 순회하고 열거한다.
이때 프로퍼티 키가 심벌인 프로퍼티는 열거 ❌

`for...of`문은 내부적으로 이터레이터의 `next`메서드를 호출하여 이터러블을 순회하며 `next`메서드가 반환한 **이터레이터 리절트 객체**의 `value` 값을 `for...of`문의 변수에 할당한다. 그리고 `done`의 값이 true가 되면 순회를 중단한다.

### 이터러블과 유사 배열 객체

유사 배열 객체는 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 `length` 프로퍼티를 갖는 객체를 말한다.
`for`문으로 순회할 수 있고, 인덱스로 프로퍼티 값에 접근할 수 있다.

```jsx
// 유사 배열 객체
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

// 유사 배열 객체는 length 프로퍼티를 갖기 때문에 for문으로 순회할 수 있다.
for (let i = 0; i < arrayLike.length; i++) {
  // 유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다.
  console.log(arrayLike[i]); // 1 2 3
}
```

유사 배열 객체는 이터러블이 아닌 일반 객체기 때문에 `for...of`문으로 순회할 수 없다. (`Symbol.iterator`가 없기 때문)

arguments, NodeList, HTMLCollection은 유사 배열 객체이면서 이터러블이다.
ES6에서 이터러블이 도입되면서 해당 유사 배열 객체에 `Symbol.iterator` 메서드가 구현되어 이터러블이 되었으나, 아직 `length` 프로퍼티를 가지기 때문에 유사 배열 객체이면서 이터러블이다.

배열도 ES6에서 이터러블이 도입되며 `Symbol.iterator` 메서드가 구현되어 이터러블이 됐다.

하지만 모든 유사 배열 객체가 이터러블은 아니다. 이땐 `Array.from` 메서드를 사용해 배열로 변환해 유사 배열 객체로 만들 수 있다.

```jsx
const arr = Array.from(arrayLike);
console.log(arr); // [1, 2, 3]
```

### 이터레이션 프로토콜의 필요성

ES6 이전 순회 가능한 데이터 컬렉션은 각자 순회 방법이 달랐으나, ES6 이후 이터러블로 통일해 `for...of`문, 스프레드 문법, 배열 구조 분해 할당을 사용할 수 있게 일원화했다.
이터러블은 `for...of`문, 스프레드 문법, 배열 구조 분해 할당과 같은 **데이터 소비자에 의해 사용**되므로 **데이터 공급자의 역할을 한다고 할 수 있다. **

각자의 방법대로 순회하는 것은 효율적이지 않기에, 다양한 데이터 공급자가 이터레이션 프로토콜을 준수하게 규정하면 데이터 소비자는 이터레이션 프로토콜만 지원되게 구현하면 된다.

이처럼 이터레이션 프로토콜은 다양한 데이터 공급자가 하나의 순회 방식을 갖도록 규정하여 데이터 소비자가 효율적으로 다양한 데이터 공급자를 사용할 수 있도록 **데이터 소비자와 데이터 공급자를 연결하는 인터페이스 역할을 한다**

![](https://velog.velcdn.com/images/wjinss/post/c514c73b-c84d-45e0-9326-41fc446fd5db/image.png)

### 사용자 정의 이터러블

#### 사용자 정의 이터러블 구현(커스텀 이터러블)

일반 객체가 이터레이션 프로토콜을 준수하도록 구현하면 사용자 정의 이터러블이 된다.
피보나치 수열을 구현한 사용자 정의 이터러블을 구현하면 다음과 같다

```jsx
const fibonacci = {
  // Symbol.iterator 메서드를 구현하여 이터러블 프로토콜을 준수
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1];
    const max = 10; // 최대값

    // Symbol.iterator 메서드는 next 메소드를 소유한 이터레이터를 반환
    // next 메서드는 이터레이터 리절트 객체를 반환
    return {
      // fibonacci 객체를 순회할 때마다 next 메서드가 호출
      next() {
        [pre, cur] = [cur, pre + cur];
        return {
          value: cur,
          done: cur >= max,
        };
      },
    };
  },
};

// 이터러블의 최대값을 외부에서 전달할 수 없다.
// 이터러블인 fibonacci 객체를 순회할 때마다 next 메서드가 호출
for (const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}
```

사용자 정의 이터러블의 순서

1. 이터레이션을 준수하도록 `Symbol.iterator` 메서드 구현
2. `Symbol.iterator` 메서드가 `next` 메서드를 갖는 이터레이터를 반환
3. `value`, `done` 프로퍼티를 갖는 이터레이터 리절트 객체를 반환
4. `for...of`은 `done`의 값이 true가 되면 반복 중단

**스프레드 문법, 배열 구조 분해 할당에서의 사용**

```jsx
const arr = [...fibonacci];
console.log(arr); // [ 1, 2, 3, 5, 8 ]

const [first, second, ...rest] = fibonacci;
console.log(first, second, rest); // 1 2 [ 3, 5, 8 ]
```

#### 이터러블을 생성하는 함수

위 fibonacci 이터러블에는 외부에서 값을 전달할 방법이 없다.
이터러블의 최대값을 외부에서 전달할 수 있도록 수정하기 위해선 이터러블의 최대 순회수를 전달받아 이터러블을 반환하는 함수를 만들면 된다.

```jsx
// 이터러블을 반환하는 함수
// 수열의 최대값을 인수로 받는다
const fibonacciFunc = function (max) {
  let [pre, cur] = [0, 1];

  // Symbol.iterator 메서드를 구현한 이터러블을 반환
  return {
    [Symbol.iterator]() {
      // Symbol.iterator 메서드는 next 메서드를 소유한 이터레이터를 반환
      // next 메소드는 이터레이터 리절트 객체를 반환
      return {
        // fibonacci 객체를 순회할 때마다 next 메소드가 호출
        next() {
          [pre, cur] = [cur, pre + cur];
          return {
            value: cur,
            done: cur >= max,
          };
        },
      };
    },
  };
};

// 이터러블을 반환하는 함수에 이터러블의 최대값을 전달하며 호출
for (const num of fibonacciFunc(10)) {
  console.log(num); // 1 2 3 5 8
}
```

#### 이터러블이면서 이터레이터인 객체를 생성하는 함수

이터레이터를 생성하려면 이터러블의 `Symbol.iterator` 메서드를 호출해야 한다.
이터러블이면서 이터레이터인 객체를 생성하면 `Symbol.iterator` 메서드를 호출하지 않아도 된다.

```jsx
// 이터러블이면서 이터레이터인 객체
// 이터레이터를 반환하는 Symbol.iterator 메서드와 이터레이션 리절트 객체를 반환하는 next 메서드를 소유
{
  [Symbol.iterator](){return this},
    next(){
    	return {value: any, done: boolean}
    }
}
// Symbol.iterator는 this를 반환하므로 next 메서드를 갖는 이터레이터를 반환
```

앞에서 살펴본 fibonacciFunc 함수를 이터러블임녀서 이터레이터인 객체를 생성해 반환하는 함수로 변경하면 다음과 같다.

```jsx
// 이터러블이면서 이터레이터인 객체를 반환하는 함수
const fibonacciFunc = function (max) {
  let [pre, cur] = [0, 1];

  // Symbol.iterator 메서드와 next 메서드를 소유한 이터러블이면서 이터레이터인 객체를 반환
  return {
    // Symbol.iterator 메소드
    [Symbol.iterator]() {
      return this;
    },
    // next 메소드는 이터레이터 리절트 객체를 반환
    next() {
      [pre, cur] = [cur, pre + cur];
      return {
        value: cur,
        done: cur >= max,
      };
    },
  };
};

// iter는 이터러블이면서 이터레이터이다.
let iter = fibonacciFunc(10);

// iter는 이터러블이다.
for (const num of iter) {
  console.log(num); // 1 2 3 5 8
}

iter = fibonacciFunc(10);

// iter는 이터레이터이다.
console.log(iter.next()); // {value: 1, done: false}
console.log(iter.next()); // {value: 2, done: false}
console.log(iter.next()); // {value: 3, done: false}
console.log(iter.next()); // {value: 5, done: false}
console.log(iter.next()); // {value: 8, done: false}
console.log(iter.next()); // {value: 13, done: true}
```

#### 무한 이터러블과 지연 평가(Lazy evaluation)

무한 이터러블을 생성하는 함수를 정의해보자.
이를 통해 무한 수열(infinite sequence)을 간단히 구현할 수 있다.

```jsx
// 무한 이터러블을 생성하는 함수
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한을 구현하기 위해 done 프로퍼티를 생략
      return { value: cur };
    },
  };
};

// fibonacciFunc 함수는 무한 이터러블을 생성한다.
for (const num of fibonacciFunc()) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...
}

// 배열 구조 분해 할당을 통해 무한 이터러블에서 3개만 취득
const [f1, f2, f3] = fibonacciFunc();
console.log(f1, f2, f3); // 1 2 3
```

이터러블은 데이터 공급자의 역할을 한다. 배열이나 문자열 등은 모든 데이터를 메모리에 미리 확보한 다음 데이터를 공급한다.
위의 예제의 이터러블은 **지연평가**를 통해 데이터를 생성하는데, 지연 평가는 데이터가 필요한 시점이 되면 그때 데이터를 생성하는 기법이다.

위 예제는 무한 이터러블을 생성한다. 하지만 함수가 생성한 무한 이터러블은 데이터를 공급하는 메커니즘을 구현한 것으로 데이터 소비자인 `for...of` 문이나 구조 분해 할당이 실행되기 이전까지 데이터를 생성하지는 않는다.
`for...of` 문의 경우, 이터러블을 순회할 때 내부에서 이터레이터의 `next` 메서드를 호출하는데 바로 이때 데이터가 생성된다.
즉, 데이터가 필요할 때까지 데이터의 생성을 지연하다가 데이터가 필요한 순간 데이터를 생성한다.

이처럼 지연 평가를 사용해 필요한 데이터를 필요한 순간에 생성하므로 빠른 속도와 불필요한 메모리를 소비하지 않으며, 무한을 표현할 수 있는 장점이 있다.
