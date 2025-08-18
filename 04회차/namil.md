# 4회차 내용 (17장 생성자 함수에 의한 객체 생성, 19장 프로토타입)


# 17. 생성자 함수에 의한 객체 생성

- 객체 리터럴에 의한 객체 생성 방식이 아닌 생성자 함수를 사용하여 객체를 생성하는 방식을 살펴본다.

## 17.1 Object 생성자 함수

- new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다. 빈 객체를 생성한 이후 프로퍼티 또는 메서드를 추가하여 객체를 완성할 수 있다.

```js
// 빈 객체의 생성
const person = new Object();

// 프로퍼티 추가
person.name = "namilc";
person.sayHello = function() {
    console.log("Hello! My name is... " + this.name);
}

console.log(person); // {name: "namilc", sayHello: f}
person.sayHello(); // Hi! My name is namilc
```

- 생성자 함수란 new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수를 말한다. 생성자 함수에 의해 생성된 객체를 **인스턴스**라 한다.

- 자바스크립트는 Object 생성자 함수 이외에도 String, Number, Boolean, Function, Array, Date, RegExp, Promise 등의 빌트인 생성자 함수를 제공

### 17.2 생성자 함수

### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점

- 빈 객체를 생성할 때 반드시 Object 생성자 함수를 사용할 필요는 없고 오히려 객체 리터럴을 사용하는 것이 더 간편하다. "Object 생성자 함수를 사용하는 방식은 **특별한 이유**가 없다면 그닥 유용해 보이지 않는다"는 말이 적혀있다.

- 이렇듯 객체 리터럴 방식이 더 편한데 왜 굳이 Object 생성자 함수를 써야할까? 라는 의문이 들 수 있다. 하지만 객체 리터럴에 의한 객체 생성 방식은 **직관적**이고 **간편**하지만 단 "**하나**"의 **객체**만 생성한다.

> 따라서 동일한 프로퍼티를 갖는 객체를 여러 개 생성해야 하는 경우 매번 같은 프로퍼티를 기술해야 하기 때문에 비효율적이다

```js
const circle1 = {
    radius: 5,
    getDiameter() {
        return 2 * this.radius;
    }
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
    radius: 10,
    getDiameter() {
        return 2 * this.radius;
    }
};

console.log(circle2.getDiameter()); // 20
```

- 객체는 프로퍼티를 통해 객체 고유의 상태를 표현한다. 그리고 메서드를 통해 상태 데이터인 프로퍼티를 참조하고 조작하는 동작을 표현한다.

- 따라서 프로퍼티는 객체마다 프로퍼티 값이 다를 수 있지만 메서드는 내용이 동일한 경우가 일반적이다.

### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

>생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.

=> 약간 얼음을 먹기 위해서 얼음판 틀??에 물을 붓고 얼려서 얼음을 만든다고 이해했다. **얼음판 틀**이라는 **"_템플릿_ "** 에 **얼음**이라는 **"_객체_ "** 를 넣는다는 식으로.

```js
// 생성자 함수
function Circle(radius) {
    // 생성자 함수 내부의 this는 함수가 생성할 인스턴스를 가리킨다.
    this.radius = radius;
    this.getDiameter = function() {
        return 2 * this.radius;
    };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

### `this`

- `this`는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 **자기 참조** 변수. `this`가 가리키는 값, 즉 `this` 바인딩은 함수 호출 방식에 따라 동적으로 결정된다

| 함수 호출 방식 | `this`가 가리키는 값(this 바인딩) |
|--------|------------|
| 일반 함수로서 호출 | `this`가 가리키는 값(this 바인딩)은 전역 객체 |
| 메서드로서 호출 | `this`가 가리키는 값(this 바인딩)은 메서드를 호출한 객체(마침표 앞의 객체) |
| 생성자 함수로서 호출 | `this`가 가리키는 값(this 바인딩)은 생성자 함수가 (미래에) 생성할 인스턴스 |

- 생성자 함수는 이름 그대로 객체(인스턴스)를 생성하는 함수이다.

> 하지만 자바와 같은 클래스 기반 객체 지향 언어의 생성자와는 다르게 그 형식이 정해져 있는 것이 아니라 일반 함수와 동일한 방법으로 생성자 함수를 정의하고 **new 연산자와 함께 호출하면 해당함수는 생성자 함수로 동작한다.**

- new 연산자와 함께 생성자 함수를 호출하지 않으면 일반 함수로 동작

```js
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동직하지 않는다
// 즉, 일반 함수로서 호출
const circle3 = Circle(15);

// 일반 함수로서 호출된 Circle은 반환문이 없으므로 암묵적으로 undefined를 반환
console.log(circle3); // undefind

// 일반 함수로서 호출된 Circle 내의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

### 17.2.3 생성자 함수의 인스턴스 생성 과정

- 생성자 함수의 역할은 프로퍼티 구조가 동일한 인스턴스를 생성하기 위한 템플릿(클래스)으로서 동작하여 **인스턴스를 생성하는 것과 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)** 하는 것이다

```js
// 생성자 함수
function Circle(radius) {
    // 인스턴스 초기화
    this.radius = radius;
    this.getDiameter = function() {
        return 2 * this.radius;
    };
}

// 인스턴스 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
```

- `this`에 프로퍼티를 추가하고 필요에 따라 전달된 인수를 프로퍼티의 초기값으로서 할당하여 인스턴스를 초기화한다. 하지만 인스턴스를 생성하고 반환하는 코드는 보이지 않는다
  - 자바스크립트 엔진은 암묵적인 처리를 통해 인스턴스를 생성하고 반환한다.

- 다음과 같은 과정을 거쳐 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 암묵적으로 인스턴스를 반환한다.

1. 인스턴스 생성과 this 바인딩
   암묵적으로 빈 객체가 생성

   이 빈 객체가 바로 생성자 함수가 생성한 인스턴스

2. 인스턴스 초기화

3. 인스턴스 반환

### 17.2.4 내부 메서드 [[Call]]과 [[Construct]]

- 함수 선언문 또는 함수 표현식으로 정의한 함수는 일반적인 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.

  - 생성자 함수로서 호출한다는 것은 new 연산자와 함께 호출하여 객체를 생성하는 것을 의미한다.

- 함수는 **객체**이므로 일반 객체와 동일하게 동작할 수 있다. 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가지고 있기 때문

```js
// 함수는 객체다. 
// + 객체는 값이다 = 함수는 값이다.
function foo() {}

// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.props = 10;

// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.methods = function() {
    console.log(this.prop);
};

foo.method(); // 10
```

- 함수는 객체이지만 일반 객체와는 다르다. **일반 객체는 호출할 수 없지만 함수는 호출할 수 있다**

- 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드는 물론, 함수로서 동작하기 위해 함수 객체만을 위한 [[Environment]], [[FormalParameters]] 등의 내부 슬롯과 [[Call]], [[Construct]] 같은 메서드를 추가로 가지고 있다.

- 함수가 일반 함수로서 호출되면 함수 객체의 내부 메서드 [[Call]]이 호출되고 new 연산자와 함께 생성자 함수로서 호출되면 내부 메서드 [[Construct]]가 호출된다

```js
function foo() {

    // 일반적인 함수로서 호출: [[Call]]이 호출된다.
    foo();

    // 생성자 함수로서 호출: [[Construct]]가 호출된다.
    new foo();
}
```

**`callable`**

- 내부 메서드 [[Call]]을 갖는 함수 객체
- 호출할 수 있는 객체, 함수

**`constructor`**

- 내부 메서드 [[Contruct]]를 갖는 함수 객체
- 생성자 함수로서 호출할 수 있는 함수

**`non-constructor`**

- [[Construct]]를 갖지 않는 함수 객체
- 생성자 함수로서 호출할 수 없는 함수

### 17.2.5 constructor와 non-constructor의 구분

### 17.2.6 new 연산자

- 일반 함수와 생성자 함수에 특별한 형식 차이는 없다. `new` 연산자와 함께 함수를 호출하면 생성자 함수로 동작한다.

  - 함수 객체의 내부 메서드 [[Call]]이 호출되는 것이 아니라 [[Construct]]가 호출

  - 단, `new` 연산자와 함께 호출하는 함수는 `non-constructor`가 아닌 `constructor`이어야 한다

### 17.2.7 new.target

- 생성자 함수가 `new` 연산자 없이 호출되는 것을 방지하기 위해 ES6에서 `new.target`을 지원한다
  - `new.target`은 `this`와 유사하게 `constructor`인 모든 함수 내부에서 암묵적인 지역 변수와 같이 사용되면 메타 프로퍼티라고 부른다.
  - 함수 내부에서 `new.target`을 사용하면 `new` 연산자와 함께 생성자 함수로서 호출되었는지 확인할 수 있다.

>**`new` 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 `new.target`은 함수 자신을 가리킨다. `new` 연산자 없이 일반 함수로서 호출된 함수 내부의 `new.target`은 "undefind"이다**

=> 따라서 함수 내부에서 `new.target`을 사용하여 `new` 연산자와 생성자 함수로서 호출했는지 확인하여 그렇지 않은 경우 `new` 연산자와 함께 재귀 호출을 통해 생성자 함수로서 호출할 수 있다.


## 19. 프로토타입

💡 객체지향에서의 객체는 **현실에 있는 것을 추상화** 한 것이며 현실의 것을 코드로 옮기는것이 아니다.

**1. 추상이란 ?**

> 사물이 지니고 있는 여러 측면 중 **특정한 부분만**을 간추려 보는것 ⇒ 그외의 필요없는 부분은 버린다

**2. 객체란 ?**

> 속성을 통해 여러개의 값을 하나의 단위로 구성한 복합적인 자료구조

## 19.1 객체지향 프로그래밍

💡 독립적인 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임 즉, **객체 위주로 설계하고 프로그래밍 하는 패러다임**

- 객체 지향 언어 에서의 추상화 최소 단위는 **객체**

**| 객체지향 프로그래밍의 상태와 동작**

- 객체의 **"상태 `state` ( Property 프로퍼티 )"** 를 나타내는 데이터와

  상태 데이터를 조작할 수 있는 **"동작 ( Method 메소드)"** 을 하나의 논리적인 단위로 묶음

  ```jsx
  // 원을 표현한 개념의 객체
  const circle = {
    radius: 10, // 반지름을 나타내는 상태 state

    getDiameter() {
      // 반지름을 조작해 지름을 구하는 동작
      return 2 * this.radius;
    },
  };
  ```

**그래서** 객체지향 프로그래밍은?

=> 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 **복합적인 자료구조**

- 객체는 고유의 기능을 갖는 독립적 부품으로 볼수도 있지만 **다른 객체와 관계성**을 가질 수 있음
  - 다른 객체의 상태 / 동작을 상속 받을 수 있음

**오해**

- 객체 지향은 **패러다임일 뿐** 언어와는 무관

  - 단지 언어는 이런 지향을 좀 더 편하게 구현하도록 도와줌
  - 클래스가 없는 JS / Go / C 언어로 객체지향 프로그래밍은 가능!
  - JS의 경우 Prototype을 통해 객체 지향을 표현

- **무조건** 객체지향이 절차 지향보다 좋은것은 아님

## 19.3 프로토타입 객체

💡 객체지향 프로그래밍의 근간을 이루는 **객체 간 상속을 구현하기 위해** 사용된다.

- JavaScript 에서는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거할 수 있다.

JS 에서의 모든 객체는 **[ [ Prototype ] ] 이라는 내부 슬롯**을 가진다.

- 만약 `prototype`이 없다면 `null` 이 할당되어진다.
- 객체가 **생성되어지는 방식에 따라** 프로토타입이 결정되고 내부슬롯에 저장된다.
- 일반적인 객체의 내부슬롯은 `Object.prototype` 을 가리킨다.

**사용 이유 예시**

```js
function Test(value) {
  this.value = value;
  this.double = () => {
    return this.value * 2;
  };
}

const test1 = new Test(5);
const test2 = new Test(10);

console.log(test1.double === test2.double); // false
console.log(test1); // Test { value: 5, double: [Function (anonymous)] }
console.log(test2); // Test { value: 10, double: [Function (anonymous)] }
```

생성자 함수를 이용해 2개의 값을 만든 상황이다.

- `console.log` 출력시 두 개 다 각자의 `value` 라는 상태는 인스턴스 마다 다른 상황이 많고 **각자의 객체에 표현**되는것이 맞다.

>하지만 `Double` 이라는 함수는 모든 인스턴스마다 가지고 있으며 사용할 필요가 없다.

=====> **메모리 낭비가 심함**

```js
function Test(value) {
  this.value = value;
  Test.prototype.double = function () {
    return this.value * 2;
  };
}
const test1 = new Test(10);
const test2 = new Test(5);

// 이전과 다르게 각자의 객체가 하나의 Method 를 사용하기 때문에 true
console.log(test1.double === test2.double); // ture
console.log(test1); // Test { value: 10 }
console.log(test1.double()); // 20
console.log(test2.double()); // 10
```

- **JavaScript 에서 `prototype` 으로 상속을 구현한다면?**

  - 위와 같이 구현시 상위 객체 역할을 하는 `Test.prototype`의 **모든 메서드 와 프로퍼티를 상속받음**


**\_ _ proto _ \_**  (접근자 프로퍼티)

- 를 통해 객체의 **프로토타입 / 내부슬롯을 접근** 할 수 있다.

- 크롬에 객체 생성후 해당 객체 출력시 확인 가능

- **객체가 직접 소유하고 있지 않고** `object.prototype` 의 프로퍼티이다.

---

**`constructor` : 제작자 / 건설하다**

- `prototype` 프로퍼티로 **자신을 참조하고 있는** 생성자 함수를 가리킨다.

- `Person` 생성자 함수로 `me` 객체를 생성한다면 `me` 객체는 프로토타입의 `constructor` 프로퍼티를 통해 생성자 함수와 연결 된다.

---

💡 프로토타입과 생성자 함수는 단독으로 존재 할 수 없고 **언제나 쌍** 으로 존재한다.  
 또 프로토타입은 **생성자 함수가 생성되는 시점**에 더불어 생성된다.

그리고 **빌트인 생성자 함수**들 또한  
생성자 함수 / 리터럴 표기법으로 객체가 생성되면  
생성된 객체의 **내부슬롯에 프로토타입이 할당**됨

### 19.7 프로토타입 체인

---

- JavaScript가 객체의 프로퍼티에 접근 하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면?
  - 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.

이것이 **프로토타입 체인**

- 상속와 프로퍼티 검색을 위한 메커니즘
  - 스코프 체인은 식별자 검색을 위한 메커니즘
  - 따라서 두개의 두개의 체인은 서로 **협력하여 식별자와 프로퍼티를 검색**한다.

💡 So, 프로토타입 체인의 최상위 위치는 언제나 `object.prototype` 이고,  
**모든 객체는 `object.prototype` 을 상속** 받는다. 이것을 **프로토타입 체인의 종점** 이라한다.

### 19.8 오버라이딩 `overriding` / 오버로딩 `overloading` / 프로퍼티 섀도잉

---

💡 오버라이딩?  
- 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재 정의하여 사용한 방식

```jsx
function Test(value) {
  this.value = value;
  this.tsetName = value + "name";
  Test.prototype.double = () => this.value * 4;
}

const test1 = new Test(10);

test1.double = function () {
  return this.value * 2;
};

console.log(test1.double()); // 20

delete test1.double;

console.log(test1.double()); //40
```

위에서와 같이 생성자 함수 내부의 `double` 프로토타입 메서드가 존재하지만 인스턴스를 추가한 후 같은 인스턴스 메서드를 추가한 상황

- 이때 인스턴스 메서드가 생성자 프로토타입 메소드를 **덮어쓰는것이 아니다.**
- 오버라이딩 하게되고 프로토타입 **메서드는 가려지게 된다.**
- 이런 프로퍼티가 가려지는 현상을 **프로퍼티 섀도잉 (property shadowing)** 이라한다.

> 위의 예시와 같이 `delete` 사용시 **다시 프로토타입 메소드를 사용**하게 된다.

## 19.10 instanceof 연산자

- 이항 연산자로 우변의 생성자 함수의 `prototype`에 바인딩된 객체가 좌변의 객체의 `prototype` 체인 상에 존재한다면 `true` 아니라면 `false`를 반환한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('namilc');

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```
- 이처럼 생성자 함수의 `prototype`에 바인딩 된 객체가 프로토타입 체인 상에 존재하는지 확인한다.

---

`instanceof` 연산자를 함수와 표현하면 다음과 같다.

```js
function isInstanceof(instance, constructor) {
  // 프로토타입 취득
  const prototype = Object.getPrototypeOf(instance);

  // 재귀 탈출 조건
  // prototype이 null이면 프로토타입 체인의 종점에 다다른 것이다.
  if (prototype === null) return false;

  // 프로토타입이 생성자 함수의 prototype 프로퍼티에 바인딩된 객체라면 true를 반환한다.
  // 그렇지 않다면 재귀 호출로 프로토타입 체인 상의 상위 프로토타입으로 이동하여 확인한다.
  return prototype === constructor.prototype || isInstanceof(prototype, constructor);
}

console.log(isInstanceof(me, Person)); // true
console.log(isInstanceof(me, Object)); // true
console.log(isInstanceof(me, Array));  // false
```

> 따라서 생성자 함수에 의해 프로토타입이 교체되어 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 생성자 함수의 `prototype` 프로퍼티와 프로토타입 간의 연결은 파괴되지 않으므로 `instanceof`는 아무런 영향을 받지 않는다.

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hello world! My name is... ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('namilc');

// constructor 프로퍼티와 생성자 함수 간의 연결은 파괴되어도 instanceof는 아무런 영향을 받지 않는다.
console.log(me.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

### 19.11 직접 상속

---

### 19.11.1 Object.create에 의한 직접 상속

- `Object.create` 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다. 그와 동시에 추상 연산을 호출한다.  

- `Object.create`메서드로 객체를 생성할 때의 장점 :

  - new연산자 없이도 객체 생성
  - 프로토타입을 지정하면서 객체 생성
  - 객체 리터럴에 의해 생성된 객체도 상속 가능

```js
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true }
});
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// 임의의 객체를 직접 상속받는다.
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
obj = Object.create(Person.prototype);
obj.name = 'Lee';
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

 `Object.prototype`의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않는다

=> `Object.create`메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다.

< | | (`Obejct.create(null);`로 생성하는 경우) | | >

```js
// 프로토타입이 null인 객체, 즉 프로토타입 체인의 종점에 위치하는 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true

// obj는 Object.prototype의 빌트인 메서드를 사용할 수 없다.
console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function
// 프로토타입이 null인 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function

// Object.prototype의 빌트인 메서드는 객체로 직접 호출하지 않는다.
console.log(Object.prototype.hasOwnProperty.call(obj, 'a')); // true
```

### 19.11.2 객체 리터럴 내부에서 \_ _proto \_ _에 의한 직접 상속

- ES6에서는 객체 리터럴 내부에서 __proto__ 접근자 프로퍼티를 사용해 직접 상속을 구현할 수 있다.

```js
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

### 19.12 정적 프로퍼티/메서드

- 정적(static) 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staticMethod = function () {
  console.log('staticMethod');
};

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

- 정적 프로퍼티/메서드는 생성자 함수가 아닌 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다. 그 이유는 인스턴스의 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니기 때문이다.

- 참고로 앞서 살펴본 `Object.create` 메서드는 `Object` 생성자 함수의 정적 메서드고 `Object`.`prototype.hasOwnProperty` 메서드는 `Object.prototype`의 메서드다.

- 인스턴스가 호출한 인스턴스/프로토타입 메서드 내에서 `this`는 인스턴스를 가리킨다. 이 `this`를 사용하지 않는다면, 즉 인스턴스를 참조할 필요가 없다면 정적 메서드로 변경하여도 동작한다.

```js
// Object.create는 정적 메서드다.
const obj = Object.create({ name: 'Lee' });

// Object.prototype.hasOwnProperty는 프로토타입 메서드다.
obj.hasOwnProperty('name'); // -> false
function Foo() {}

// 프로토타입 메서드
// this를 참조하지 않는 프로토타입 메소드는 정적 메서드로 변경해도 동일한 효과를 얻을 수 있다.
Foo.prototype.x = function () {
  console.log('x');
};

const foo = new Foo();
// 프로토타입 메서드를 호출하려면 인스턴스를 생성해야 한다.
foo.x(); // x

// 정적 메서드
Foo.x = function () {
  console.log('x');
};

// 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.
Foo.x(); // x
```

### 19.13 프로퍼티 존재 확인

### 19.13.1 in 연산자

---

- in 연산자는 객체 내의 특정 프로퍼티가 존재하는지 여부를 확인한다.

> 단, 확인 대상 객체의 프로퍼티뿐만 아니라 그 객체가 속한 프로토타입 체인을 모두 확인한다.


```js
const person = {
  name: 'namilc',
  address: 'Incheon'
};

// person 객체에 name 프로퍼티가 존재한다.
console.log('name' in person);    // true
// person 객체에 address 프로퍼티가 존재한다.
console.log('address' in person); // true
// person 객체에 age 프로퍼티가 존재하지 않는다.
console.log('age' in person);     // false
console.log('toString' in person); // true
```

- `Object.prototype` 에 `toString` 메서드가 존재하기 때문에 `true`다.
in대신 ES6에서 도입된 `Reflect.has`메서드도 사용할 수 있다. in과 동일하게 동작한다.

```js
const person = { name: 'namilc' };

console.log(Reflect.has(person, 'name'));     // true
console.log(Reflect.has(person, 'toString')); // true
```

### 19.13.2 Object.prototype.hasOwnProperty 메서드

- `Object.prototype.hasOwnProperty` 메서드를 사용해도 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.
  - 메서드의 이름에서도 확인할 수 있듯이 객체 고유의 프로퍼티 키가 아닌 상속받은 프로토타입의 프로퍼티 키인 경우 `false`를 반환한다.

```js
const person = { name: 'namilc' };

console.log(person.hasOwnProperty('name')); // true
console.log(person.hasOwnProperty('age'));  // false
console.log(person.hasOwnProperty('toString')); // false
```

---

### 19.14 프로퍼티 열거

### 19.14.1 for … in 문

---

- 객체의 모든 프로퍼티를 순회하며 열거(enumeration)하려면 `for...in`문을 사용한다.

  - 프로퍼티 키를 순회한다.
  - 프로토타입 체인을 모두 순회한다.
  - [[Enumerable]]값을 참조한다.
  - `Symbol`을 열거하지 않는다.
  - 순서를 보장하지 않는다.(하지만 모던 브라우저는 순서를 보장한다.)

```js
const person = {
  name: 'namilc',
  address: 'Incheon'
};

// in 연산자는 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인한다.
console.log('toString' in person); // true

// for...in 문도 객체가 상속받은 모든 프로토타입의 프로퍼티를 열거한다.
// 하지만 toString과 같은 Object.prototype의 프로퍼티가 열거되지 않는다.
// for...in 문의 변수 key에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: namilc
// address: Incheon
```

>객체를 순회하며 `key` 변수에 프로퍼티 키를 할당한다.
>
>`for...in` 문은 `in` 연산자 처럼 상속받은 프로토타입의 프로퍼티까지 열거한다. 하지만 위의 >예제에서는 `toString`과 같은 `Object.prototype`의 프로퍼티가 열거되지 않는다.
>
>그 이유는 `Object.prototype.string` 프로퍼티의 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 >`false`이기 때문이다.

따라서 `for...in` 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 true인 프로퍼티를 순회하며 열거한다.

```js
const person = {
  name: 'namilc',
  address: 'Incheon',
  __proto__: { age: 23 }
};

for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: namilc
// address: Incheon
// age: 23
```

- `for...in` 문은 프로퍼티가 Symbol인 프로퍼티는 열거하지 않는다.

```js
const sym = Symbol();
const obj = {
  a: 1,
  [sym]: 10
};

for (const key in obj) {
  console.log(key + ': ' + obj[key]);
}
// a: 1
```

- 상속받은 프로퍼티가 아닌 객체 자신의 프로퍼티만 열거하려면 `Object.prototype.hasOwnProperty` 메서드를 사용하여 객체 자신의 프로퍼티인지 확인해야 한다.

```js
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

for (const key in person) {
  // 객체 자신의 프로퍼티인지 확인한다.
  if (!person.hasOwnProperty(key)) continue;
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

- `for...in` 문은 프로퍼티를 열거할 때 순서를 보장하지 않으므로 주의하자.

- 하지만 대부분의 모던 브라우저는 순서를 보장하고 숫자인 문자열의 프로퍼티 키에 대해서는 정렬을 실시한다

```js
const obj = {
  2: 2,
  3: 3,
  1: 1,
  b: 'b',
  a: 'a'
};

for (const key in obj) {
  if (!obj.hasOwnProperty(key)) continue;
  console.log(key + ': ' + obj[key]);
}

/*
1: 1
2: 2
3: 3
b: b
a: a
*/
```

- 추가적으로 배열에는 `for...in` 문을 사용하지 말고 일반적인 `for` 문이나 `for...of` 문
또는 `Array.prototype.forEach` 메서드를 사용하기를 권장한다.

```js
const arr = [1, 2, 3];
arr.x = 10; // 배열도 객체이므로 프로퍼티를 가질 수 있다.

for (const i in arr) {
  // 프로퍼티 x도 출력된다.
  console.log(arr[i]); // 1 2 3 10
};

// arr.length는 3이다.
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // 1 2 3
}

// forEach 메서드는 요소가 아닌 프로퍼티는 제외한다.
arr.forEach(v => console.log(v)); // 1 2 3

// for...of는 변수 선언문에서 선언한 변수에 키가 아닌 값을 할당한다.
for (const value of arr) {
  console.log(value); // 1 2 3
};
```

### 19.14.2 Object.keys/values/entries 메서드

- 객체 자신의 고유 프로퍼티만 열거하기 위해서는 `for...in` 문을 사용하는 것보다 `Object.keys/valuse/entries` 메서드를 사용하는 것을 권장한다. `Object.keys` 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.

```js
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

console.log(Object.keys(person)); // ["name", "address"]
```

- ES8에 도입된 `Object.values` 메서드는 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환하고,

- ES8에 도입된 `Object.entries` 메서드는 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환한다.

```js
console.log(Object.values(person)); // ["Lee", "Seoul"]
console.log(Object.entries(person)); // [["name", "Lee"], ["address", "Seoul"]]

Object.entries(person).forEach(([key, value]) => console.log(key, value));
/*
name Lee
address Seoul
*/
```
