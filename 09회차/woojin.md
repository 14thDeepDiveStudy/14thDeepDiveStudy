# JS DeepDive

## 25.클래스

### JS에서의 클래스란?

ES6에서 도입된 객체 생성 템플릿이다.

기존 프로토타입 기반 객체지향 프로그래밍보다 클래스 기반 객체지향 프로그래밍 언어(Java, C# 등)와 매우 흡사한 새로운 객체 생성 매커니즘이다.

> **프로토타입 기반 상속**을 더 직관적이고 문법적으로 깔끔하게 표현한 **새로운 객체 생성 매커니즘**

#### 생성자 함수와 클래스의 차이

객체 생성 매커니즘이면 js에는 이미 생성자 함수가 있으며, 생성자 함수와 클래스의 차이는 다음과 같다.

| 구분       | 클래스                                                                                       | 생성자 함수                                                                                   |
| ---------- | -------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| new 연산자 | new 연산자 없이 호출하면 **에러**                                                            | new 연산자 없이 호출하면 **일반 함수로 호출**                                                 |
| 키워드     | 상속을 지원하는 extends와 super를 제공                                                       | 해당 키워드 제공❌                                                                            |
| 호이스팅   | 호이스팅이 **발생하지 않는 것** 처럼 동작                                                    | 함수 선언문으로 생성되면 함수 호이스팅, 함수 표현식으로 생성되면 변수 호이스팅                |
| 엄격모드   | 모든 코드에 암묵적으로 strict mode가 지정되어 실행되며, 해제 불가능                          | 지정되지 않음                                                                                 |
| 열거 여부  | constructor, 프로토타입 메서드, 정적 메서드 모두 `[[Eunmerable]]`의 값이 false라 열거 불가능 | 생성자 함수 자체의 내장 프로퍼티 => 열거 불가능 / 인스턴스에 직접 정의한 프로퍼티 → 열거 가능 |

> 즉, 클래스는 생성자 함수 기반의 객체 생성 방식보다 견고하고 명로함

---

### 클래스 정의

`class`키워드를 통해 정의한다.

- 이름은 파스칼 케이스를 사용하는 것이 일반적이나, 사용하지 않아도 에러가 발생하지 않는다.
- 함수 표현식으로 정의할 수 있으며, 익명,기명함수 둘 다 가능하다

```jsx
// 클래스 선언문
class ClassObject {}

// 익명 클래스 표현식
const AnonymousClass = class {};

// 기명 클래스 표현식
const RegisteredClass = class NamedClass {};
```

클래스를 표현식으로 정의할 수 있다??
=> 클래스는 값으로 사용할 수 있다
=> 값으로 사용할 수 있다??
=> 클래스는 일급 객체(함수)다

#### 일급 객체로서 클래스의 특징

- 무명의 리터럴로 생성 가능 > 런타임에 생성 가능
- 변수나 자료구조(객체, 배열 등)에 저장 가능
- 함수의 매개변수에 전달 가능
- 함수의 반환값으로 사용 가능

---

### 클래스의 호이스팅

클래스는 함수로 평가된다.

```jsx
// 클래스 선언문
class ClassObject {}

console.log(typeof ClassObject); //function
```

1. 클래스 선언문으로 정의한 클래스는 함수기 때문에 런타임 이전에 먼저 평가되어 함수 객체를 생성한다.
2. 클래스가 평가되어 생성된 함수 객체는 `constructor`이다.(생성자 함수로 호출할 수 있어야 되기 때문)
3. 생성자 함수로서 호출할 수 있는 함수는 함수 객체를 생성하는 시점에 프로토타입과 함께 생성된다.(프로토타입과 생성자함수는 **쌍**으로 존재하기 때문)

단, 클래스는 클래스 정의 이전에 참조할 수 없다.

위에 말했듯 클래서 선언문은 호이스팅이 발생하지 않는 것 처럼 보인다고 했다. 즉 호이스팅이 이뤄지나 그렇게 안보인다는 뜻이다.

```jsx
const SomeClass = "";

{
  // 호이스팅이 발생하지 않으면 ""이 출력되어야 한다.
  console.log(SomeClass);
  // ReferenceError: Cannot access 'SomeClass' before initialization

  // 클래스 선언문
  class SomeClass {}
}
```

클래스 선언문도 호이스팅이 발생하지만, let, const처럼 호이스팅이 된다.
즉, 클래스 선언문 이전에 TDZ(일시적 사각지대)에 빠지기 때문에 호이스팅이 발생하지 않는 것 처럼 동작한다.

> 모든 선언문은 런타임 이전에 먼저 실행되기 때문에 호이스팅이 된다.(var, let, const, function, function\*, class)

---

### 인스턴스 생성

클래스는 기본적으로 생성자 함수이며 new연산자와 함께 호출되어 인스턴스를 생성한다.

```jsx
class SomeClass {}

// 인스턴스 생성
const SomeInstance = new SomeClass();
console.log(SomeInstance); // SomeClass{}
```

클래스는 생성자 함수와 달리 **인스턴스를 생성하는 것**이 유일한 목적이므로 반드시 **new연산자와 함께 호출**한다.

```jsx
class SomeClass {}

const me = SomeClass();
// TypeError: Class constructor SomeClass cannot be invoked without 'new'
```

클래스 표현식으로 정의된 클래스의 경우, 클래스를 가리키는 식별자를 사용해 **인스턴스를 생성하지 않고** 기명 클래스 표현식의 클래스 이름을 사용해 인스턴스를 생성하면 에러가 발생한다.

```jsx
const SomeObject = class SomeClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new SomeObject();

// 클래스 이름 SomeClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
console.log(SomeClass); // ReferenceError: SomeClass is not defined

const you = new SomeClass(); // ReferenceError: SomeClass is not defined
```

이는 기명 함수 표현식과 마찬가지로 클래스 표현식에서 사용한 클래스 이름은 **외부 코드에서 접근 불가능**하기 때문이다.

---

### 클래스의 메서드

클래스의 몸체에는 0개 이상의 메서드만 정의할 수 있다.
클래스 몸체에서 정의할 수 있는 메서드는 `cosntructor(생성자)`, `프로토타입 메서드`, `정적 메서드`로 3가지가 있다.

```jsx
// 클래스 선언문
class SomeClass {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // name의 프로퍼티는 Public하다
  }

  // 프로토타입 메서드
  saySomething() {
    console.log(`안녕! 나는 ${this.name}이야`);
  }

  // 정적 메서드
  static sayAnything() {
    console.log("하나둘셋넷");
  }
}

// 인스턴스 생성
const SomeInstance = new SomeClass("우리둥절");

// 인스턴스의 프로퍼티 참조
console.log(SomeInstance.name); // 우리둥절
// 프로토타입 메서드 호출
SomeInstance.saySomething(); // 안녕! 나는 우리둥절이야
// 정적 메서드 호출
SomeClass.sayAnything(); // 하나둘셋넷
```

#### cosntructor(생성자)

`cosntructor`는 인스턴스를 생성하고 초기화하기 위한 특수 메서드이다.
`cosntructor`는 이름을 변경할 수 없다.

```jsx
class SomeClass {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}
```

앞서 언급했듯, 클래스는 평가되어 함수 객체가 된다.
이는 클래스도 함수 객체 고유의 프로퍼티를 가지며, 프로토타입과 연결돼 자신의 스코프 체인을 구성한다.

> prototype 프로퍼티가 가리키는 프로토타입의 객체의 `constructor` 프로퍼티는 클래스 자신을 가리킴
> 이는 클래스가 **인스턴스를 생성하는 생성자 함수라는 것**을 의미
> 즉, **new연산자와 함꼐 클래스를 호출하면 클래스는 인스턴스를 생성**

생성자 함수와 마찬가지로 `constructor` 내부에서 `this`에 추가한 프로퍼티는 **인스턴스 프로퍼티**가 된다.
`constructor` 내부의 `this`는 생성자 함수와 마찬가지로 **클래스가 생성한 인스턴스를 가리킨다.**

`constructor`는 메서드로 해석되는 것이 아니라, 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다.

> 즉, 클래스 정의가 평가되면 `constructor`의 **기술된 동작을 하는 함수 객체가 생성**된다.

`constructor`와 생성자 함수는 유사하지만 몇 가지 차이가 있다.

- `constructor`는 클래스 내에 최대 한 개만 존재 가능하며, 2개 이상이면 문법 에러가 발생
- `constructor`는 생략 가능하며, 생략 시 클래스에 빈 `constructor`가 암묵적으로 정의(빈 객체를 생성)
- 프로퍼티가 추가되어 초기화된 인스턴스를 생성하려면 `constructor` 내부에 `this`에 인스턴스 프로퍼티를 생성
- 인스턴스를 생성할 때 클래스 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 `constructor`에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달(초기값은 `constructor`의 매개변수에 전달됨)
  - 인스턴스를 초기화하려면 `constructor`를 생략하면 안됨
- `constructor`는 반환문을 갖지 않아야 됨(암묵적으로 인스턴스(`this`)를 반환하기 때문)
  - 반환값을 명시하면 인스턴스(`this`)를 반환하지 않고 명시된 객체를 반환
  - 원시값을 반환하면 암묵적으로 인스턴스(`this`)가 반환

#### 프로토타입 메서드

클래스 몸체에서 정의한 메서드는 생성자 함수와 달리, 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.

```jsx
// 클래스 선언문
class SomeClass {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  saySomething() {
    console.log(`안녕! 나는 ${this.name}이야`);
  }
}

// 인스턴스 생성
const SomeInstance = new SomeClass("우리둥절");

// 프로토타입 메서드 호출
SomeInstance.saySomething(); // 안녕! 나는 우리둥절이야
```

생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다. (프로토타입 메서드 상속 가능)

> 프로토타입 체인은 기존의 모든 객체 생성 방식뿐만 아니라, 클래스에 의해 생성된 인스턴스에도 동일하게 적용
> 클래스는 생성자 함수와 같이 인스턴스를 생성하는 생성자 함수다.
> 즉, 클래스는 생성자 함수와 마찬가지로 **프로토타입 기반의 객체 생성 매커니즘**이다.

#### 정적 메서드

정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드다.
클래스에서는 메서드에 `static`키워드를 붙여 정적 메서드(클래스 메서드)가 된다.

```jsx
// 클래스 선언문
class SomeClass {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 정적 메서드
  static sayAnything() {
    console.log("하나둘셋넷");
  }
}
```

정적 메서드는 클래스에 바인딩된 메서드가 된다.
클래스는 함수 객체로 평가되므로, 자신의 프로퍼티/메서드를 소유할 수 있다.
클래스는 클래스 정의가 평가되는 시점에 함수 객체가 되므로, 인스턴스와 달리 별다른 생성 과정이 필요없다.

> 정적 메서드는 클래스 정의 이후 **인스턴스를 생성하지 않아도 호출 가능**하며,
> 프로토타입 메서드처럼 인스턴스로 호출하지 않고, **클래스로 호출**

```jsx
// 정적 메서드는 클래스로 호출
// 정적 메서드는 인스턴스 없이도 호출 가능
SomeClass.sayAnything(); // 하나둘셋넷
```

정적 메서드는 **인스턴스로 호출할 수 없다.**
정적 메서드가 바인딩된 클래스는 **인스턴스의 프로토타입 체인 상에 존재하지 않기 때문**에 상속이 되지 않기 때문이다.

```jsx
// 인스턴스 생성
const SomeInstance = new SomeClass("우리둥절");
SomeInstance.sayAnything(); // TypeError: SomeInstance.sayAnything is not a function
```

#### 정적 메서드와 프로토타입 메서드의 차이

두 메서드의 차이는 다음과 같다.

1. 정적 메서드와 프로토타입 메서드는 자신이 **속해 있는 프로토타입 체인이 다름**
2. **정적 메서드는 클래스로 호출, 프로토타입 메서드는 인스턴스로 호출**
3. 프로토타입 메서드는 정적 메서드와 달리 **인스턴스 프로퍼티 참조 가능**

**정적 메서드 사용 예시**

```jsx
class Square {
  // 정적 메서드
  static area(width, height) {
    return width * height;
  }
}

console.log(Square.area(10, 10)); // 100
```

**프로토타입 메서드 사용 예시**

```jsx
class Square {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  // 프로토타입 메서드
  area() {
    return this.width + this.height;
  }
}

const square = new Square(10, 10);
console.log(square.area()); // 100
```

메서드 내부의 `this`는 메서드를 호출한 객체에 바인딩된다.

프로타타입 메서드는 인스턴스로 호출하므로 내부의 `this`는 프로토타입을 호출한 인스턴스를 가리킨다.

정적 메서드는 클래스로 호출하므로 내부의 `this`는 클래스를 가리킨다.

> 내부의 `this`바인딩이 다름

따라서 메서드 내부에서 인스턴스를 참조할 필요가 있다면(`this`사용) **프로토타입 메서드로 정의**하며,
그럴 필요가 없다면 `this`를 사용하지 않고 **정적 메서드로 정의**한다.

#### 클래스에서 정의한 메서드의 특징

- `function` 키워드를 생략한 메서드 축약 표현을 사용
- 객체 리터럴과 다르게 클래스에 메서드를 정의할 때는 `,`가 필요없음
- 암묵적으로 엄격모드(strict mode) 실행
- `for...in`문이나 `Object.keys` 메서드 등으로 열거 불가능
  - 프로퍼티 열거 가능 여부를 나타내며, 논리형 값을 갖는 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`
- 내부 메서드 `[[Construct]]`를 갖지 않는 `non-constructor`다
  - new연산자와 함께 호출 ❌

---

### 클래스의 인스턴스 생성 과정

- new연산자와 함께 클래스를 호출하면 클래스의 내부 메서드 `[[Construct]]`가 호출(클래스는 new연산자 없이 호출 불가능)
- 생성자함수와 유사한 방식으로 인스턴스가 생성

#### 1. 인스턴스 생성과 this 바인딩

1. new 연산자와 함께 클래시 호출 시, `constructor`의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체 생성
2. 이때 생긴 빈 객체 === 인스턴스
3. 클래스가 생성한 인스턴스의 프로토타입으로 클래스의 `prototype` 프로퍼티가 가리키는 객체가 설정
4. 인스턴스가 this에 바인딩(`constructor` 내부의 `this`는 클래스가 생성한 인스턴스를 가리킴)

#### 2. 인스턴스 초기화

1. `constructor` 내부 코드가 실행되어 `this`에 바인딩된 인스턴스를 초기화
   1.1 `this`에 바인딩된 인스턴스에 프로퍼티를 추가하고 `constructor`가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티 값 초기화
   1.2 `constructor`가 생략되면 이 과정도 생략

#### 3. 인스턴스 반환

1. 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환

```jsx
class SomeClass {
  // 생성자
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩
    console.log(this); // SomeClass {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩된 인스턴스를 초기화
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적 반환
  }
}
```

---

### 프로퍼티

#### 인스턴스 프로퍼티

인스턴스 프로퍼티는 `constructor` 내부에서 정의해야 한다.

```jsx
class SomeClass {
  constructor(name) {
    // 인스턴스 프로퍼티
    this.name = name; // name 프로퍼티는 public하다
  }
}

// 인스턴스 생성
const SomeInstance = new SomeClass("우리둥절");

console.log(SomeInstance); // SomeClass {name : "우리둥절"}
// name은 public하다
console.log(SomeInstance.name); // 우리둥절
```

1. `constructor` 내부 코드가 실행되기 이전에 `constructor` 내부의 `this`에 이미 클래스가 암묵적으로 생성한 인스턴스(빈 객체)가 바인딩
2. `constructor` 내부에서 `this`에 인스턴스 프로퍼티를 추가
3. 클래스의 인스턴스(암묵적으로 생성한 빈 객체)에 프로퍼티가 추가되어 인스턴스가 초기화

`constructor` 내부에서 `this`에 추가한 프로퍼티는 언제나 클래스가 생성한 인스턴스의 프로퍼티가 된다.
ES6의 클래스는 다른 객체지향 언어처럼 `private`, `public`, `protected` 키워드와 같은 **접근 제한자를 지원하지 않는다.**

> 인스턴스 프로퍼티는 **언제나 `public`**

#### 접근자 프로퍼티

접근자 프로퍼티는 자체적으로는 값(내부슬롯 `[[Value]]`)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 **접근자 함수로 구성**된 프로퍼티다.

이 접근자 프로퍼티를 클래스에서 사용하면 다음과 같다.

```jsx
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티이다.
  //  getter 함수 (특정 프로퍼티를 읽을 때 실행되는 함수 > 반환값이 프로퍼티 값)
  // 메서드처럼 동작하지만, 호출할 때는 괄호 없이 프로퍼티처럼 접근 가능
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // setter 함수 (특정 프로퍼티에 값을 대입할 때 실행되는 함수 > 전달된 값을 가공해서 내부 상태를 변경)
  // 전달된 값을 받아 내부 상태를 업데이트하는 용도로 사용
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  }
}

const me = new Person("우진", "쓰");

// 데이터 프로퍼티를 통한 프로퍼티 값 참조
console.log(`${me.firstName}${me.lastName}`); // 우진쓰

console.log(me); // Person{firstName: "우진", lastName: "쓰"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출
me.fullName = "우리둥절";

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수 호출
console.log(me.fullName); // 우리둥절

// fullName은 접근자 프로퍼티
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다
console.log(Object.getOwnPropertyDescriptor(Person.prototype, "fullName"));
// {get: f, set: f, enumerable: false, configurable: true}
```

접근자 프로퍼티는 자체적으로 값을 갖지 않고 **다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용**하는 접근자 함수(getter, setter 함수)로 구성되어 있다.

- **getter()**:

  - 인스턴스 프로퍼티에 접근할 때마다 프로퍼티 값을 조작하거나, 별도의 행위가 필요할 때 사용
  - 메서드 이름 앞에 `get` 키워드를 사용해 정의
  - 반환값 필수

- **setter()**:

  - 인스턴스 프로퍼티에 값을 할당할 때마다 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용
  - 메서드 이름 앞에 `set` 키워드를 사용해 정의
  - 매개변수 필수(하나의 매개변수만 선언 가능)

- getter(), setter() 이름은 인스턴스 프로퍼티처럼 사용

> 즉, 호출하는 것이 아니라 프로퍼티처럼 참조/할당하는 형식으로 사용되며, 참조/할당 시 내부적으로 호출된다.

클래스의 메서드는 기본적으로 프로토타입 메서드가 되며, 접근자 프로퍼티 또한 프로토타입의 프로퍼티가 된다.

#### 클래스 필드 정의 제안

**클래스필드란?**
클래스 멤버라고도 하며, 클래스 기반 객체지향 언어에서 **클래스가 생성할 인스턴스의 프로퍼티를 가리키는** 용어다.

> JAVA와 JavaScript의 클래스 필드 차이
>
> | 구분                            | JavaScript                                   | JAVA                                    |
> | ------------------------------- | -------------------------------------------- | --------------------------------------- |
> | 인스턴스 프로퍼티 선언 / 초기화 | `constructor`내부에서 `this`에 프로퍼티 추가 | 변수처럼 클래스 몸체에 `this` 없이 선언 |
> | 인스턴스 참조                   | `this`를 사용해 참조                         | `this`없이 참조 가능                    |

- 클래스 몸체에 클래스 필드를 정의하는 경우 `this`에 클래스 필드 바인딩 ❌
  - `this`는 클래스의 `constructor`와 메서드에서만 유효
- 클래스 필드를 참조하는 경우 `this`를 반드시 사용
- 클래스 필드에 초기값을 할당하지 않으면 `undefined`를 가짐
- 인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 `constructor`에서 클래스 필드를 초기화
  - 인스턴스(`this`)에 클래스 필드에 해당하는 프로퍼티가 없다면 자동 추가되기 때문
- 함수 === 일급 객체이므로 클래스 필드에 할당 가능(클래스 필드를 통해 메서드 정의 가능)
  - 이때 메서드는 프로토타입 메서드가 아닌, 인스턴스 메서드(모든 클래스 필드는 인스턴스 프로퍼티기 때문)
  - 권장 ❌
- 클래스 필드에 화살표 함수를 할당해 화살표 함수 내부의 `this`가 인스턴스를 가리키는 방법도 존재
  - 인스턴스가 여러개 생성 시 메모리의 손해가 생김

이로 인해 인스턴스 프로퍼티를 정의하는 방법은 두 가지가 된다.

- 인스턴스 생성 시 외부 초기값으로 클래스 필드를 초기화할 **필요가 있다면**, `constructor`에서 인스턴스 프로퍼티를 정의하는 방식
- 인스턴스 생성 시 외부 초기값으로 클래스 필드를 초기화할 **필요가 없다면**, 기존의 `constructor`에서 인스턴스 프로퍼티를 정의하는 방식 + **클래스 필드 정의 제안 사용 가능**

#### private 필드 정의 제안

JS는 캡슐화를 완전히 지원하지 않는다.
즉, 접근 제한자를 지원하지 않으니 인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있다.

> **언제나 public**

TC39 프로세스의 stage3에서 `private 필드`를 정의할 수 있는 방법이 추가됐다.
`private 필드`를 정의하려면 `private 필드` 선두에 **`#`**을 붙여주며, 참조할 때도 **`#`**을 붙여야 한다.

```jsx
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    // private 필드 참조
    this.#name = name;
  }
}

const me = new Person("우리둥절");

// private 필드 #name은 클래스 외부에서 참조 ❌
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```

> TypeScript 에서는 클래스 기반 객체지향 언어가 지원하는 접근 제한자(public, private, static)를 모두 지원하며, 의미도 동일

- `public 필드`는 **어디서든 참조 가능**하지만, `private 필드`는 **클래스 내부에서만 참조 가능**

| 접근 가능성                 | public | private |
| --------------------------- | ------ | ------- |
| 클래스 내부                 | ✅     | ✅      |
| 자식 클래스 내부            | ✅     | ❌      |
| 클래스 인스턴스를 통한 접근 | ✅     | ❌      |

- 클래스 외부에서 `private 필드`에 직접 접근할 순 없지만, 접근자 프로퍼티를 통해 간접적으로 접근 가능
  - `private 필드`는 클래스 몸체에 정의해야되며, `constructor`에 정의하면 에러 발생

```jsx
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    this.#name = name;
  }

  get name() {
    // private 필드를 참조하여 trim한 다음 반환
    return this.#name.trim();
  }
}

const me = new Person("우리둥절");
console.log(me.name); // 우리둥절
```

#### static 필드 정의 제안

클래스에는 static 키워드를 사용해 정적 메서드를 정의할 수 있다.
하지만 static 키워드로 정적 필드는 정의할 순 없었지만, TC39 프로세스의 stage3를 통해 `static public 필드`, `static privte 필드`, `static privte 메서드`를 정의할 수 있는 표준 사양인 **Static class features**가 제안되었다.

```jsx
class MyMath {
  // static public 필드 정의
  static PI = 22 / 7;

  // static private 필드 정의
  static #num = 10;

  // static 메서드
  static increament() {
    return ++MyMath.#num;
  }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increament()); // 11
```

---

### 상속에 의한 클래스 확장

#### 클래스 상속과 생성자 함수 상속

- 프로토타입 기반 상속은 프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념
- **상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의**

```jsx
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() {
    return "eat";
  }

  move() {
    return "move";
  }
}

// 상속을 통한 Animal 클래스를 확장한 Bird 클래스
class Bird extends Animal {
  fly() {
    return "fly";
  }
}
const bird = new Bird(1, 5);

console.log(bird); // Bird{ age:1. weight: 5}
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); //fly
```

> 상위 클래스의 속성을 상속을 통해 사용할 수 있으며, 자신만의 고유한 속성만 추가해 클래스를 확장 가능

클래스는 상속을 통해 다른 클래스를 확장할 수 있는 문법인 **extends**키워드가 기본적으로 제공된다.
해당 키워드를 사용한 클래스 확장은 간편하고 직관적이다.

- 생성자 함수는 상속을 통해 다른 생성자 함수를 확장하는 문법이 제공 ❌
  - 생성자 함수에서 `의사 클래스 상속` 패턴을 사용해 상속에 의한 클래스 확장을 흉내낼 순 있지만 권장 ❌

#### extends 키워드

상속을 통해 클래스를 확장하려면 `extends` 키워드를 사용해 상속받을 클래스를 정의한다.

```jsx
// 수퍼(베이스/부모) 클래스
class Bass {}

// 서브(파생/자식) 클래스
class Derived extends Base {}
```

- 상속을 통해 확장된 클래스: 서브/파생/자식 클래스
- 서브클래에게 상속된 클래스: 수퍼/베이스/부모 클래스

`extends` 키워드의 역할 : - 수퍼 클래스와 서브 클래스 간의 상속 관계를 설정하는 것 - 프로토타입을 통해 상속 관계 구현

수퍼 클래스와 서브 클래스는 인스턴스의 프로토타입 체인, 클래스간의 프로토타입 체인 생성
즉, **프로토타입 메서드, 정적 메서드 모두 상속 가능**

#### 동적 상속

`extends` 키워드로 생성자 함수를 상속받아 클래스를 확장할 수 있다.
단, **`extends` 키워드 앞에 반드시 클래스가 와야한다.**

`extends` 키워드 다음에는 클래스뿐만 아니라 `[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용 가능하다.
이로 인해 동적으로 상속받을 대상을 결정할 수 있다.

```jsx
function Base1() {}

class Base2 {}

let condition = true;

class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();

console.log(derived); // Derived {}
console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

#### 서브 클래스의 constructor

클래스에서 `constructor`를 생략하면 클래스에 빈 `constructor`가 암묵적으로 정의된다.
서브 클래스에서 `constructor`를 생략하면 클래스에 다음과 같은 `constructor`가 암묵적으로 정의된다.
args는 new 연산자와 함꼐 클래스를 호출할 때 전달한 인수의 리스트다.

```jsx
constructor(...args) { super(...agrs)}
```

super()는 수퍼 클래스의 `constructor(super-constructor)`를 호출해 인스턴스를 생성한다.

예제를 통해 살펴보면 다음과 같다.

```jsx
// 수퍼 클래스
class Bass {]

// 서브 클래스
class Derived extends Bass{}
```

위의 예제는 `constructor`를 생략해 `constructor`가 암묵적으로 정의된다

```jsx
// 수퍼 클래스
class Bass {
  constructor() {}
}

// 서브 클래스
class Derived extends Bass {
  constructor(...args) {
    super(...agrs);
  }
}

const derived = new Derived();
console.log(derived); // Derived()
```

이처럼 수퍼 클래스, 서브 클래스 모두 `constructor`를 생략하면 빈 객체가 생성되며, 프로퍼티를 소유한 인스턴스를 생성하려면 `constructor` 내부의 인스턴스에 프로퍼티를 추가해야된다.

#### super 키워드

`super 키워드`는 함수처럼 호출 가능하며, `this`와 같이 식별자처럼 참조할 수 있다.
`super 키워드`의 동작

- `super`를 호출하면 수퍼 클래스의`constructor(super-constructor)`를 호출
- `super`를 참조하면 수퍼 클래스의 메서드를 호출 가능

**1. super 호출**
**`super`를 호출하면 수퍼 클래스의`constructor(super-constructor)`를 호출**

- 수퍼 클래스의 `constructor` 내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생성한다면 서브 클래스의 `constructor`를 생략 가능
- new 연산자와 함께 서브 클래스를 호출하면서 전달한 인수는 모두 서브 클래스에 암묵적으로 정의된 `constructor`의 `super` 호출을 통해 수퍼 클래스의 `constructor`에 전달

```jsx
// 수퍼 클래스
class Bass {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브 클래스
class Derived extends Bass {
  // 암묵적으로 constructor가 정의
  // constructor(...args) { super(...agrs)}
}

const derived = new Derived(1, 2);
console.log(derived); // Derived {a: 1, b: 2}
```

- 수퍼 클래스에서 추가한 프로퍼티와 서브 클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브 클래스의 `constructor`는 생략 불가
- new 연산자와 함께 서브 클래스를 호출하면서 전달한 인수 중에서 수퍼 클래스의 `constructor`에 전달할 필요가 있는 인수는 서브 클래스의 `constructor`에서 호출하는 `super`를 통해 전달

```jsx
// 수퍼 클래스
class Bass {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브 클래스
class Derived extends Bass {
  constructor(a, b, c) {
    super(a, b);
    this.c = c;
  }
}

const derived = new Derived(1, 2, 3);
console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

1. new연산자와 함께 Derived 클래스 호출
2. 전달한 인수 1,2,3은 Derived 클래스의 constructor에 전달
3. super 호출
4. Bass 클래스의 constructor에 전달

이처럼 인스턴스 초기화를 위해 전달한 인수는 수퍼 클래스, 서브 클래스에 배분되고 상속 관계의 두 클래스는 서로 협력하여 인스턴스를 생성

**super를 호출할 때 주의 사항**

1. 서브 클래스에서 `constructor`를 생략하지 않은 경우, 서브 클래스의 `constructor`에서는 **반드시 `super`를 호출**
2. 서브 클래스의 `constructor`에서 `super`를 호출하기 전에는 **`this` 참조 불가**
3. `super`는 반드시 **서브 클래스의 `constructor`에서만 호출**
   - 서브 클래스가 아닌 클래스의 `constructor`나 함수에서 `super` 출 시 에러 발생

**2. super 참조**
**메서드 내에서 `super`를 참조하면 수퍼 클래스의 메서드를 호출 가능**

1. 서브 클래스의 프로토타입 메서드 내에서 `super.메서드`는 **수퍼 클래스의 프로토 타입 메서드를 가리킴**
   - `super`참조가 동작하기 위해서는 `super`를 참조하고 있는 메서드가 바인딩된 객체의 프로토타입을 찾을 수 있어야하며, 이를 위해 메서드는 내부 슬롯`[[HomeObject]]`를 가지며, 자신을 바인딩하고 있는 객체를 가리킴
   - `[[HomeObject]]`를 가지는 함수만이 `super`참조 가능
2. 서브 클래스의 정적 메서드 내에서 `super.메서드`는 **수퍼 클래스의 정적 메서드를 가리킴**

#### 상속 클래스의 인스턴스 생성 과정

다음 예제를 보며 생성과정을 알아보자

```jsx
// 수퍼 클래스
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }

  toString() {
    return `width = ${this.width}, height = ${this.height}`;
  }
}

// 서브 클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);
    this.color = color;
  }

  // 메서드 오버라이딩
  toString() {
    return super.toString() + `, color = ${this.color}`;
  }
}

const colorRectangle = new ColorRectangle(2, 4, "blue");
console.log(colorRectangle);

// 상속을 통해 getArea 메서드 호출
console.log(colorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드 호출
console.log(colorRectangle.toString()); // width = 2, height = 4, color = blue
```

서브 클래스 ColorRectangle이 new 연산자와 함께 호출되면 다음 과정을 통해 인스턴스를 생성한다.

**1. 서브 클래스의 super 호출**

1. JS엔진이 클래스를 평가할때 수퍼 클래스와 서브 클래스를 구분하기 위해 각 클래스 명을 값으로 갖는 내부 슬롯 `[[ConstructorKind]]`를 가짐
2. 각 클래스의 내부슬롯에 수퍼 클래스와 서브 클래스의 이름이 설정 > new 연산자와 함께 호출되었을때 동작이 구분
3. 상속 받지 않는 수퍼 클래스는 new 연산자와 함께 호출될 때 암묵적으로 빈 객체(인스턴스)를 생성하고 이를 `this` 바인딩
4. **서브 클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼 클래스에게 인스턴스 생성을 위임**
   > 서브 클래스의 `constructor`에서 반드시 `super`를 호출해야 하는 이유
5. 서브 클래스가 new연산자와 함께 호출되면 서브 클래스 `constructor` 내부의 `super`키워드가 함수처럼 호출
6. `super`가 호출되면 수퍼 클래스의 `constructor(super-constructor)` 호출(수퍼 클래스가 평가되어 생성된 함수 객체의 코드 실행)
7. 서브 클래스의 `constructor` 내부에 `super`호출이 없으면 에러 발생
   7.1 인스턴스를 생성하는 주체는 수퍼 클래스이므로 수퍼 클래스의 `constructor`를 호출하는 `super`가 호출되지 않으면 인스턴스를 생성 불가

**2. 수퍼 클래스의 인스턴스 생성과 this 바인딩**

1. 수퍼 클래스의 `constructor` 내부 코드가 실행되기 전에 암묵적으로 빈 객체(클래스가 생성한 인스턴스) 생성
2. 빈 객체(인스턴스)가 `this`에 바인딩
   2.1 수퍼 클래스의 `constructor` 내부의 `this`는 생성된 인스턴스를 가리킴
3. 인스턴스를 생성한건 수퍼 클래스지만, new 연산자와 함께 호출된 클래스는 서브 클래스이므로, **인스턴스는 new.target이 가리키는 서브 클래스가 생성한 것으로 처리**
   > **new연산자와 함께 호출된 함수를 가리키는 new.target의 대상은 서브 클래스**

**3. 수퍼 클래스의 인스턴스 초기화**

1. 수퍼 클래스의 `constructor`가 실행돼 `this`에 바인딩된 인스턴스를 초기화
   1.1 `this`에 바인딩된 인스턴스에 프로퍼티를 추가하고 `constructor`가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화

**4. 서브 클래스의 constructor로의 복귀와 this 바인딩**

1. `super`의 호출이 종료되고 제어 흐름이 서브 클래스 `constructor`로 돌아옴
2. **`super`가 반환한 인스턴스가 `this`가 바인딩**
3. **서브 클래스는 별도의 인스턴스를 생성하지 않고 `super`가 반환한 인스턴스를 `this`에 바인딩하여 그대로 사용**

   > **`super`가 호출되지 않으면 인스턴스가 생성되지 않으며, `this` 바인딩도 불가능** > **이것이 서브 클래스의 `constructor`에서 `super`를 호출하기 전에는 `this`를 참조할 수 없는 이유**

**5. 서브 클래스의 인스턴스 초기화**

1. `super`호출 이후, 서브 클래스의 `constructor`에 기술된 인스턴스 초기화 실행
   1.1 `this`에 바인딩된 인스턴스에 프로퍼티를 추가하고 `constructor`가 받은 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화

**6. 인스턴스 반환**
클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적 반환

#### 표준 빌트인 생성자 함수 확장

앞서 설명했듯 `extends` 키워드 다음에는 클래스뿐만 아니라 `[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용 가능하다.
즉, String, Number, Array 같은 표준 빌트인 객체도 `extends` 키워드를 사용해 확장 가능하다.

```jsx
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 중복된 배열 요소를 제거하고 반환
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균 구하기
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 2, 2, 3, 4);

console.log(myArray); //[1, 2, 2, 3, 4];

// MyArray.prototype.uniq 호출
console.log(myArray.uniq()); //[1, 2, 3, 4]
// MyArray.prototype.average 호출
console.log(myArray.average()); //2.4
```

Array 생성자 함수를 상속받아 확장한 MyArray 클래스가 생성한 인스턴스는 Array.prototype과 MyArray.prototype의 모든 메서드를 사용할 수 있다.

- Array.prototype의 메서드 중 새로운 배열을 반환하는 메서드(map, filter 등..)는 MyArray 클래스의 인스턴스를 반환
- MyArray 클래스의 인스턴스를 반환하지 않고, Array의 인스턴스를 반환하면 MyArray 클래스의 메서드와 메서드 체이닝이 불가능
- 만약 MyArray 클래스의 메서드가 Array의 인스턴스를 반환하게 하려면 Symbol.species를 사용하여 정적 접근자 프로퍼티를 추가
