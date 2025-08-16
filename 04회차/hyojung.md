# 17장 : 생성자 함수에 의한 객체 생성

## 17.1 `Object` 생성자 함수

자바스크립트는 객체를 생성하는 방법이 여러 가지 있는데, 그중 하나가 `Object` 생성자 함수다.

```js
const person = new Object();

person.name = 'Hyojung';
person.sayHello = function () {
  console.log('Hi! My name is ' + this.name);
};

console.log(person); // { name: 'Hyojung', sayHello: f }
```

하지만 일반적으로는 **객체 리터럴** `{}`을 더 많이 사용한다.
이유는 더 간단하고 직관적이기 때문!

`new Object()`는 도자기를 손수 구워 컵을 만드는 방법,
`{}`은 마트에서 컵을 바로 집어오는 방법.
결과는 같지만 편의성에서 차이가 있다고 이해하면 된다.

## 17.2 생성자 함수

### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점

객체 리터럴은 편리하지만, 동일한 형태의 객체를 여러 개 만들 때 **비효율적**이다. 😅

```js
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};
```

`circle1`, `circle2`는 구조가 같지만 각각 정의해줘야 한다.
객체가 많아질수록 코드 중복이 늘고 유지보수성이 떨어진다.

매번 빵을 손으로 반죽해서 구워야 한다면?
고객이 늘어날수록 제빵사는 과로로 쓰러질 것이다. 😵

### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

생성자 함수를 사용하면 동일한 구조의 객체를 **틀(생산 공장)**처럼 반복적으로 생성할 수 있다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

Circle 함수는 new 연산자와 함께 호출되었을 때만 인스턴스를 생성하는 생성자 함수로 동작한다.

제빵사는 이제 빵 기계를 들여놓았고,
버튼만 누르면 같은 빵을 여러 개 찍어낼 수 있게 됐다!

### 17.2.3 생성자 함수의 인스턴스 생성 과정

_근데 아까부터 자꾸 인스턴스 거리는데 . .
인스턴스가 뭐지? 싶으면 아래 글을 꼭 확인해주세요!_

> **💡 인스턴스란 무엇일까요?**
> · 생성자 함수에 의해 생성된 객체를 인스턴스라고 합니다.
> · 즉, 생성자 함수 = 설계도, 인스턴스 = 그 설계도로 찍어낸 실제 제품이라고 생각하면 쉬워요.

_아직도 이해가 부족하다고요?_

> 생성자 함수 자체는 **그냥 함수일 뿐**이에요.
> 하지만 **new 연산자와 함께 호출**되면 **새로운 객체가 만들어지고**, 그 객체는 **생성자 함수의 틀**을 따릅니다.
> 이렇게 탄생한 객체가 바로 **인스턴스**예요!

🤓 다시 본론으로 돌아와서,
new와 함께 생성자 함수가 호출되면 내부적으로 다음 과정이 일어난다.

1️⃣ **빈 객체 생성** : 내부적으로 `{}` 같은 객체를 만든다.
2️⃣ **this 바인딩** : 새로 만든 객체를 `this`에 바인딩한다.
3️⃣ **프로퍼티 추가** : 생성자 함수 내부 코드를 실행하면서 `this`에 프로퍼티나 메서드를 추가한다.
4️⃣ **자동 반환** : 생성된 객체를 반환한다. (명시적으로 객체를 반환하지 않는 한 자동으로 `this` 반환)

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle = new Circle(5);
console.log(circle); // Circle { radius: 5, getDiameter: f }
```

☕️ 위의 코드를 일상생활에 비유해보면 아래와 같다.

1️⃣ 빈 컵 준비 (**빈 객체 생성**)
· `new` 키워드가 실행되면 JS 엔진은 제일 먼저 아무것도 없는 빈 객체를 만든다.
· 이 시점의 객체는 아직 내용물이 없고, 단지 "곧 무언가가 담길 예정"인 상태다.
· 컵을 씻어서 새로 꺼낸 것과 같다!

2️⃣ "이 컵은 너 거야" 라벨 붙이기 (**this 바인딩**)
· 새로 만든 빈 객체를 생성자 함수 내부에서 `this`라는 키워드로 연결한다.
· 즉, 생성자 함수 안에서 `this`라고 쓰면, 방금 준비한 그 컵을 가리키게 된다.
· 컵에 "효정"이라고 라벨을 붙여서, 종업원(=엔진)과 손님(=개발자)이 헷갈리지 않게 하는 과정이다.

```js
function Circle(radius) {
  // 여기서 this = 빈 객체
  this.radius = radius;
}
```

3️⃣ 커피 채워 넣기 (**프로퍼티 할당**)
· 이제 생성자 함수 본문이 실행되면서, `this`에 속성이나 메서드가 추가된다.
· 즉, 빈 컵에 주문받은 커피(프로퍼티, 메서드)를 담는 과정이다.

```js
function Circle(radius) {
  this.radius = radius; // 컵에 radius라는 음료를 담음
  this.getDiameter = function () {
    return 2 * this.radius; // 컵에 추가 메뉴까지 추가
  };
}
```

4️⃣ 컵을 건네주기 (**자동 반환**)
· 함수 실행이 끝나면, JS는 자동으로 `this`(즉, 새로 만들어진 객체)를 반환한다.
· 개발자가 `return this;` 라고 직접 쓰지 않아도 엔진이 알아서 해준다.
· 즉, 손님이 주문한 음료가 다 채워진 컵을 손님한테 건네주는 것이다.

```js
const circle = new Circle(5);
// 컵이 손님(circle 변수) 손에 전달된 상태
```

이렇게 해서 만들어진 컵이 바로 **인스턴스**_!_
손님(circle1, circle2)은 각자 컵을 가지지만,
같은 커피 머신(Circle 생성자 함수)에서 만들어졌으니 기본 구조는 같다.

### 17.2.4 내부 메서드 `[[call]]`과 `[[Construct]]`

JS의 모든 함수 객체는 **호출할 수 있는 성질(callable)**을 가진다.
그런데 어떤 함수는 생성자로도 사용할 수 있고, 어떤 함수는 그렇지 않다.
이 차이를 구분하는 기준이 바로 내부 메서드인 `[[Call]]`과 `[[Construct]]`다.

💭 _근데 왜 대괄호 두 개로 표기할까?_
모든 함수 객체는 내부 메서드(Internal Method)를 가진다.
`[[Call]]`, `[[Construct]]`는 ECMAScript 명세 전용 내부 메서드다.
우리가 코드에서 `foo[[Call]]()` 이런 식으로 직접 호출할 수 없고, 오직 엔진이 내부적으로 동작시킨다.
이걸 구분하기 위해 "내부 전용"이라는 의미를 담아 이중 대괄호로 표기한다.

이걸 일상생활에 비유해보면,
마트 안쪽의 "직원 전용 문"처럼, 고객(개발자)은 직접 못 들어가고 직원(엔진)만 쓸 수 있는 문이다.
`foo()`라고 호출하면 엔진이 뒤에서 직원 전용 문`([[Call]])`을 열어 일을 처리한다.
`new foo()`라고 호출하면 이번에는 직원이 다른 문`([[Construct]])`을 열고 처리하는 셈이다.

즉, **같은 함수라도 호출 방식에 따라** 엔진은 **서로 다른 내부 메서드를 실행**한다.

```js
function Foo() {}

Foo(); // [[Call]] → 일반 함수 호출
new Foo(); // [Construct]] → 생성자 함수 호출
```

But _!_
모든 함수가 `[[Call]]`을 갖고 있지만, 모든 함수가 `[[Construct]]`를 갖는 건 아니다.
즉, 어떤 함수는 "생성자 함수 역할"을 할 수 있고, **어떤 함수는 못한다**. (→ 이건 17.2.5에서 더 자세히 다룸)

### 17.2.5 `constructor`와 `non-constructor`의 구분

JS 엔진은 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 `constructor`와 `non-constructor`로 구분한다.

· `constructor` : 생성자 함수로 호출할 수 있는 함수 (함수 선언문, 함수 표현식, 클래스)
· `non-constructor` : 생성자 함수로 호출할 수 없는 함수 (화살표 함수, 메서드 축약 표현)

```js
function Foo() {} // constructor

const Bar = function () {}; // constructor
const Baz = () => {}; // non-constructor
```

면허(constructor 자격)가 있는 사람만 운전이 가능한 것처럼, 모든 사람이 운전대를 잡을 수 있는 건 아니다.

### 17.2.6 `new` 연산자

함수 앞에 `new`를 붙이면 **생성자 함수 호출**이고,
함수 앞에 `new`를 붙이지 않으면 **일반 함수 호출**이다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 생성자 함수 호출
const circle = new Circle(5);

// 일반 함수 호출
const normal = Circle(10);
console.log(normal); // undefined
```

일반 함수로 호출하면 `this`가 전역 객체(`window`/`global`)에 바인딩될 수 있어 문제가 된다.
따라서 **생성자 함수**는 보통 이름을 **대문자**로 시작해서 구분한다.

항상 `new`와 함께 호출하는 건, "안전모(`new`)"를 안 쓰고 공장에 들어가면 사고가 나는 것으로 비유할 수 있다.

### 17.2.7 `new.target`

ES6에서 도입된 기능으로, 함수가 `new`와 함께 호출됐는지 아닌지를 확인할 수 있다.

```js
function Circle(radius) {
  if (!new.target) {
    // new 없이 호출되면 new 붙여서 다시 호출
    return new Circle(radius);
  }
  this.radius = radius;
}

const circle1 = Circle(5); // new Corclr(5)로 보정됨
const circle2 = new Circle(10);
```

놀이공원에서 직원이 "헬멧(`new`) 쓰셨나요? 안 쓰셨으면 여기서 챙기세요!" 라고 검사하는 것과 같다.

### ✨ Summary

· 객체 리터럴은 간단하지만 동일한 구조의 객체를 여러 개 만들 땐 비효율적!
· 생성자 함수는 객체 생성 패턴을 공장처럼 자동화할 수 있다.
· `new` 연산자와 함께 호출하면 `this`가 새 객체에 바인딩되고 반환된다.
· 모든 함수가 생성자가 될 수 있는 건 아니며, 화살표 함수는 생성자로 사용할 수 없다.
· `new.target`을 이용해 `new` 누락을 방지할 수 있다.

---

# 19장 : 프로토타입

💡 프로토타입이란 무엇일까?
JS에서 모든 객체는 다른 객체를 참조할 수 있는데, 이때 참조하는 객체를 프로토타입(prototype)이라고 한다.

🤓 **"참조하는 객체를 프로토타입이라고 한다"는 말의 의미 **
모든 객체는 내부에 `[[Prototype]]`이라는 비밀 슬롯을 가지고 있다.
이 슬롯은 또 다른 객체를 가리키는데, 그 객체가 바로 프로토타입 객체다.
그래서 "객체가 참조하는 그 객체를 프로토타입이라 한다"는 것이다.

_아니 근데 . .
슬롯이 사람 이름은 아닐테고.
슬롯이 대체 뭐야?_

> **슬롯(Internal Slot)이란?**
> 슬롯은 JS 엔진 내부에서 객체의 상태를 저장하기 위해 사용하는 숨겨진 프로퍼티다.
> ECMAScript에서는 이를 Internal Slot(내부 슬롯)이라고 부르고, 표기할 때 `[[ ]]`를 사용한다.
> 중요한 점은, 개발자가 코드로 직접 접근할 수 없다는 점.

· `[[Prototype]]` : 객체가 참조하는 프로토타입을 가리키는 슬롯
· `[[Environment]]` : 함수가 선언될 때의 스코프 정보를 담는 슬롯
· `[[Extensible]]` : 객체에 새로운 프로퍼티 추가 가능 여부

슬롯 vs 프로퍼티
· 프로퍼티 : 우리가 코드에서 obj.key처럼 직접 접근 가능한 키-값 쌍
· 슬롯 : 엔진 내부 전용, 우리가 직접 접근 불가

_걍 한 마디로 정리하면,_
슬롯은 JS 엔진 내부 전용 데이터 저장소인데,
프로퍼티와 달리 코드에서 쓸 수 없고 오직 엔진이 객체를 관리하는 데 사용하는 숨겨진 정보임.

JS에서 객체는 자기 자신만의 프로퍼티(속성, 메서드)를 가진다.

그런데 모든 객체가 공통적으로 필요로 하는 기능들이 있잖아?
예를 들어:
· 모든 배열이 쓸 수 있는 `push()`, `pop()`
· 모든 문자열이 쓸 수 있는 `toUpperCase()`
· 모든 객체가 쓸 수 있는 `hasOwnProperty()`

이런 "공통 기능"을 각 객체에 일일이 집어넣지 않고, **프로토타입**이라는 공유 저장소에 모아둔다.
그리고 객체는 필요할 때 자기 프로토타입을 참조해서 빌려 쓰는 구조다.

**일상생활에 비유를 하면 좀 더 이해가 쉬울 것이다.**
_내가 공부를 하다가 모르는 내용이 있으면?_
내 머리(인스턴스)에 없으니, 교과서(프로토타입)을 본다.
_교과서에도 없으면?_
도서관(상위 프로토타입)에서 찾아본다.
결국 내가 가진 지식 + 내가 속한 지식의 출처(프로토타입)를 합쳐서 활용하는 셈*!*

그래서 프로토타입이 뭐냐~
프로토타입은 **객체들이 공유하는 원형 객체**!
JS의 상속은 클래스 → 인스턴스가 아니라, **인스턴스 → 프로토타입** 참조 구조로 이루어진다.

## 19.1 객체지향 프로그래밍

JS는 객체 기반 프로그램이 언어다.
객체지향 프로그래밍(OOP)은 현실 세계의 대상을 코드 속 객체로 추상화하여, 상태와 동작을 정의하고, 이를 조합해 문제를 해결하는 프로그래밍 패러다임이다.

조금 더 자세히 알아보자.
**추상화(abstraction)**란?
현실 세계의 복잡한 대상에서 중요한 특징만 뽑아내고, 불필요한 세부 사항은 생략하는 것!
예컨대 사람을 설명할 때 세포 하나하나를 말하지 않고, 이름 | 나이 | 행동 같은 주요 특징만 말하는 것과 같다.

JS에서 객체는 상태(state)와 동작(behaviour)을 가진다.
· 상태 → 프로퍼티 (ex : `name`, `age`)
· 동작 → 메서드 (ex : `sayHello()`)
즉, 현실 세계의 개념을 단순화하여 **"속성 + 행동"** 구조로 표현하는 것이 **객체 단위의 추상화**다.

자동차를 코드로 추상화해보자.
현실 세계 속 자동차 → 색상, 연식, 속도, 달리기, 멈추기

```js
const car = {
  colour: 'red',
  speed: 0,
  drive() {
    console.log('부릉!');
  },
  stop() {
    console.log('끼익!');
  },
};
```

사람은 어떨까?
현실 세계 속 사람 → 이름, 나이, 걷기, 말하기

```js
const person = {
  name: 'Hyojung',
  age: 26,
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};
```

이렇게 하면, 복잡한 현실을 단순화된 객체 단위로 코드 속에 옮겨올 수 있고, 이를 조합하여 더 큰 프로그램을 만든다. (코드가 직관적 + 재사용성과 유지보수성 향상)

## 19.2 상속과 프로토타입

객체지향 프로그래밍에서 중요한 개념 중 하나가 **상속**이다.

**💡 상속이란?**
부모 객체의 속성과 메서드를 자식 객체가 재사용하는 것을 말한다.
클래스 기반 언어(Java, C++ 등)는 클래스를 통해 상속을 구현하지만,
**JS**는 클래스가 아닌 **프로토타입(prototype)**을 통해 상속을 구현한다.

**🤷🏻‍♀️ 상속이 왜 필요할까?**
객체마다 똑같은 메서드를 중복해서 정의하면, 비효유적이고 유지보수하기 어렵다.
상속을 이용하면, 공통 기능은 부모 객체(또는 프로토타입)에 정의하고, 자식 객체는 그걸 "물려받아" 사용하면 된다
JS에서는 생성자 함수로 객체를 생성하면,
그 객체(인스턴스)에는 직접 정의된 프로퍼티만 있고,
공통 메서드는 프로토타입 객체에 저장된다.
인스턴스가 메서드를 호출하면, 자기 자신에 없으면 **자동으로 프로토타입을 참조**해서 찾는다.

```js
function Person(name) {
  this.name = name;
}

// 공통 기능은 프로토타입에 정의
Person.prototype.sayHello = function () {
  console.log(`Hi! I'm ${this.name}.`);
};

const me = new Person('Henry');
const you = new Person('Charlie');

me.sayHello(); // Hi! I'm Henry.
you.sayHello(); // Hi! I'm Charlie.
```

여기서 `sayHello` 메서드는 각각의 인스턴스에 복사된 게 아니다.
프로토타입(`Person.prototype)`에 **단 한 번만 정의**되어 있고, **인스턴스들이 공유**한다.

**✨ Summary**
· 상속은 코드 재사용성을 위해 필수적인 개념이다.
· JS는 프로토타입을 기반으로 상속을 구현한다.
· 인스턴스에 없는 프로퍼티/메서드를 찾을 때는, 자동으로 연결된 프로토타입 객체에서 탐색한다.
· 따라서 모든 인스턴스는 공통 기능을 공유하면서도, 각자 고유한 상태(프로퍼티)를 가진다.

## 19.3 프로토타입 객체

**프로토타입 객체**(prototype object)는 **모든 인스턴스가 공유하는 부모 객체** 역할을 한다.
생성자 함수로 객체(인스턴스)를 만들면, 그 인스턴스는 내부 슬롯`[[Prototype]]`을 통해 생성자 함수의 프로토타입 객체와 연결된다.
따라서 인스턴스는 자신에게 없는 속성/메서드를 프로토타입 객체에서 찾아 쓸 수 있다.

**프로토타입 객체의 본질은?**
그냥 일반 객체(Object)다.
다만 특별히 인스턴스들이 공유하는 공용 창고 역할을 한다는 점에서 의미가 있다.
예를 들어 `Person` 생성자 함수를 정의하면, JS 엔진은 자동으로 `Person.prototype`이라는 프로토타입 객체를 생성해둔다.

**인스턴스와의 연결은 어떻게 되는 걸까?**
`new Person('Lee')` 같은 식으로 객체를 만들면, 새 인스턴스의 내부 슬롯`[[Prototype]]`은 `Person.prototype`을 가리킨다.
이 덕분에 인스턴스에서 프로퍼티를 찾을 때 없으면, 자동으로 `Person.prototype`을 뒤진다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! I'm ${this.name}.`);
};

const me = new Person('Henry');
const you = new Person('Charlie');

// me, you에는 sayHello가 없음
me.sayHello(); // 프로토타입에서 찾음 → Hi! I'm Henry.
you.sayHello(); // Hi! I'm Charlie.
```

### 19.3.1 `__proto__` 접근자 프로퍼티

모든 객체는 자신의 프로토타입에 접근할 수 있는 `__proto__` 접근자 프로퍼티를 가진다.
사실 `__proto__`는 내부 슬롯`[[Prototype]]`을 간접적으로 보여주는 "창구"일 뿐이다. 🙂

내부 슬롯은 엔진 전용이라 직접 접근 불가. (`obj.[[Prototype]]` ❌)
그래서 개발자 편의를 위해 `__proto__`라는 게터/세터 프로퍼티가 제공된다.
하지만 `__proto__`는 비표준이었고, 나중에 호환성 때문에 ES6에서 표준에 포함되긴 했지만, 모던 코드에서는 대신 다음 메서드를 쓰는 게 좋다.
· `Object.getPrototypeOf(obj)` → 프로토타입 얻기
· `Object.setPrototypeOf(obj, proto)` → 프로토타입 교체하기

```js
const obj = {};

console.log(obj.__proto__ === Object.prototype); // true
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

### 19.3.2 함수 객체의 prototype 프로퍼티

JS에서 함수 객체만 가지는 특별한 프로퍼티는 prototype이다. (아놔 둘다 p로 시작해서 헷갈려용~!!!!)
이 prototype은 생성자 함수가 생성할 인스턴스들의 프로토타입 객체를 가리킨다.
즉, Person.prototype은 new Person()으로 생성된 객체의 부모(프로토타입)가 된다.

**prototype 프로퍼티는 왜 있는 걸까?**
함수는 일반 함수로도 쓰일 수 있고, 생성자 함수로도 쓰일 수 있다.
만약 생성자 함수로 호출된다면(`new`), 그 함수는 인스턴스와 연결될 프로토타입 객체가 필요하다.
이때 연결고리 역할을 하는 게 바로 **함수 객체의 prototype 프로퍼티**다.

**인스턴스와 프로토타입 연결 과정은 어떻게 될까?**
생성자 함수를 정의하면, JS 엔진은 자동으로 prototype 객체를 하나 만든다.
new 키워드로 인스턴스를 생성할 때, 그 인스턴스의 `[[Prototype]]` 슬롯은 이 prototype 객체를 참조하게 된다.

```js
function Person(name) {
  this.name = name;
}

console.log(Person.prototype);
// { constructor: f Person(), __proto__: Object.prototype }
```

위 예제에서 Person.prototype은 자동으로 생성된 프로토타입 객체를 가리킨다.
이 객체에는 기본적으로 constructor 프로퍼티가 하나 들어있다.

함수만 prototype 프로퍼티를 갖는 이유는 무엇일까?
객체 리터럴 {}이나 배열 [] 같은 건 prototype 프로퍼티가 없다.
즉, **함수만 갖는다**.
왜냐하면 함수만이 생성자 역할을 해서 새로운 객체를 만들어내고, 그 객체와 연결될 프로토타입이 필요하기 때문이다.

```js
const obj = {};
console.log(obj.prototype); // undefined

function Foo() {}
console.log(Foo.prototype); // { constructor: f Foo() }
```

**✨ Summary**
· prototype 프로퍼티는 **함수 객체에만 존재**한다.
· 이 프로퍼티는 생성자 함수로부터 만들어질 **인스턴스들의 프로토타입 객체**를 가리킨다.
· 따라서 인스턴스는 자신의 `[[Prototype]]`을 통해 생성자 `.prototype`에 연결된다.
· 일반 객체, 배열에는 prototype 프로퍼티가 없다.

### 19.3.3 프로토타입의 constructor 프로퍼티와 생성자 함수

모든 프로토타입 객체는 기본적으로 constructor 프로퍼티를 가진다.
이 constructor 프로퍼티는 그 프로토타입을 참조하고 있는 생성자 함수를 가리킨다.
즉, "누가 나를 만들었는지"를 가리키는 **자기 참조** 같은 것!

**프로토타입의 기본 구조는 어떤 모습일까?**
생성자 함수가 정의되면 엔진은 자동으로 프로토타입 객체를 만들고, 거기에 constructor 프로퍼티를 추가한다.

```js
function Person(name) {
  this.name = name;
}

// Person.prototype 객체 내부 구조
console.log(Person.prototype);
// { constructor: f Person(), __proto__: Object.prototype }
```

`Person.prototype.constructor`는 Person 함수를 가리킨다.
이 연결을 통해 인스턴스에서 다시 자신의 생성자 함수를 역참조할 수 있다.

**인스턴스와 생성자 함수 연결은 어떻게 할까?**

```js
const me = new Person('Hyojung');

console.log(me.constructor === Person); // true
```

me 인스턴스는 직접 constructor 프로퍼티를 가지고 있지 않다.
대신 프로토타입(`Person.prototype`)에 있는 constructor 프로퍼티를 상속받아 접근하는 것이다.
이 덕분에 인스턴스로부터 "내가 어떤 생성자 함수로 만들어졌는지"를 확인할 수 있다.

**⚠️ 주의할 점!**
만약 개발자가 프로토타입 객체를 임의로 덮어씌우면(constructor를 포함하지 않은 새 객체로 교체), constructor 연결이 끊어진다.
이런 경우, 필요하다면 constructor를 다시 명시적으로 복구해주어야 한다.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 객체를 교체
Person.prototype = {
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};

const me = new Person('Hyojung');

console.log(me.constructor === Person); // false → Object
```

constructor가 끊겨서 me.constructor는 Person이 아니라 기본값 Object를 가리키게 된다.

따라서 프로토타입 교체 시에는 보통 constructor를 복원해준다. 아래 코드처럼 말이다.

```js
Person.prototype = {
  constructor: Person, // 명시적 복원
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};
```

**✨ Summary**
· 프로토타입 객체는 기본적으로 constructor 프로퍼티를 가진다.
· constructor는 그 프로토타입을 참조하는 **생성자 함수**를 가리킨다.
· 따라서 인스턴스는 instance.constructor를 통해 자신의 생성자 함수를 알 수 있다.
· 단, 프로토타입을 교체하면 이 연결이 깨질 수 있으므로 주의해야 한다.

### 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

객체, 배열, 함수 같은 값들은 리터럴 표기법(리터럴 문법)으로도 생성할 수 있다.
리터럴 표기법으로 생성된 객체는 문법적으로 `new 생성자함수()`를 실행하지 않는다.
즉, 엔진 내부적으로 최적화된 방식으로 객체를 만들 뿐이지, 진짜로 생성자 함수를 호출해서 만드는 건 아니다.
하지만 이렇게 생성된 객체도 결국 해당 생성자 함수와 연결된 프로토타입을 갖는다.
그래서 겉보기에는 `new Object()`나 `{}`로 만든 객체가 거의 동일하게 동작한다.

**객체 리터럴**

```js
const obj = {};
```

이건 내부적으로 Object 생성자 함수를 직접 호출하지 않는다.
엔진이 "객체 리터럴을 만나면 곧바로 객체를 만들어라"라는 최적화 경로를 따르는 것이다.
하지만 생성된 객체는 Object.prototype을 프로토타입으로 연결한다.

```js
console.log(obj.constructor === Object); // true
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

**배열 리터럴**

```js
const arr = [1, 2, 3];
```

이 역시 `new Array(1, 2, 3)`을 호출하는 것이 아니라, 배열 리터럴 문법을 해석해서 엔진이 곧바로 배열 객체를 만든다.
다만 결과적으로 이 배열의 프로토타입은 `Array.prototype`이다.

```js
console.log(arr.constructor === Array); // true
console.log(Object.getPrototypeOf(arr) === Array.prototype); // true
```

**함수 리터럴**

```js
function foo() {}
```

함수 리터럴로 사실상 Function 생성자 함수를 호출한 건 아니다.
엔진이 함수 객체를 직접 생성한 것이다.
하지만 생성된 함수의 프로토타입은`Function.prototype`이다.

```js
console.log(foo.constructor === Function); // true
console.log(Object.getPrototypeOf(foo) === Function.prototype); // true
```

일상생활에서 비유해보면,
리터럴 표기법은 마트에서 바로 사는 즉석 상품과 비슷하다.
`new Object()`, `new Array()`는 공장 생산품이다.
둘 다 사실 같은 공장에서 나왔고 품질도 동일하다. 그냥 껍데기(문법)만 다를 뿐*!*

**✨ Summary**
· 리터럴 표기법 = 생성자 함수 직접 호출 아님!
· 하지만 리터럴 표기법으로 만든 객체도 결국 해당 생성자 함수와 연결된 프로토타입을 공유한다.
· 그래서 `{}`로 만든 객체와 `new Object()`로 만든 객체는 결과적으로 똑같이 `Object.prototype`을 프로토타입으로 가진다.

한 줄 요약 : "리터럴 표기법은 생성자 함수를 직접 호출하지 않지만, 결국 동일한 프로토타입 체계를 따른다."

## 19.5 프로토타입의 생성 시점

**프로토타입 객체는 "언제" 만들어질까?**
두 가지 경우로 나뉘는데,
1️⃣ 사용자 정의 생성자 함수 → 함수가 정의되는 순간 프로토타입 객체가 함께 생성된다.
2️⃣ 빌트인 생성자 함수(Object, Array, Function 등) → JS 엔진이 구동될 때 미리 생성된다.

### 19.5.1 사용자 정의 생성자 함수와 프로토타입 생성 시점

사용자가 function Foo() {} 같은 함수를 선언하면, JS 엔진은 함수 객체를 만들고, 동시에 Foo.prototype이라는 프로토타입 객체도 자동으로 생성한다.
이 프로토타입 객체는 constructor 프로퍼티 하나만 가진 상태로 시작한다.

```js
function Person(name) {
  this.name = name;
}

console.log(Person.prototype);
// { constructor: f Person(), __proto__: Object.prototype }
```

즉, 생성자 함수는 정의되는 즉시 자기 전용 프로토타입을 보장받는다.
이후 new Person()을 호출하면, 생성된 인스턴스의 [[Prototype]] 슬롯이 Person.prototype을 참조한다.

예를 들면,
새로운 선생님(Person 함수)이 발령 나면, 동시에 교과서(Person.prototype)도 자동으로 배포되는 것으로 생각해볼 수 있따.
학생(인스턴스)이 생기면 그 교과서를 자동으로 공유받는다.

### 19.5.2 빌트인 생성자 함수와 프로토타입 생성 시점

Object, Array, Function 같은 빌트인 생성자 함수들은 JS 엔진이 실행될 때(런타임 시작 시) 미리 만들어진다.
이와 동시에 이들에 연결된 프로토타입 객체(Object.prototype, Array.prototype, Function.prototype)도 함께 생성된다.
이 덕분에 우리가 코드에서 곧바로 리터럴을 써도 문제없이 프로토타입 체계가 동작하는 것!

```js
console.log(Object.prototype);
console.log(Array.prototype);
console.log(Function.prototype);
```

모든 객체의 조상 역할을 하는 Object.prototype은 가장 먼저 만들어지고, 이후 다른 빌트인 프로토타입들이 이걸 상속받는 구조로 세팅된다.

**✨ Summary**
· 사용자 정의 생성자 함수 → 함수가 정의될 때 프로토타입 객체 자동 생성함!
· 빌트인 생성자 함수 → 엔진 실행 시점에 미리 생성자 함수와 프로토타입 객체가 준비됨!
· 따라서 모든 객체는 생성 순간부터 "누구의 프로토타입을 참조할지"가 이미 결정되어 있다.

## 19.6 객체 생성 방식과 프로토타입의 결정

JS에서 객체는 여러 가지 방식으로 생성할 수 있다.
또한 생성 방식에 따라 연결되는 프로토타입이 달라진다.
크게 3가지 방식이 있다.
1️⃣ 객체 리터럴
2️⃣ Object 생성자 함수
3️⃣ 사용자 정의 생성자 함수

### 19.6.1 객체 리터럴에 의해 생성된 객체의 프로토타입

리터럴은 `new Object()`를 "호출하지 않는다".
대신 엔진이 `OrdinaryObjectCreate` 같은 추상 연산으로 바로 객체를 만든다.
그럼에도 결과 객체의 `[[Prototype]]`은 항상 `Object.prototype`을 가리킨다.

```js
const obj = { x: 1 };

console.log(obj.constructor === Object); // true
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

리터럴로 생성된 객체도 결국 Object.prototype을 상속한다.

### 19.6.2 Object 생성자 함수

```js
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'); // true
```

Object 생성자 함수를 명시적으로 호출해도 객체가 생성된다.
이 객체 역시 `Object.prototype`을 프로토타입으로 갖는다.
따라서 객체 리터럴과 `new Object()` 방식은 프로토타입 관점에서는 동일하다.

편의점에서 산 라면이나(리터럴), 본사에 주문한 라면이나(`new Object()`), 결국 같은 라면 공장(프로토타입)에서 나온다.

### 19.6.3 생성자 함수에 의해 생성된 객체의 프로토타입

사용자 정의 생성자 함수로 객체를 생성하면, 인스턴스의 `[[Prototype]]`은 그 생성자 함수의 prototype 프로퍼티를 참조한다.
그리고 이 prototype 객체의 `[[Prototype]]`은 `Object.prototype`을 가리키기 때문에, 인스턴스는 결국 Object의 빌트인 메서드까지 상속받는다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// me 인스턴스에는 name 프로퍼티만 있음
console.log(me); // Person { name: "Lee" }

// 하지만 Object.prototype의 메서드도 사용 가능
console.log(me.hasOwnProperty('name')); // true
console.log(me.propertyIsEnumerable('name')); // true
console.log(me.toString()); // [object Object]
```

1. `new Person('Lee')` 실행 → 새로운 객체 생성
   · `me.[[Prototype]]`은 `Person.prototype`을 참조.
2. `Person.prototype`은 기본적으로 빈 객체 `{}`와 같음
   · 단, `constructor` 프로퍼티를 가지고 있고
   · 그 `[[Prototype]]`은 `Object.prototype`을 가리킴.
3. 따라서 me에서 프로퍼티/메서드를 찾는 순서:
   · me 객체 자체 → `Person.prototype` → `Object.prototype`
4. 그 결과, `Object.prototype`에 정의된 빌트인 메서드
   (`hasOwnProperty`, `propertyIsEnumerable`, `toString` 등)도 me에서 사용 가능하다.

## 19.7 프로토타입 체인 (핵심 ⭐️‼️)

JS 객체는 자신의 프로토타입에 연결된 다른 객체를 순차적으로 따라 올라가며 프로퍼티/메서드를 탐색한다.
이 연결 구조를 **프로토타입 체인(prototype chain)**이라고 한다.
결국, 모든 객체는 `Object.prototype`을 정점으로 하는 체인 구조에 포함된다.

**프로토타입 체인의 동작 방식**

1. 객체에서 프로퍼티나 메서드를 참조할 때!
   · 먼저 객체 자신의 프로퍼티를 확인한다.
   · 없다면 `[[Prototype]]`에 연결된 프로토타입 객체를 확인한다.
   · 그래도 없으면 그 위 프로토타입을 계속 따라 올라간다.
   · 최종적으로 `Object.prototype`까지 가서도 없으면 `undefined` 반환.
2. `Object.prototype`도 `[[Prototype]]`을 가지고 있는데, 이 값은 null이다.
   · 따라서 탐색은 `null`에서 멈춘다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! I'm ${this.name}`);
};

const me = new Person('Lee');

// 1. me 객체 자신에게 name 있음
console.log(me.name); // "Lee"

// 2. me에는 sayHello 없음 → Person.prototype에서 검색
me.sayHello(); // Hi! I'm Lee

// 3. toString은 어디서 오나?
console.log(me.toString()); // [object Object]
// me → Person.prototype → Object.prototype에서 발견
```

me 객체는 직접 가진 프로퍼티(name)뿐만 아니라,
`Person.prototype`에 정의된 메서드(`sayHello`),
`Object.prototype`에 정의된 메서드(`toString`)까지 사용 가능하다.

**✨ Summary**
· 프로토타입 체인은 자바스크립트의 상속 메커니즘이다.
· 객체는 프로퍼티를 찾을 때 자신의 프로토타입을 따라 체인식으로 검색한다.
· 최종 도착지는 `Object.prototype`, 그 위는 null.
· 이 구조 덕분에 모든 객체가 공통 기능(`toString`, `hasOwnProperty` 등)을 공유할 수 있다.

## 19.8 오버라이딩과 프로퍼티 섀도잉 (핵심 ⭐️‼️)

객체가 프로토타입 체인을 따라 올라가며 프로퍼티를 찾을 때, **자신이 가진 프로퍼티가 우선**된다.
만약 같은 이름의 프로퍼티가 프로토타입에도 있으면, 프로토타입의 프로퍼티는 가려진다(=섀도인 Shadowing).
이렇게 "자신이 직접 같은 이름의 프로퍼티를 갖게 해서 프로토타입의 것을 덮어쓰는 것"을 오버라이딩(overriding)이라고 한다.

**오버라이딩과 섀도잉의 동작 방식**

1. 프로퍼티 읽기 시
   · 객체 자신의 프로퍼티 → 최우선.
   · 없으면 프로토타입 체인 검색.
2. 프로퍼티 쓰기 식
   · 객체 자신의 프로퍼티에 기록한다.
   · 프로토타입에 같은 이름이 있더라도, 덮어쓰지 않고 객체 자신에게 새로 만든다.
3. 따라서 프로토타입의 프로퍼티는 여전히 남아 있지만, 인스턴스의 같은 이름 프로퍼티 때문에 가려져 접근할 수 없게 된다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! I'm ${this.name}`);
};

const me = new Person('Lee');

// 원래는 프로토타입 메서드 호출
me.sayHello(); // Hi! I'm Lee

// 오버라이딩: 인스턴스에 같은 이름 메서드 추가
me.sayHello = function () {
  console.log(`Hello! My name is ${this.name}`);
};

me.sayHello(); // Hello! My name is Lee

// 프로토타입 메서드는 가려졌을 뿐 여전히 존재
Person.prototype.sayHello.call(me); // Hi! I'm Lee
```

여기서 `me.sayHello`는 프로토타입의 `sayHello`를 덮어쓴 게 아니라, 인스턴스 자신에게 새로 정의한 것!
결과적으로 프로토타입의 원본 메서드는 섀도잉되어 접근할 수 없게 된 것이다.

**✨ Summary**
· 오버라이딩: 인스턴스가 프로토타입과 동일한 이름의 프로퍼티/메서드를 가질 때, 인스턴스의 것이 우선 적용됨!
· 프로퍼티 섀도잉: 프로토타입의 프로퍼티가 가려져 접근할 수 없게 되는 현상.
· ⚠️ 주의할 점: 프로토타입의 값이 수정된 게 아니라 단지 가려진 것일 뿐이다!

## 19.9 프로토타입의 교체

JS에서는 생성자 함수의 prototype 프로퍼티나, 인스턴스의 `__proto__` 접근자를 이용해서 프로토타입 객체를 교체할 수 있다.
하지만 이 경우 constructor 연결이 깨지거나, 인스턴스와 생성자 간의 관계가 이상해지는 부작용이 생길 수 있다.

### 19.9.1 생성자 함수에 의한 프로토타입의 교체

보통 생성자 함수를 정의하면, 엔진이 자동으로 `{ constructor: F }` 구조를 가진 프로토타입 객체를 붙여준다.
그런데 개발자가 `F.prototype = { ... }`처럼 새 객체로 통째로 덮어씌우면, 기본으로 있던 constructor 프로퍼티가 사라진다.
그 결과, 새로 생성된 인스턴스의 constructor는 원래의 생성자가 아닌 Object를 가리키게 된다.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 교체
Person.prototype = {
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};

const me = new Person('Lee');

console.log(me.constructor === Person); // false
console.log(me.constructor === Object); // true
```

`Person.prototype`을 새 객체로 교체했기 때문에, constructor 연결이 끊어진 상태!

프로토타입을 교체할 때는 constructor를 다시 명시적으로 연결해줘야 한다.

```js
Person.prototype = {
  constructor: Person, // 복원
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};

const me2 = new Person('Kim');
console.log(me2.constructor === Person); // true
```

### 19.9.2 인스턴스에 의한 프로토타입의 교체

인스턴스가 가진 `__proto__` 접근자(= `[[Prototype]]`)를 이용해 프로토타입을 직접 교체할 수도 있다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 새로운 프로토타입 객체
const parent = {
  sayHello() {
    console.log(`Hello! My name is ${this.name}`);
  },
};

// 인스턴스의 프로토타입 교체
Object.setPrototypeOf(me, parent);

me.sayHello(); // Hello! My name is Lee
```

이렇게 하면 인스턴스가 원래 참조하던 `Person.prototype` 대신 새로운 객체(parent)를 프로토타입으로 삼는다.
하지만 이 방법은 런타임에 동적으로 프로토타입 체인을 바꾸는 것이므로 **성능에 좋지 않고**, **코드 일관성을 깨뜨릴 수 있다**.

**✨ Summary**
· 프로토타입 교체는 생성자 함수 단위로도, 인스턴스 단위로도 가능하다.
· 그러나 교체 과정에서 constructor 연결이 끊기거나, 인스턴스가 생성자와의 관계를 잃어버릴 수 있다.
· 따라서 프로토타입 교체는 가급적 지양하고, 꼭 필요하다면 constructor를 명시적으로 복구해야 한다.

## 19.10 `instanceof` 연산자

`instanceof`는 객체가 특정 생성자 함수와 연결된 인스턴스인지 판별하는 연산자다.
원리: 객체의 프로토타입 체인을 따라가면서 `생성자함수.prototype`이 등장하는지 확인한다.
즉, constructor 프로퍼티와는 전혀 상관없다.

**동작 방식**

```js
객체 instanceof 생성자함수;
```

객체의 `[[Prototype]]` 체인을 따라가며 `생성자함수.prototype`이 발견되면 `true`, 끝까지 못 찾으면 `false`를 반환한다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

console.log(me instanceof Person); // true
console.log(me instanceof Object); // true
```

1. `me.[[Prototype]]` → `Person.prototype` 발견 → `true`
2. `me.[[Prototype]].[[Prototype]]` → `Object.prototype` 발견 → `true`

**`constructor` 프로퍼티와 차이**

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 교체
Person.prototype = {
  sayHello() {
    console.log('hi');
  },
};

const me = new Person('Lee');

console.log(me.constructor === Person); // false ❌
console.log(me.constructor === Object); // true

console.log(me instanceof Person); // true ✅
```

`Person.prototype`을 새 객체로 교체하면서, 원래 자동 추가된 `constructor: Person` 연결이 끊겼다.
그래서 `me.constructor`는 Object를 가리킨다.
하지만 `instanceof`는 constructor 프로퍼티가 아니라 프로토타입 체인만 보기 때문에 여전히 `true`.

**✨ Summary**
· instanceof는 객체의 constructor 프로퍼티와 무관하다.
· 오직 프로토타입 체인에 `생성자.prototype`이 존재하는지만 본다.
· 따라서 constructor가 깨져도 instanceof 판별은 영향받지 않는다.
· 단, 프로토타입 자체를 교체하면 기존 인스턴스와 새 인스턴스가 서로 다른 체인을 따르게 되므로 결과가 달라질 수 있다.

## 19.11 직접 상속

JS는 생성자 함수를 거치지 않고도, 객체를 직접 다른 객체를 프로토타입으로 삼아 상속시킬 수 있다.
대표적인 방법은 두 가지가 있다.
1️⃣ `Object.create` 메서드
2️⃣ 객체 리터럴 내부에서 `__proto__` 지정

### 19.11.1 `Object.create`에 의한 직접 상속

**개념**
`Object.create(proto, propertiesObject)`는 새로운 객체를 생성하면서, 지정한 proto 객체를 그 객체의 프로토타입으로 설정한다.
즉, 생성자 함수를 거치지 않고 직접 상속 관계를 설정할 수 있는 방법이다.
두 번째 인자로는 프로퍼티 정의(= `Object.defineProperties` 동작과 동일)를 전달할 수 있다.

```js
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
```

`Object.create(null)`은 프로토타입이 없는 순수한 빈 객체를 만든다.
이 객체는 `Object.prototype`을 상속받지 않으므로, `toString` 같은 빌트인 메서드도 사용할 수 없다.

```js
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

여기서는 명시적으로 `Object.prototype`을 상속받음.

```js
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
});
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

두 번째 인자를 주면 프로퍼티까지 세밀하게 정의할 수 있다.
내부적으로 `Object.defineProperties`가 사용된다.

```js
const myProto = { x: 10 };

obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

이렇게 하면 obj의 프로토타입이 myProto로 지정된다.
따라서 obj는 myProto를 상속받아 x 프로퍼티를 사용할 수 있다.

```js
const obj = { a: 1 };

console.log(obj.hasOwnProperty('a')); // true
console.log(obj.propertyIsEnumerable('a')); // true
```

일반 객체는 `Object.prototype`을 상속하므로, `hasOwnProperty` 같은 메서드를 사용할 수 있다.

```js
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true
console.log(obj.hasOwnProperty('a')); // TypeError!
```

프로토타입이 없는 객체는 `Object.prototype` 메서드들을 상속받지 못한다.
따라서 `hasOwnProperty`를 직접 호출하면 에러 발생.
대신 `Object.prototype.hasOwnProperty.call(obj, 'a')` 처럼 빌려 써야 한다.

```js
function Person(name) {
  this.name = name;
}

const me = Object.create(Person.prototype);
me.name = 'Lee';

console.log(me.name); // Lee
console.log(Object.getPrototypeOf(me) === Person.prototype); // true
```

생성자 함수를 호출하지 않고도, `Object.create`로 직접 인스턴스 비슷한 객체를 만들 수 있다.

**✨ Summary**
· `Object.create`는 객체를 만들면서 원하는 프로토타입을 직접 지정할 수 있는 강력한 방법이다.
· `Object.create(null)`은 프로토타입 없는 객체를 만들어 완전한 해시 맵처럼 사용할 때 유용하다.
· 두 번째 인자는 내부적으로 `Object.defineProperties`가 호출되므로, 프로퍼티를 세밀하게 정의할 수 있다.
· 단, 프로토타입을 `null`로 하면 `Object.prototype`의 메서드를 못 쓰므로 주의해야 한다.

## 19.12 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 생성자 함수 자신에게 직접 추가되는 속성이다.
인스턴스가 아닌 생성자 함수에서만 접근할 수 있다.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티/메서드
Person.staticProp = 'static prop';
Person.staticMethod = function () {
  console.log('static method');
};

const me = new Person('Lee');

Person.staticMethod(); // static method
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

즉, 정적 메서드는 인스턴스가 상속받지 않는다.

## 19.13 프로퍼티 존재 확인

### 19.13.1 `in` 연산자

객체에 해당 프로퍼티가 존재하는지 확인 (상속받은 프로퍼티까지 포함).

```js
const person = { name: 'Lee', address: 'Seoul' };

console.log('name' in person); // true
console.log('address' in person); // true
console.log('age' in person); // false
```

`toString` 같은 메서드도 확인됨 → `Object.prototype`에 존재하기 때문.

```js
console.log('toString' in person); // true
```

### 19.13.2 `Object.prototype.hasOwnProperty`

상속받은 프로퍼티는 제외하고, 자신의 프로퍼티만 확인.

```js
console.log(person.hasOwnProperty('name')); // true
console.log(person.hasOwnProperty('age')); // false
console.log(person.hasOwnProperty('toString')); // false
```

## 19.14 프로퍼티 열거

### 19.14.1 `for...in` 문

객체의 모든 열거 가능한 프로퍼티를 순회. (상속받은 프로퍼티도 포함)

```js
const person = { name: 'Lee', address: 'Seoul' };

for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

`toString` 같은 비열거 프로퍼티는 제외됨.

**열거와 관련된 특징**

1. 상속받은 프로퍼티도 열거됨.

```js
console.log('toString' in person); // true
for (const key in person) console.log(key);
// name
// address
// (toString은 안 나옴 → enumerable: false)
```

2. `for...in`은 키가 Symbol인 프로퍼티는 열거하지 않음!

```js
const sym = Symbol();
const obj = { a: 1, [sym]: 10 };

for (const key in obj) console.log(key); // a
```

3. 배열에 `for...in` 쓰면 권장되지 않는다. (순서 보장 안 되기 때문)

### 19.14.2 `Object.keys/values/entries`

`for...in`은 상속받은 프로퍼티까지 순회하지만,
`Object.keys` / `Object.values` / `Object.entries`는 자신의 프로퍼티만 반환한다.

```js
const person = { name: 'Lee', address: 'Seoul' };

console.log(Object.keys(person)); // ["name", "address"]
console.log(Object.values(person)); // ["Lee", "Seoul"]
console.log(Object.entries(person));
// [["name", "Lee"], ["address", "Seoul"]]

Object.entries(person).forEach(([key, value]) => console.log(key, value));
// name Lee
// address Seoul
```

## 🔥 Overall Summary

· `__proto__`를 객체 리터럴 안에서 직접 써서 프로토타입을 지정할 수 있다. (19.11.2)
· 정적 메서드/프로퍼티는 생성자 함수에 직접 추가되는 것으로, 인스턴스는 접근할 수 없다. (19.12)
· 프로퍼티 존재 확인 방법
1️⃣ `in` 연산자 : 상속 포함
2️⃣ `hasOwnProperty` : 자신의 프로퍼티만
· 프로퍼티 열거 방법
1️⃣ `for...in` : 상속 포함, `enumerable`만
2️⃣ `Object.keys` / `Object.values` / `Object.entries` : 자신의 프로퍼티만, 배열로 반환

---

스터디 정리하다가 디질뻔
