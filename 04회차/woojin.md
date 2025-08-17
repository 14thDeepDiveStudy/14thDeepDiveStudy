# JS DeepDive

## 17. 생성자 함수에 의한 객체 생성

### 생성자 함수란?

constructor이라고 하며, new연산자와 함께 호출하여 인스턴스를 생성하는 함수

> new연산자가 있다? => 생성자 함수
> new연산자가 없다? => 일반 함수

**인스턴스란? **
생성자 함수에 의해 생성된 객체

```jsx
// String 생성자 함수
const strObj = new String("jeong");

// Number 생성자 함수에 의한 인스턴스 생성
const numObj = new Number(222);

// Boolean 생성자 함수에 의한 인스턴스 생성
const boolObj = new Boolean(true);

// Function 생성자 함수에 의한 인스턴스 생성
const fn = new Function("x", "return x * x");

// Array 생성자 함수에 의한 인스턴스 생성
const arr = new Array(1, 10, 100);

// RegExp 생성자 함수에 의한 인스턴스 생성
const Regexp = new RegExp(/ab+c/i);

// Date 생성자 함수에 의한 인스턴스 생성
const date = new Date();
```

> 객체 리터럴을 사용해 만드는게 더 간편하다.

그럼 객체 리터럴을 사용하면 되는데 왜 생성자 함수를 사용해 객체를 만드는가?

#### 객체 리터럴에 의한 객체 생성의 단점

객체 리터럴로 객체를 만들면 **단 하나**의 객체만 생성한다.
즉, 동일한 프로퍼티를 갖는 객체를 여러개 생성할 경우 매번 같은 메서드와 프로퍼티를 작성해야된다.

#### 생성자 함수에 의한 객체 생성의 장점

**템플릿(클래스)처럼 동일한 구조의 객체들을 간편하게 생성**할 수 있다.

```jsx
// 생성자 함수
function Circle(rad) {
  // 생성자 함수 내부의 this는 인스턴스를 가르킴
  this.radius = rad;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5); // 반지름이 5인 Circle 객체 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체 생성
```

> **this**
> this는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 **자기 참조 변수**다.
> this가 가리키는 값(this 바인딩)은 함수 호출 방식에 따라 동적으로 결정된다.
>
> | 함수 호출 방식       | this 바인딩                   |
> | -------------------- | ----------------------------- |
> | 일반 함수로서 호출   | 전역 객체                     |
> | 메서드로 호출        | 메서드를 호출한 객체          |
> | 생성자 함수로서 호출 | 생성자 함수가 생성할 인스턴스 |

#### 생성자 함수의 인스턴스 생성 과정

생성자 함수의 역할

- 인스턴스를 생성
- 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)

생성자 함수 내부에서는 this를 추가하고, 전달된 인수로 프로퍼티를 초기화한다.
하지만 인스턴스를 생성하고 반환하는 코드는 내부에 **없다**.

> new연산자와 함께 생성자 함수를 호출하면 JS엔진은 암묵적으로 인스턴스를 생성하고 초기화한 후 암묵적으로 반환한다.

반환 과정

1. 인스턴스 생성과 this바인딩
   - 암묵적으로 인스턴스(빈 객체) 생성
   - 인스턴스가 this에 바인딩(생성자 함수에서의 this가 인스턴스를 가리키는 이유)
   - 함수 몸체의 코드가 실행되기 이전에 실행

> **바인딩이란?**
> 식별자와 값을 연결하는 과정을 의미
>
> Ex)

- 변수 선언 = 변수 이름(식별자)과 확보된 메모리 공간의 주소를 바인딩
- this 바인딩 = this와 this가 가리킬 객체를 바인딩

2. 인스턴스 초기화

   - this에 바인딩되어 있는 인스턴스에 프로퍼티나 메서드를 추가해 인수로 전달받은 값을 프로퍼티에 할당하여 초기화하거나 고정값을 할당

3. 인스턴스 반환
   - 생성자 함수 내부의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this를 암묵적으로 반환
   - this가 아닌 다른 객체를 명시적으로 반환하면 return문에 명시한 객체가 반환됨
   - 명시적으로 원시 값을 반환하면 암묵적으로 this가 반환

> 생성자 함수 내부에서는 return문 반드시 생략할 것

#### 내부 메서드 [[Call]], [[Construct]]

- 일반 함수로서 호출되면 내부 메서드 `[[Call]]` 호출
- 생성자 함수로 호출되면 내부 메서드 `[[Construct]]` 호출

> [[Call]]을 갖는 함수 객체 === / 호출할 수 있는 객체 함수
> [[Construct]]를 갖는 함수 객체 === constructor
> [[Construct]]를 갖지 않는 객체 === non-constructor

**callable** : [[Call]]을 갖는 함수 객체 / 호출할 수 있는 객체 / 함수
**constructor** : [[Construct]]를 갖는 함수 객체 / 생성자 함수로서 호출할 수 있는 함수
**non-constructor** : [[Construct]]를 갖지 않는 객체 / 객체를 생성자 함수로서 호출할 수 없는 함수

함수 객체 === callable
함수 객체 === constructor || non-constructor

> 함수 객체는 callable이면서 constructor이거나, non-constructor이다.

![](https://velog.velcdn.com/images/wjinss/post/11ca9170-d011-4018-bb72-ffc93b2ecc7a/image.png)

#### constructor와 non-constructor의 구분

JS엔진은 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 구분한다.

- constructor : 함수 선언문, 함수 표현식, 클래스
- non-constructor : 메서드(ES6 축약 표현), 화살표 함수

non-constructor인 함수 객체를 생성자 함수로 호출하면 에러 발생

#### new연산자

생성자 함수는 일반적으로 첫 문자를 대문자로 생성해 일반 함수와 구별한다.

왜?

> 일반함수에 new 연산자를 붙여서 생성자 함수를 만들 수 있지만, 만들어진 함수가 일반 함수로 호출되면 내부의 this가 전역 객체를 가리켜 프로퍼티와 메서드는 전역 객체의 프로퍼티와 메서드가 된다.
> 이를 방지하기 위함이다.

#### new.target

메타 프로퍼티라고도 부르며, this와 유사하게 constructor인 모든 함수 내부에서 암묵적인 지연 변수와 같이 사용된다.

**new연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신을 가리킨다. new연산자 없이 일반 함수로서 호출되면 new.target은 undefined이다.**

```jsx
// 생성자 함수
function Circle(rad) {
  // 이 함수가 생성자 함수가 아니면 new.target은 undefined
  if (!new.target) {
    // 생성자 함수를 재귀 호출해 생성된 인스턴스를 반환
    return new Circle(rad);
  }
  this.radius = rad;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

> IE는 new.target을 지원하지 않는다. new.target을 사용할 수 없는 상황에서는 **스코프 세이프 생성자 패턴**을 사용할 수 있다.

대부분의 빌트인 생성자 함수는 new연산자와 함께 호출됐는지 확인한 후 적절한 값을 반환한다.
Object와 Function 생성자 함수는 new연산자 없이 호출해도 문제 없다.
하지만 String, Number, Boolean 생성자 함수는 new연산자 없이 호출하면 문자열, 숫자, 논리형값을 반환한다.

---

## 19. 프로토타입

### 객체지향 프로그래밍이란?

명령형 프로그래밍의 절차지향적 관점에서 벗어나 여러 개의 독립적 단위, 즉 객체의 집합으로 프로그램을 표현하는 프로그래밍 패러다임이다.

> 함수의 순서가 아닌, 객체들이 모여 상호 협력하면서 데이터를 처리하는 방식의 프로그래밍 설계 방법

**추상화란?**

- 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것

각 객체는 자신의 기능을 수행하면서 다른 객체와 관계성을 가질 수 있다. 다른 객체와 데이터를 처리하거나, 상태 데이터나 동작을 상속받아 사용할 수 있다.

#### 상속과 프로토타입

어떤 객체의 프로퍼티 or 메서드를 다른 객체가 상속받아 사용하는 것

> 기존의 코드를 재사용

**상속 ❌**

```jsx
function Circle(rad) {
  this.radius = rad;
  this.getArea = function () {
    return Math.PI * this.radius ** 2;
  };
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);
const circle3 = new Circle(3);
const circle4 = new Circle(4);
```

> `getArea()`가 중복으로 생성됨 > 성능 저하

**상속 ✅**
JS는 프로토타입 기반으로 상속 구현

```jsx
function Circle(rad) {
  this.radius = rad;
}

Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

const circle1 = new Circle(1);
const circle2 = new Circle(2);
const circle3 = new Circle(3);
const circle4 = new Circle(4);
```

> `getArea()` 단 하나만 생성되었으며, 모든 인스턴스는 `getArea()`를 상속을 받음

인스턴스는 자신의 프로토타입(상위 / 부모 객체)인 생성자함수명.prototype의 모든 프로퍼티와 메서드를 상속받는다.

#### 프로토타입 객체

프로토타입이라고도 부르며, 객체 간 상속을 구현하기 위해 사용된다. 어떤 객체의 상위 / 부모 객체로서 다른 객체에 공유 프로퍼티를 제공한다. 상속받은 하위 / 자식 객체는 상위 객체의 프로퍼티를 자유롭게 사용한다.

모든 객체는 하나의 프로토타입을 가지며, 모든 프로토타입은 생성자 함수와 연결되어있다.
![](https://velog.velcdn.com/images/wjinss/post/4d860c4f-100e-451e-be58-8ef856a0b5c4/image.png)

모든 객체는 [[Prototype]]이라는 내부 슬롯을 가지며, `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입에 간접적으로 접근할 수 있다.

> 즉, [[Prototype]] === 객체의 프로토타입

프로토타입은 자신의 constructor 프로퍼티를 통해 생성자 함수에 접근할 수 있고, 생성자 함수는 자신의 prototype 프로퍼티를 통해 프로토타입에 접근할 수 있다.

> 프로토타입.constructor === 자신(프로토타입 객체)을 만든 생성자 함수
> 생성자함수.prototype === 자신의 프로토타입

#### `__proto__` 접근자 프로퍼티

모든 객체는 `__proto__`를 통해 자신의 프로토타입에 접근할 수 있다.

![](https://velog.velcdn.com/images/wjinss/post/4c2c746d-176c-4298-bd50-45d054b15ad2/image.png)

- `__proto__`는 접근자 프로퍼티다.
  접근자 프로퍼티는 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수이다.
  즉 [[Get]], [[Set]] 프로퍼티 어트리뷰트로 구성된 프로퍼티이다.
  - getter 함수인 [[Get]]이 호출 / `get __proto__`
  - setter 함수인 [[Set]]이 호출 / `set __proto__`
- `__proto__`는 상속을 통해 사용된다.
  `__proto__접근자 프로퍼티`는 객체가 소유한 프로퍼티가 아니라 Object.prototype의 프로퍼티이다.
  즉, 모든 객체는 상속을 통해 `Object.prototype.__proto__`를 사용할 수 있다.

- `__proto__`를 통해 프로토타입에 접근하는 이유.
  상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위함이다.

```jsx
const obj1 = {};
const obj2 = {};

obj2.__proto__ = obj1;
obj1.__proto__ = obj2;
// 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인이 만들어진다.
```

> 프로토타입 체인은 프로퍼티 검색 방향이 한쪽 방향으로만 흘러가야 한다(단방향 링크드 리스트)

- `__proto__`를 코드 내에서 직접 사용하는건 권장❌
  모든 객체가 `__proto__`를 사용할 수 있지 않기 때문이다.
  - 프로토타입의 참조 취득 : `Object.getPrototypeOf` 권장
  - 프로토타입 교체 : `Object.setPrototypeOf` 권장

#### 함수 객체의 prototype 프로퍼티

함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.

> 일반 객체는 prototype 프로퍼티가 없으며, noo-constructor인 함수는 프로토타입을 생성하지 않는다.

모든 객체가 가지고 있는 `__proto__`와 함수 객체의 prototype은 **동일한 프로토타입을 가리킨다. **

| 구분        | 소유        | 값                | 사용 주체   | 사용 목적                                                  |
| ----------- | ----------- | ----------------- | ----------- | ---------------------------------------------------------- |
| `__proto__` | 모든 객체   | 프로토타입의 참조 | 모든 객체   | 객체가 자신의 포로토타입에 접근 or 교체하기 위함           |
| prototype   | constructor | 프로토타입의 참조 | 생성자 함수 | 생성자 함수가 자신의 인스턴스의 프로토타입을 할당하기 위함 |

#### 프로토타입의 constructor 프로퍼티와 생성자 함수

모든 프로토타입은 constructor를 가지며, constructor는 prototype으로 자신을 참조하는 생성자 함수를 가리킨다. 이 연결은 함수 객체(생성자 함수)가 생성될때 이뤄진다.

인스턴스에는 constructor가 없지만, 프로토타입으로 상속받아 constructor를 사용할 수 있다.

1. JS 엔진이 인스턴스에서 constructor를 못 찾음
2. 인스턴스.[[Prototype]] => 생성자함수.prototype으로 올라감
3. 생성자함수.prototype에는 기본적으로 constructor: 생성자함수가 있다

> 인스턴스.constructor === 생성자 함수

#### 리터럴의 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

객체 리터럴로 만든 객체의 프로토타입의 constructor가 가리키는 생성자 함수가 !== 객체를 생성한 생성자 함수

```jsx
//객체 리터럴로 생성한 객체(생성자 함수X)
const obj = {};

// obj 객체의 생성자 함수는 Object 생성자 함수
obj.constructor === Object; // true
```

그럼 객체 리터럴로 객체를 만들면 Object 생성자 함수로 만드는 것이다?

**ECMAScript 사양에서는 Object 생성자 함수에 인수를 전달하지 않거나, undefined, null을 전달해 호출하면 내부적으로 추상 연산 OrdinaryObjectCreate을 호출해 Object.prototype을 프로토타입으로 갖는 빈 객체를 생성한다.**

> 추상연산이란? ECMAScript 사양에서 내부 동작의 구현 알고리즘을 표현한 것

Object 생성자 함수 호출과 객체 리터럴의 평가는 추상연산(OrdinaryObjectCreate)을 호출해 빈 객체를 만드는 것은 동일하나 new.target을 확인하거나 프로퍼티를 추가하는 세부적인 부분이 다르다.
(객체 리터럴은 new.target 자체를 확인하지 않는다.)

> Object() → new.target = undefined
> new Object() → new.target = Object 생성자 함수
> {} → new.target 확인 자체 없음

그래서 객체 리터럴에 의해 생성된 객체는 Object 생성자 함수가 생성한 객체가 아니다.

함수의 경우에는 함수 선언문과 함수 표현식으로 만든 함수 !== Function 생성자 함수이다.
하지만 constructor로 확인하면 생성자 함수이다.

> **하지만 프로토타입과 생성자 함순는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.**
> 리터럴 표기법에 의해 생성된 객체도 가상적인 생성자 함수를 갖는다.

리터럴 표기법에 의해 생성한 객체나, 생성자 함수로 만든 객체나 본질적으론 **객체**로 동일한 특성을 갖는다.
**즉, 객체 리터럴도 프로토타입을 가진다. **

| 리터럴 표기법 | 생성자 함수 | 프로토타입         |
| ------------- | ----------- | ------------------ |
| 객체 리터럴   | Object      | Object.prototype   |
| 함수 리터럴   | Function    | Function.prototype |
| 배열 리터럴   | Array       | Array.prototype    |
| 정규 표현식   | RegExp      | RegExp.prototype   |

**근데 왜 객체 리터럴도 프로토타입을 가지는가? **

객체 리터럴도 내부적으로 추상연산 OrdinaryObjectCreate을 호출해 객체를 만들기 때문에 프로토타입을 가진다.
즉, new.target을 확인하진 않지만, 프로토타입을 설정하는 로직(OrdinaryObjectCreate을)은 여전히 들어가 있다.

> 즉, 객체 리터럴로 **빈 객체**를 만들면 최종적으로 같은 추상연산을 통해 객체를 만들고 프로토타입에 연결되는 것이라서 Object 생성자 함수로 만드는 것과 동일하다.
>
> 최종 결과물은 같지만, 차이점은 과정에서의 new.target 확인 여부이다.

#### 프로토타입의 생성 시점

프로토타입은 생성자 함수가 **생성되는 시점에 생성**된다.
프로토타입은 생성자 함수는 언제나 쌍으로 존재하기 때문

- **사용자 정의 생성자 함수와 프로토타입 생성 시점**

  - 생성자 함수로서 호출할수 있는 함수(constructor)는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 생성
  - non-constructor는 프로토타입 생성 ❌
  - 생성된 프로토타입의 프로토타입은 언제나 Obejct.prototype

- **빌트인 생성자 함수와 프로토타입 생성 시점**
  - 빌트인 생성자 함수도 생성되는 시점에 프로토타입이 생성 === 전역객체가 생성되는 시점
  - 생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩
  - 생성자 함수, 객체 리터럴로 생성된 객체의 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당 === 생성된 객체는 프로토타입을 상속

#### 객체 생성 방식과 프로토타입의 결정

프로토타입은 추상연산 OrdinaryObjectCreate에 전달되는 인수에 의해 결정되며, 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

- **객체 리터럴에 의해 생성된 객체의 프로토타입**
  - JS엔진은 객체 리터럴에 의해 생성된 객체의 프로토타입은 Obeject.prototype
  - Obeject.prototype을 상속받음
- **Object 생성자 함수에 의해 생성된 객체의 프로토타입**
  - 인수없이 Object 생성자 함수를 생성한(빈 객체) 객체의 프로토타입은 Obeject.prototype
  - Obeject.prototype을 상속받음
- **생성자 함수에 의해 생성된 객체의 프로토타입**
  - 생성자 함수에 의해 생성된 객체의 프로토타입 === 생성자 함수 prototype에 바인딩 되어있는 객체 === Constructor의 prototype === Obeject.prototype을 상속받음
  - **프로토타입의 프로토타입은 언제나 Obeject.prototype**

> 즉, 객체를 생성할땐 추상연산 OrdinaryObjectCreate을 호출하는데 이때 전달되는 프로토타입이 Obeject.prototype이기 때문

#### 프로토타입 체인

JS의 객체는 프로퍼티에 접근할때 해당 객체에 접근하려는 프로퍼티가 없으면 상위 / 부모의 프로토타입을 찾는 것을 반복한다.
이로 인해 모든 객체는 Obeject.prototype에 접근할 수 있으며 객체 지향 프로그래밍의 상속과 프로퍼티 검색을 구현하는 메커니즘이다.

**Obeject.prototype는 체인의 종점이며, Obeject.prototype의 프로토타입은 null**

스코프 체인과 프로토타입 체인은 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다.

> 스코프 체인이란? 스코프가 계층적으로 연결된 것이며, JS는 변수를 참조할 때 변수를 참조하는 코드에서 시작해 상위 스코프 방향으로 이동하며 변수를 검색한다.

#### 오버라이딩과 프로퍼티 섀도잉

**오버라이딩**

- 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

**프로퍼티 섀도잉**

- 오버라이딩(상속관계)에 의해 프로퍼티가 가려지는 현상

오버로딩(JS지원 ❌)

- 동일한 함수 이름에 매개변수의 타입 or 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식(TS의 함수 오버로드)

프로퍼티를 삭제할땐 오버라이딩된 프로퍼티가 삭제되며 프로퍼티 섀도잉된 프로퍼티는 유지된다.
프로토타입 프로퍼티를 변경 or 삭제하려면 프로토타입에 직접 접근해야된다.

#### 프로토타입 교체

생성자 함수, 인스턴스에 의해 프로토타입을 변경할 수 있어 객체간의 상속관계를 동적으로 변경할 수 있다.

- **생성자 함수에 의한 프로토타입의 교체**
  - 생성자 함수의 .prototype을 다른 객체로 바꾸면 이후 생성된 인스턴스들은 바뀐 프로토타입을 상속받음
  - constructor와 생성자 함수의 연결이 없어짐(Obeject.prototype 연결됨)
  - 미래에 생성할 인스턴스의 프로토타입을 교체하는 것

```jsx
function Person(name) {
  this.name = name;
}

// 기본 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`hello! 나는 ${this.name}`);
};

const p1 = new Person("바뀌기 전");
p1.sayHello(); // hello! 나는 바뀌기 전

//생성자 함수의 프로토타입을 교체
Person.prototype = {
  sayHI() {
    console.log(`HI, 나는 ${this.name}`);
  },
};

const p2 = new Person("바뀐 뒤");

// 새 인스턴스는 새로운 프로토타입을 따름
p2.greet(); // HI, 나는 바뀐 뒤
// p2.sayHello(); // TypeError

// 하지만 기존 인스턴스는 교체 전 프로토타입을 유지
p1.sayHello(); // hello! 나는 바뀌기 전
```

> 프로토타입 교체 이후에 만든 인스턴스는 새로운 프로토타입을 따른다.(constructor가 없기 때문)
> 교체 이전 인스턴스는 기존 프로토타입을 그대로 따른다.

- **인스턴스에 의한 프로토타입의 교체**
  - 이미 만들어진 인스턴스의 [[Prototype]]을 바꾸는 것 ( `__proto__`, setPrototypeOf 등)
  - 이미 생성된 객체의 프로토타입을 교체하는 것(기존 인스턴스에도 즉시 영향을 줌)
  - constructor와 생성자 함수의 연결이 없어짐(Obeject.prototype 연결됨)

```jsx
function Person(name) {
  this.name = name;
}

// 기본 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`hello! 나는 ${this.name}`);
};

const p1 = new Person("바뀌기 전");
p1.sayHello(); // hello! 나는 바뀌기 전

// 인스턴스의 프로토타입을 교체
const newProto = {
  sayHI() {
    console.log(`HI, 나는 바뀐 뒤`);
  },
};

Object.setPrototypeOf(p1, sayHI);

p1.sayHI(); // HI, 나는 바뀐 뒤
p1.sayHello(); // TypeError: p1.sayHello is not a function
```

> 인스턴스의 [[Prototype]]을 바꾸면, 그 시점 이후부터는 새로운 프로토타입 체인을 따른다.
> 기존 프로토타입 체인에 있던 메서드는 더 이상 못 쓴다.

**하지만 프로토타입은 직접 교체하지 않는 것이 좋다. **

#### instanceof 연산자

좌변에 객체, 우변에 생성자 함수를 피연산자로 받으며, 우변의 피연산자가 함수가 아닌 경우 TypeError를 반환한다.

```jsx
객체 instanceof 생성자 함수
```

우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true, 그렇지 않으면 false를 반환

```jsx
function Person(name) {
  this.name = name;
}

const me = new Person("우리둥절");

// Person.prototype이 me객체의 프로토타입 체인 상에 존재하므로 true
console.log(me instanceof Person); // true
// Object.prototype이 me객체의 프로토타입 체인 상에 존재하므로 true
console.log(me instanceof Object); // true

// 프로토타입 교체
const parent = {};
Object.setPrototypeOf(me, parent);

// Person.prototype이 me객체의 프로토타입 체인 상에 존재하므로 false
console.log(me instanceof Person); // false
// Object.prototype이 me객체의 프로토타입 체인 상에 존재하므로 false
console.log(me instanceof Object); // false

// parent 객체를 Person 생성자 함수의 프로토타입에 바인딩
Person.prototype = parent;

// Person.prototype이 me객체의 프로토타입 체인 상에 존재하므로 true
console.log(me instanceof Person); // true
// Object.prototype이 me객체의 프로토타입 체인 상에 존재하므로 true
console.log(me instanceof Object); // true
```

이처럼 instanceof 연산자는 생성자 함수의 prototype에 바인딩된 객체가 **프로토타입 체인 상에 존재하는지 확인한다.**

#### 직접상속

- **Object.create에 의한 직접 상속**
  명시적으로 프로토타입을 지정해 새로운 객체를 생성한다.

```jsx
Object.create(proto[, propertiesObject])
```

첫 번째 매개변수: 생성할 객체의 프로토타입으로 지정할 객체
두 번째 매개변수: 생성할 객체의 프로퍼티를 객체(옵션)
반환값: 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체

```jsx
let obg = Object.create(null); // 프로토타입이 null인 객체 생성. 생성된 객체는 프로토타입 종점에 위치
//Object.prototype을 상속받지 못함

obj = Object.create(Object.prototype); //obj = {}과 동일

obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
}); // obj = {x:1}과 동일
// 아래 함수와 동일
// obj = Object.create(Object.prototype)
// obj.x = 1

const myProto = { x: 10 }; // 임의의 객체를 직접 상속

obj = Object.create(myProto);
console.log(obj.x); // 10

// 생성자 함수
function Person(name) {
  this.name = name;
}

obj = Object.create(Person.prototype); // obj = new Person과 동일
obj.name = "우리둥절";
console.log(obj.name); // 우리둥절
```

Object.create의 장점

- new 연산자가 없이도 객체를 생성 가능
- 프로토타입을 지정하면서 객체를 생성 가능
- 객체 리터럴에 의해 생성된 객체도 상속 가능

ESLint에서 Object.prototype의 메서드를 직접 호출하는 것은 권장되지 않는다.

- Object.create를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문
- 프로토타입 체인의 종점에 위치하는 객체는 Object.prototype의 메서드를 사용할 수 없음
- 에러를 없애기 위해 Object.prototype의 메서드는 간접적으로 호출하는 것이 권장됨

```jsx
const obj = Object.create(null);
obj.a = 1;

console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
```

- **객체 리터럴 내부에서 `__proto__`에 의한 직접 상속**
  Object.create은 여러 장점이 있지만, 두번째 인자로 프로퍼티를 정의하는 것이 번거롭다.
  ES6에서는 객체 리터럴 내부에서 `__proto__`를 사용해 직접 상속을 구현할 수 있다.

```jsx
const myProto = { x: 10 };

const obj = {
  y: 20,
  //객체를 직접 상속
  __proto__: myProto,
};

console.log(obj); // {y:20, x:10}
```

#### 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다.

```jsx
function Person(name) {
  this.name = name;
}

//프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`안녕! 내 이름은 ${this.name}이야!`);
};

//정적 프로퍼티
Person.staticProp = "정적 프로퍼티";

//정적 메서드
Person.staticMethod = function () {
  console.log("정적 메서드");
};

const me = new Person("우리둥절");

Person.staticMethod(); // 정적 메서드

//인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 됨
me.staticMethod(); // me.staticMethod is not a function
```

생성자 함수 객체가 소유한 프로퍼티/메서드를 정적 프로퍼티/메서드라 하며, 인스턴스로 참조/호출할 수 없다.
정적 프로퍼티/메서드는 인스턴스의 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니므로 인스턴스로 접근할 수 없다.

- Object.create는 정적 메서드라 인스턴스로 호출 ❌
- 인스턴스/프로토타입 메서드 내에서 this를 사용하지 않으면 그 메서드는 정적 메서드로 변경 가능✅
  - 인스턴스가 호출한 인스턴스/프로토타입 메서드 내에서 this는 인스턴스를 가리킨다.
  - 인스턴스를 참조할 필요가 없으면 정적 메서드로 변경해도 동작함
  - 정적 메서드는 인스턴스를 호출하지 않아도 호출 가능
    - 프로토타입 메서드를 호출하려면 인스턴스를 생성해야됨

```jsx
function Foo() {}

Foo.prototype.x = function () {
  console.log("x");
};

//프로토타입 메서드를 호출하려면 인스턴스를 생성
const foo = new Foo();
foo.x(); // x

// 정적 메서드
Foo.x = function () {
  console.log("x");
};

// 정적 메서드는 인스턴스 없이 호출 가능
Foo.x(); //x
```

#### 프로퍼티 존재 확인

- **in 연산자 **
  객체 내 특정 프로퍼티가 존재하는지 여부를 확인한다.

```jsx
key in object;
```

좌항엔 프로퍼티 키를 나타내는 문자열, 우항에는 객체

```jsx
const person = {
  name: "우리둥절",
};

console.log("name" in person); // true
console.log("address" in person); // false
```

in 연산자는 확인 대상 객체의 프로퍼티 뿐만 아니라 상속받는 모든 프로토타입의 프로퍼티를 확인한다.

```jsx
console.log("toString" in person); // true
//toString은 Object.prototype의 메서드
```

- **Reflect.has 메서드**
  in연산자와 동일하게 작동한다.
  첫 번째 매개변수: 객체
  첫 번째 매개변수: 프로퍼티 키를 나타내는 문자열

```jsx
const person = {
  name: "우리둥절",
};

console.log(Reflect.has(person, "name")); // true
console.log(Reflect.has(person, "address")); // false
console.log(Reflect.has(person, "toString")); // true
```

- **Object.prototype.hasOwnProperty 메서드**
  객체에 특정 프로퍼티가 존재하는지 확인한다.

```jsx
console.log(person.hasOwnProperty("name")); // true
console.log(person.hasOwnProperty("address")); // false
console.log(person.hasOwnProperty("toString")); // false
```

인수로 받은 키가 객체의 고유 프로퍼티: true
상속받은 프로토타입의 키: false

#### 프로퍼티 열거

- **for...in 문**
  객체의 모든 프로퍼티를 프로퍼티 개수만큼 순회하며 열거(나열)한다.

```jsx
for(변수선언문 in 객체) {...}
```

```jsx
const person = {
  name: "우리둥절",
  address: "쎄오울",
};

for (const key in person) {
  console.log(`${key} : ${person[key]}`);
}

// name : 우리둥절
// address : 쎄오울
```

in연산자처럼 상속받는 모든 프로토타입의 프로퍼티도 열거하지만, toString은 열거할 수 없다.
프로퍼티의 어트리뷰트 [[EnumRable]]의 값이 false인 경우는 열거 불가능하다.
[[EnumRable]] === 열거 가능 여부를 나타내는 논리형값

> 즉, for...in문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중 [[EnumRable]]의 값이 true인 프로퍼티를 순회하며 열거한다.

또한, 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않는다.
배열에서는 for || for...of, forEach를 권장하지만, 배열===객체이므로 가능하긴 하다.

- **Object.keys/values/entries 메서드 **
  객체 자신의 고유 프로퍼티만 열거하기 위해 사용한다. - Object.keys는 객체 자신의 **키**를 배열로 반환한다. - Object.values는 객체 자신의 **값**을 배열로 반환한다. - Object.entries는 객체 자신의 **키와 값의 쌍**을 배열로 반환한다.

```jsx
const person = {
  name: "우리둥절",
  address: "쎄오울",
  __proto__: { age: 1 },
};

console.log(Object.keys(person)); // ['name', 'address']
console.log(Object.values(person)); // ['우리둥절', '쎄오울']
console.log(Object.entries(person)); // [['name', '우리둥절'], ['address', '쎄오울']]
```
