## 클래스란 무엇일까?

먼저 이 문장부터 쉽게 풀어보자: **"ES6에서 도입된 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 익숙한 클래스 기반 객체지향 프로그래밍보다 익숙한 클래스 기반 객체지향 프로그래밍에 가까운 새로운 객체 생성 매커니즘을 제시한다."**

이게 무슨 말이냐면, JavaScript에 클래스가 생기기 전에는 객체를 만드는 방법이 다른 언어들과 달라서 어려웠는데, **ES6**에서 클래스가 생기면서 다른 언어처럼 익숙한 방식으로 객체를 만들 수 있게 되었다는 뜻이다.

### 💭 "프로토타입 기반"과 "클래스 기반"은 뭐가 다를까?

✔️ **다른 언어들의 "클래스 기반" 방식** (Java, C++ 등)

```java
// java 예시 - 직관적이고 이해하기 쉬움
class Person {
	String name;

    Person(String name) { // 생성자로 객체 초기화❗️
    	this.name = name;
    }

    void sayHi() { // 메서드 정의❗️
    	System.out.println("Hi! " + name);
    }
}

Person me = new Person("Lee"); // 클래스로 객체 생성❗️
```

_**"설계도(클래스) 만들고 → 설계도로 제품(객체) 생산"**_

✔️ **JavaScript의 기존 "프로토타입 기반" 방식** (ES5까지)

```js
function Person(name) {
  this.name = name; // 생성자 함수에서 프로퍼티 설정❗️
}

// 메서드는 따로 프로토타입에 추가해야 함❗️ (번거로움)
Person.prototype.sayHi = function () {
  console.log(`Hi! ` + this.name);
};

var me = new Person('Lee'); // 복잡한 과정을 거쳐 객체 생성❗️
```

_**"생성자 함수 만들고 → 프로토타입에 메서드 추가하고 → 객체 생성" (여러 단계 필요)**_

✔️ **JavaScript ES6의 "클래스" 방식**

```js
// JavaScript ES6 - Java처럼 익숙함!
class Person {
  constructor(name) {
    // 생성자에서 프로퍼티 설정❗️
    this.name = name;
  }

  sayHi() {
    // 메서드도 클래스 안에서 한 번에 정의❗️
    console.log(`Hi! ${this.name}`);
  }
}

const me = new Person('Lee'); // 간단하게 객체 생성❗️
```

_**"다른 언어처럼 클래스 만들고 → 클래스로 객체 생산" (한 번에 완성! 🎉)**_

### 핵심 : "새로운 객체 생성 매커니즘"이란?

비유로 설명하면:

- **기존 방식**: 부품을 하나씩 조립해서 자동차를 만드는 수제 공방 (복잡 😩)
- **새로운 방식**: 완성된 자동차 공장에서 버튼 하나로 자동차를 만드는 방식 (간단 😌)

하지만 클래스가 등장했다고 해서 JavaScript가 갑자기 클래스 기반 객체지향 언어가 된 건 아니다. 여전히 프로토타입 기반 객체지향 언어다. 그럼 클래스는 정확히 뭘까?

**클래스를 쉽게 비유하면 "붕어빵 틀"이라고 생각해볼 수 있다.** 붕어빵 틀(클래스)은 그 자체로는 먹을 수 없지만, 이 틀을 사용해서 실제 붕어빵(인스턴스)들을 만들어낸다. 모든 붕어빵은 같은 모양이지만, 각각 다른 팥(프로퍼티)이 들어갈 수 있다.

클래스는 생성자 함수와 비슷하게 동작하지만 몇 가지 차이가 있다. 그리고 무엇보다 중요한 건, 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 "문법적 설탕"이라고 보기에는 클래스와 생성자 함수는 정확히 동일하게 동작하지 않는다는 점이다.

---

## 25.1 클래스는 프로토타입의 문법적 설탕인가?

JavaScript는 프로토타입 기반 객체지향 언어다. 프로토타입 기반 객체지향 언어는 클래스가 필요 없는 객체지향 프로그래밍 언어다. ES5에서는 클래스 없이도 다음과 같이 생성자 함수와 프로토타입을 통해 객체지향 언어의 상속을 구현할 수 있다.

```js
// ES5 생성자 함수
var Person = (function () {
  // 즉시 실행 함수로 감싸서 캡슐화 💊
  function Person(name) {
    this.name = name; // 각 인스턴스마다 고유한 name 프로퍼티를 가짐
  }

  // 프로토타입 메서드 - 모든 인스턴스가 공유하는 메서드
  Person.prototype.sayHi = function () {
    console.log('Hi! My name is ' + this.name);
  };

  // 생성자 함수 반환
  return Person;
})();

// 인스턴스 생성 - Person 틀로 'Lee'라는 이름을 가진 인스턴스 생성❗️
var me = new Person('Lee'); // Person 틀로 'Lee'라는 이름을 가진 인스턴스 생성
me.sayHi(); // Hi! My name is Lee
```

보기만 해도 복잡하다. 🤯 즉시 실행 함수로 감싸고, 생성자 함수 따로 만들고, 프로토타입에 메서드 따로 추가하고... **마치 레고 블록을 하나씩 조립하는 기분이다.** 여기서 중요한 건 `Person`이라는 즉시 실행 함수 안에서 실제 생성자 함수를 정의하고, 프로토타입에 공통 메서드를 추가한 다음, 그 생성자 함수를 반환한다는 점이다.
이렇게 해야만 모든 Person 인스턴스들이 같은 sayHi 메서드를 공유할 수 있다.

하지만 클래스 기반 언어에 익숙한 프로그래머들에게는 프로토타입 기반 프로그래밍 방식이 혼란스러울 수 있다. 그래서 **ES6**에서 클래스가 도입되었다*!* 🎉

```js
// ES6 클래스
class Person {
  // 생성자 - 깔끔하게 한 곳에서 정의 😌
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // 'new Person('Lee')를 호출하면 이 부분이 실행됨
  }

  // 프로토타입 메서드 - 자동으로 Person.prototype에 추가됨
  sayHi() {
    console.log(`Hi! My name is ${this.name}.`);
  }
}

// 인스턴스 생성 - constructor가 호출되어 this.name = 'Lee'가 실행됨❗️
const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee.
```

**훨씬 깔끔하다*!*** 🤩 비유로 설명하면, **ES5** 방식은 마치 수제 공방에서 하나하나 수작업으로 도구를 만들고 작업대를 설치하는 것이라면, **ES6** 클래스는 이미 완성된 제조 공장에서 간단한 설정만 하면 바로 제품을 만들어내는 것과 같다.

하지만 여기서 중요한 질문이 생긴다.
클래스가 기존 프로토타입 방식을 예쁘게 포장한 "~~문법적 설탕~~"일까? 아니면 정말로 새로운 기능일까?

정답은 **"새로운 기능"**이다. 클래스와 생성자 함수는 비슷해 보이지만 실제로는 꽤 다르게 동작한다.

### 클래스와 생성자 함수의 차이점

**마치 "엄격한 공장"과 "느슨한 작업장"의 차이라고 보면 된다:**

1. **클래스는 new 연산자 없이 호출하면 에러가 발생한다.** 하지만 생성자 함수를 new 연산자 없이 호출하면 일반 함수로서 호출된다.

```js
class StrictFactory {}
function LooseWorkshop() {}

// new StrictFactory(); // 정상 동작
// StrictFactory(); // TypeError 발생 - 공장은 반드시 제품 생산 모드로만 작동!

// new LooseWorkshop(); // 정상 동작
// LooseWorkshop(); // 그냥 일반 함수로 실행됨 - 작업장은 유연하게 작동!
```

**이건 안전장치 같은 개념이다.** 클래스는 "나는 객체를 만드는 용도야!"라고 명확히 선언하고, 다른 용도로 사용하려고 하면 아예 막아버린다.

2. **클래스는 상속을 지원하는 extends와 super 키워드를 제공한다.** 하지만 생성자 함수는 extends와 super 키워드를 지원하지 않는다.

3. **클래스는 호이스팅이 발생하지 않는 것처럼 동작한다.** 하지만 **함수 선언문**으로 정의된 생성자 함수는 *함수 호이스팅*이, **함수 표현식**으로 정의한 생성자 함수는 *변수 호이스팅*이 발생한다.

4. **클래스 내의 모든 코드에는 암묵적으로 strict mode가 지정되어 실행되며 strict mode를 해제할 수 없다.** 하지만 생성자 함수는 암묵적으로 strict mode가 지정되지 않는다. 마치 공장 내에서는 항상 안전 규정을 지켜야 하는 것과 같다.

5. **클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false다.** 즉, 열거되지 않는다. 이것도 캡슐화를 위한 설계다.

이런 차이점들을 보면, 클래스는 단순히 "기존 방식을 예쁘게 포장한 것"이 아니라 **완전히 새로운 객체 생성 시스템**이라는 걸 알 수 있다. 더 안전하고, 더 명확하고, 더 강력한 기능들을 제공한다.

---

## 25.2 클래스 정의

클래스는 **class** 키워드를 사용하여 정의한다. 클래스 이름은 생성자 함수와 마찬가지로 **파스칼 케이스**를 사용하는 것이 일반적이다.

```js
// 클래스 선언문 - 마치 "공장 설계도"를 만드는 것
class Person {}
```

일반적이지는 않지만 함수와 마찬가지로 **표현식**으로 클래스를 정의할 수도 있다. 이때 클래스는 함수와 마찬가지로 이름을 가질 수도 있고, 가지지 않을 수도 있다.

```js
// 익명 클래스 표현식 - 이름 없는 공장
const Person = class {};

// 기명 클래스 표현식 - 내부에서는 MyClass로, 외부에서는 Person으로 부름
const Person = class MyClass {};
```

**여기서 재밌는 점이 하나 있다.** 클래스를 표현식으로 정의할 수 있다는 것은 클래스가 값으로 사용할 수 있는 **일급 객체**라는 것을 의미한다. **마치 공장 설계도를 USB에 담아서 어디든 가져갈 수 있다는 것과 같다.** 즉, 클래스는 일급 객체로서 다음과 같은 특징을 갖는다.

- 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
- 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
- 함수의 매개변수에게 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다.

**그런데 더 재밌는 사실은, 클래스는 사실 함수다*!***
따라서 클래스는 값처럼 사용할 수 있는 일급 객체다.

클래스 몸체에는 0개 이상의 메서드만 정의할 수 있다.
클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드 세 가지다.

```js
// 클래스 선언문
class Person {
  // 생성자 - "제품 조립 매뉴얼"
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // name 프로퍼티는 public하다.
  }

  // 프로토타입 메서드 - "모든 제품이 공통으로 사용하는 기능"
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 정적 메서드 - "공장 자체의 기능 (제품과는 무관)"
  static sayHello() {
    console.log('Hello!');
  }
}

// 인스턴스 생성 - 공장에서 'Lee'라는 이름표를 단 제품 생산
const me = new Person('Lee');

// 인스턴스의 프로퍼티 참조 - 제품의 이름표 확인
console.log(me.name);
// 프로토타입 메서드 호출 - 제품의 기능 사용
me.sayHi(); // Hi! My name is Lee
// 정적 메서드 호출 - 공장 자체의 기능 사용 (제품 없이도 가능)
Person.sayHello(); // Hello!
```

**이 세 가지 메서드는 각각 다른 역할을 한다.** constructor는 객체가 만들어질 때의 초기 설정을, 프로토타입 메서드는 모든 인스턴스가 공유하는 기능을, 정적 메서드는 클래스 자체의 기능을 담당한다.

---

## 25.3 클래스 호이스팅

클래스는 **함수**로 평가된다.

```js
// 클래스 선언문
class Person {}

console.log(typeof Person); // function - 클래스도 결국 함수!
```

**클래스는 겉보기에는 새로운 문법이지만, 실제로는 "함수라는 기존 재료로 만든 새로운 요리"라고 볼 수 있다.**

클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정, 즉 런타임 이전에 먼저 평가되어 함수 객체를 생성한다. 이때 클래스가 평가되어 생성된 함수 객체는 생성자 함수로서 호출할 수 있는 함수, 즉 **constructor**다. 생성자 함수로서 호출할 수 있는 함수는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다. **프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.**

단, 클래스는 **클래스 정의 이전에 참조할 수 없다.**

```js
console.log(Person);
// ReferenceError: Cannot access 'Person' before initialization

// 클래스 선언문
class Person {}
```

클래스 선언문은 마치 호이스팅이 발생하지 않는 것처럼 보인다. 하지만 그렇지 않다.

```js
const Person = '';

{
  // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
  console.log(Person);
  // ReferenceError: Cannot access 'Person' before initialization

  // 클래스 선언문
  class Person {}
}
```

**이건 마치 영화 예매는 했지만, 상영 시간 전까지는 입장할 수 없는 상황과 같다.**

클래스 선언문도 변수 선언, 함수 정의와 마찬가지로 호이스팅이 발생한다. 단, 클래스는 **let**, **const** 키워드로 선언한 변수처럼 호이스팅된다. 따라서 클래스 선언문 이전에 **TDZ**에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작한다.

**var**, **let**, **const**, **function**, **function\***, **class** 키워드를 사용하여 선언된 모든 식별자는 호이스팅된다. 모든 선언문은 런타임 이전에 먼저 실행되기 때문이다.

---

## 25.4 인스턴스 생성

클래스는 생성자 함수이며 **new** 연산자와 함께 호출되어 인스턴스를 생성한다.
즉, 클래스는 붕어빵 틀이고, **new** 연산자는 붕어빵을 만드는 기계라고 생각하면 된다.

```js
class Person {}

// 인스턴스 생성 - 붕어빵 틀로 붕어빵을 만드는 과정
const me = new Person();
console.log(me); // Person {} - 빈 붕어빵 완성
```

함수는 **new** 연산자의 사용 여부에 따라 일반 함수로 호출되거나 인스턴스 생성을 위한 생성자 함수로 호출되지만, **클래스는 인스턴스를 생성하는 것이 유일한 존재 이유이므로 반드시 new 연산자와 함께 호출해야 한다.**

```js
class Person {}

// 👈 여기! 클래스를 new 연산자 없이 호출하면 타입 에러가 발생한다.
const me = Person();
// TypeError: Class constructor Person cannot be invoked without 'new'
```

이는 마치 **전용 제빵기계는 반드시 '제빵 모드'로만 작동해야 하고, 다른 용도로는 사용할 수 없는 것과 같다.** 클래스는 안전장치가 있어서 실수를 방지해준다.

클래스 표현식으로 정의된 클래스의 경우 다음과 같이 클래스를 가리키는 식별자를 사용해 인스턴스를 생성하지 않고 **기명 클래스 표현식의 클래스 이름을 사용해 인스턴스를 생성하면 에러가 발생한다.**

```js
const Person = class MyClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person(); // Person이라는 변수명으로 접근해야 함

// 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

**이는 마치 회사 내부에서는 '개발팀'이라고 부르지만, 외부에서는 '기술부서'라고 불러야 하는 상황과 같다.**

---

## 25.5 메서드

클래스 몸체에는 0개 이상의 메서드만 정의할 수 있다. **클래스는 마치 "다기능 도구 세트"와 같아서, 각각의 도구(메서드)들이 서로 다른 역할을 한다.** 클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드 세 가지다.

### 25.5.1 constructor

constructor는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다. 마치 "제품 조립 설명서"와 같은 역할을 한다. **constructor는 이름을 변경할 수 없다.**

```js
class Person {
  // 생성자 - 인스턴스가 태어날 때 실행되는 "출생 신고서 작성" 과정
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // 새로 태어난 인스턴스에게 name이라는 이름표를 달아줌
  }
}
```

앞에서 살펴본 바와 같이 클래스는 평가되어 함수 객체가 된다. 클래스도 함수 객체 고유의 프로퍼티를 모두 갖고 있다. 함수와 동일하게 프로토타입과 연결되어 있으며 자신의 스코프체인을 구성한다.

모든 함수 객체가 가지고 있는 prototype 프로퍼티가 가리키는 프로토타입 객체의 constructor 프로퍼티는 클래스 자신을 가리키고 있다. 이는 클래스가 인스턴스를 생성하는 생성자 함수라는 것을 의미한다. **즉, new 연산자와 함께 클래스를 호출하면 클래스는 인스턴스를 생성한다.**

그런데 흥미롭게도 클래스가 생성한 인스턴스를 살펴보면 constructor 메서드가 보이지 않는다. 이는 constructor가 메서드로 해석되는 것이 아니라 **클래스가 평가되어 생성된 함수 객체 코드의 일부가 되기 때문이다.** 다시 말해, 클래스 정의가 평가되면 constructor의 기술된 동작을 하는 함수 객체가 생성된다.

**여기서 신기한 점은, 클래스를 뜯어보면 constructor가 실제로는 보이지 않는다는 것이다.** 왜냐하면 constructor는 메서드가 아니라 클래스가 함수로 평가될 때의 "몸통"이 되기 때문이다. 마치 로봇의 설계도에서 가장 핵심적인 CPU 부분이 로봇 자체가 되는 것과 같다.

**constructor는 생성자 함수와 유사하지만 몇 가지 차이가 있다.**

**constructor는 클래스 내에 최대 한 개만 존재할 수 있다.** 한 사람이 출생신고서를 여러 번 작성할 수 없는 것과 같다. 만약 클래스가 2개 이상의 constructor를 포함하면 문법 에러가 발생한다.

```js
class Person {
  constructor() {}
  constructor() {} // 두 번째 출생신고서는 불가능함 🙅🏻
}
// SyntaxError: A class may only have one constructor
```

constructor는 생략할 수 있다.

```js
class Person {} // 출생신고서를 생략하면 기본 양식이 적용됨! 👌
```

constructor를 생략하면 클래스에 빈 constructor가 암묵적으로 정의된다.

```js
class Person {
  // 👈 여기! constructor를 생략하면 다음과 같이 빈 constructor가 암묵적으로 정의된다.
  constructor() {} // 빈 출생신고서 - 이름도, 주소도 없는 상태
}

// 👈 여기! 빈 객체가 생성된다.
const me = new Person();
console.log(me); // Person {} - 아무 정보도 없는 빈 인스턴스
```

프로퍼티가 추가되어 초기화된 인스턴스를 생성하려면 constructor 내부에서 this에 인스턴스 프로터피를 추가한다.

```js
class Person {
  constructor() {
    // 고정값으로 인스턴스 초기화 - 모든 사람이 같은 정보를 가짐
    this.name = 'Lee';
    this.address = 'Seoul';
  }
}

// 인스턴스 프로퍼티가 추가된다.
const me = new Person();
console.log(me); // Person { name: "Lee", address: "Seoul" }
```

이는 마치 "출생신고서에 기본값을 미리 인쇄해두는 것"과 같다.

인스턴스를 생성할 때 클래스 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 다음과 같이 constructor에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달한다. 이때 초기값은 constructor의 매개변수에게 전달된다.

```js
class Person {
  constructor(name, address) {
    // 인수로 인스턴스 초기화 - 각각 다른 정보를 받아서 개별 설정
    this.name = name; // 전달받은 name을 인스턴스의 name에 저장
    this.address = address; // 전달받은 address를 인스턴스의 address에 저장
  }
}

// 인수로 초기값을 전달한다. 초기값은 constructor에 전달된다.
const me = new Person('Lee', 'Seoul'); // 'Lee'와 'Seoul'이 constructor의 매개변수로 전달됨
console.log(me); // Person { name: "Lee", address: "Seoul" }
```

이는 마치 "출생신고서 작성 시 부모가 아기의 이름과 주소를 직접 기입하는 것"과 같다.

이처럼 constructor 내에서는 인스턴스의 생성과 동시에 인스턴스 프로퍼티 추가를 통해 인스턴스의 초기화를 실행한다. 따라서 인스턴스를 초기화하려면 constructor를 생략해서는 안 된다.

**constructor는 별도의 반환문을 갖지 않아야 한다.** 클래스의 기본 동작을 훼손하기 때문이다. 만약 this가 아닌 다른 객체를 명시적으로 반환하면 this, 즉 인스턴스가 반환되지 못하고 return 문에 명시한 객체가 반환된다.

```js
class Person {
  constructor(name) {
    this.name = name;

    // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
    return {}; // 빈 객체를 강제로 반환 - 정상적인 인스턴스 대신 빈 객체가 나옴
  }
}

// constructor에서 명시적으로 반환한 빈 객체가 반환된다.
const me = new Person('Lee');
console.log(me); // {} - Lee라는 이름은 사라지고 빈 객체만 남음
```

이는 마치 "출생신고서를 작성했는데, 엉뚱한 서류를 제출하는 것"과 같다.

하지만 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.

```js
class Person {
  constructor(name) {
    this.name = name;

    // 명시적으로 원시값을 반환하면 반환은 무시되고 암묵적으로 this가 반환된다.
    return 100; // 숫자를 반환하려 했지만 무시됨
  }
}

const me = new Person('Lee');
console.log(me); // Person { name: "Lee" } - 정상적으로 인스턴스 반환
```

이는 마치 "출생신고서에 잘못된 정보를 적었지만, 시청에서 알아서 올바른 출생증명서를 발급해주는 것"과 같다.

이처럼 constructor 내부에서 명시적으로 this가 아닌 다른 값을 반환하는 것은 클래스의 기본 동작을 훼손한다. **따라서 constructor 내부에서 return 문을 반드시 생략해야 한다.**

### 25.5.2 프로토타입 메서드

생성자 함수를 사용하여 인스턴스를 생성하는 경우 프로토타입 메서드를 생성하기 위해서는 다음과 같이 명시적으로 프로토타입에 메서드를 추가해야 한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드 - 수동으로 프로토타입에 메서드를 추가해야 함
Person.prototype.sayHi = function () {
  console.log(`Hi! My name is ${this.name}`)'
};

const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

이는 마치 "수제 공방에서 제품을 만든 후, 사용설명서를 별도로 제작해서 제품과 함께 포장하는 것"과 같다.

클래스 몸체에서 정의한 메서드는 생성자 함수에 의한 객체 생성 방식과는 다르게 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드 - 클래스에서 정의하면 자동으로 프로토타입에 추가됨
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }
}

const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

이는 마치 "첨단 자동화 공장에서 제품과 사용설명서가 동시에 완성되어 나오는 것"과 같다. **클래스는 훨씬 편리하다*!*** 👍

생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

```js
// me 객체의 프로토타입은 Person.prototype이다.
Object.getPrototypeOf(me) === Person.prototype; // true
me instanceof Person; // true

// Person.prototype의 프로토타입은 Object.prototype이다.
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
me instanceof Object; // true

// me 객체의 constructor는 Person 클래스다.
me.constructor === Person; // true
```

이 코드를 실행해보면 흥미로운 사실을 발견할 수 있다. `me` 인스턴스는 Person.prototype을 통해 sayHi 메서드를 상속받는다. 그리고 Person.prototype은 다시 Object.prototype을 상속받는다. 이는 마치 "자식이 부모로부터 성격을 물려받고, 부모는 다시 할머니 할아버지로부터 성격을 물려받는 가족사"와 같다.

이처럼 클래스 몸체에서 정의한 메서드는 인스턴스의 프로토타입에 존재하는 프로토타입 메서드가 된다. 인스턴스는 프로토타입 메서드를 상속받아 사용할 수 있다.

프로토타입 체인은 기존의 모든 객체 생성 방식(객체 리터럴, 생성자 함수, Object.create 메서드 등) 뿐만 아니라 클래스에 의해 생성된 인스턴스에도 동일하게 적용된다. 생성자 함수의 역할을 클래스가 할 뿐이다.

결국 클래스는 생성자 함수와 같이 인스턴스를 생성하는 생성자 함수라고 볼 수 있다. 다시 말해, **클래스는 생성자 함수와 마찬가지로 프로토타입 기반의 객체 생성 매커니즘이다.**

### 25.5.3 정적 메서드

정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다. 마치 "공장의 관리사무소 기능"과 같다. 제품(인스턴스)을 만들지 않아도 공장 자체에서 제공하는 서비스다.

생성자 함수의 경우 정적 메서드를 생성하기 위해서는 다음과 같이 명시적으로 생성자 함수에 메서드를 추가해야 한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 정적 메서드 - 생성자 함수에 직접 메서드를 추가
Person.sayHi = function () {
  console.log('Hi!');
};

// 정적 메서드 호출
Person.sayHi(); // Hi!
```

클래스에서는 메서드에 static 키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 정적 메서드 - static 키워드만 붙이면 끝! 🤓
  static sayHi() {
    console.log('Hi!');
  }
}
```

이는 마치 "수동으로 간판을 달아야 했던 옛날과 달리, 이제는 버튼 하나로 네온사인이 켜지는 것"과 같다.

이처럼 정적 메서드는 클래스에 바인딩된 메서드가 된다. 클래스는 함수 객체로 평가되므로 자신의 프로퍼티/메서드를 소유할 수 있다. 클래스는 클래스 정의가 평가되는 시점에 함수 객체가 되므로 인스턴스와 달리 별다른 생성 과정이 필요 없다. 따라서 정적 메서드는 클래스 정의 이후 인스턴스를 생성하지 않아도 호출할 수 있다.

정적 메서드는 프로토타입 메서드처럼 인스턴스로 호출하지 않고 클래스로 호출한다.

```js
// 정적 메서드는 클래스로 호출한다.
// 정적 메서드는 인스턴스 없이도 호출할 수 있다.
Person.sayHi(); // Hi!
```

정적 메서드는 인스턴스로 호출할 수 없다. 정적 메서드가 바인딩된 클래스는 인스턴스의 프로토타입 체인상에 존재하지 않기 때문이다. 다시 말해, 인스턴스의 프로토타입 체인 상에는 클래스가 존재하지 않기 때문에 인스턴스로 클래스의 메서드를 상속받을 수 없다.

```js
// 인스턴스 생성
const me = new Person('Lee');
me.sayHi(); // TypeError: me.sayHi is not a function
```

이는 마치 "개별 제품에서는 공장의 관리사무소 전화번호로 전화를 걸 수 없는 것"과 같다. 제품과 공장은 별개의 존재다*!*

### 25.5.4 정적 메서드와 프로토타입 메서드의 차이

정적 메서드와 프로토타입 메서드의 차이는 다음과 같다.

**1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.**
**2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.**
**3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.**

```js
class Square {
  // 정적 메서드 - 클래스 자체의 기능
  static area(width, height) {
    return width * height; // 전달받은 인수만 사용
  }
}

console.log(Square.area(10, 10)); // 100
```

정적 메서드 area는 2개의 인수를 전달받아 면적을 계산한다. 이때 정적 메서드 area는 인스턴스 프로퍼티를 참조하지 않는다.

만약 인스턴스 프로퍼티를 참조해야 한다면 저적 메서드 대신 프로토타입 메서드를 사용해야 한다.

```js
class Square {
  constructor(width, height) {
    this.width = width; // 인스턴스가 자신의 크기 정보를 가짐
    this.height = height;
  }

  // 프로토타입 메서드 - 인스턴스의 데이터 사용
  area() {
    return this.width * this.height; // 자신의 프로퍼티 사용
  }
}

const square = new Square(10, 10);
console.log(square.area()); // 100
```

이는 마치 "개별 직사각형 객체가 자신의 크기를 알고 있어서, 자신의 넓이를 스스로 계산할 수 있는 것"과 같다.

메서드 내부에서 인스턴스 프로퍼티를 참조할 필요가 있다면 this를 사용해야 하며, 이런 경우 프로토타입 메서드로 정의해야 한다. 하지만 메서드 내부에서 인스턴스 프로퍼티를 참조해야 할 필요가 없다면 this를 사용하지 않게 된다.

물론 메서드 내부에서 this를 사용하지 않더라도 프로토타입 메서드로 정의할 수 있다. 하지만 반드시 인스턴스를 생성한 다음 인스턴스로 호출해야 하므로 this를 사용하지 않는 메서드는 정적 메서드로 정의하는 것이 좋다.

**정리하면:**

- **정적 메서드**: 유틸리티 함수처럼 독립적인 기능 (Math.max, Array.from 등)
- **프로토타입 메서드**: 인스턴스의 상태를 사용하는 기능 (배열의 push, 문자열의 slice 등)

### 25.5.5 클래스에서 정의한 메서드의 특징

클래스에서 정의한 메서드는 다음과 같은 특징을 가진다.

**1. function 키워드를 생략한 메서드 축약 표현을 사용한다.**
**2. 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.**
**3. 암묵적으로 strict mode로 실행된다.**
**4. for...in문이나 Object.keys 메서드 등으로 열거할 수 없다.** 즉, 프로퍼티의 열거 가능 여부를 나타내며, 불리언 값을 갖는 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false다. 5. **내부 메서드 [[Construct]]를 갖지 않는 non-constructor다.** 따라서 new 연산자와 함께 호출할 수 없다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  // 메서드 축약 표현 사용, 콤마 없음
  sayHi() {
    console.log(`Hi! ${this.name}`);
  }

  static sayHello() {
    console.log('Hello!');
  }
}

// 메서드는 열거되지 않음
console.log(Object.keys(Person.prototype)); // []

// 메서드는 생성자로 사용할 수 없음
const person = new Person('Lee');
// new person.sayHi(); // TypeError: person.sayHi is not a constructor
```

이런 특징들은 클래스를 더 안전하고 예측 가능하게 만든다.

---

## 25.6 클래스의 인스턴스 생성 과정

new 연산자와 함께 클래스를 호출하면 생성자 함수와 마찬가지로 클래스의 내부 메서드 [[Construct]]가 호출된다. 클래스는 new 연산자 없이 호출할 수 없으므로 언제나 생성자 함수로서 호출된다.

클래스로 인스턴스를 만드는 과정은 마치 "자동차 공자에서 자동차가 조립되는 과정"과 같다. 단계별로 살펴보자.

### 1. 인스턴스 생성과 this 바인딩

new 연산자와 함께 클래스를 호출하면 constructor의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체가 생성된다. 이 빈 객체가 바로 (아직 완성되지는 않았지만) 클래스가 생성한 인스턴스다. 이때 클래스가 생성한 인스턴스의 프로토타입으로 클래스의 prototype 프로퍼티가 가리키는 객체가 설정된다. 그리고 암묵적으로 생성된 빈 객체, 즉 인스턴스는 this에 바인딩된다. 따라서 constructor 내부의 this는 클래스가 생성한 인스턴스를 가리킨다.

```js
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true
  }
}
```

이는 마치 "공장에서 자동차 프레임을 먼저 만들고, 작업자(this)가 그 프레임을 담당하게 되는 것"과 같다.

### 2. 인스턴스 초기화

constructor의 내부 코드가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화한다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티 값을 초기화한다. 만약 constructor가 생략되었다면 이 과정도 생략된다.

```js
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name; // 프레임에 이름표를 부착
    console.log(this); // Person { name: "Lee" }
  }
}
```

이는 마치 "자동차 프레임에 엔진을 설치하고, 색칠을 하고, 브랜드 로고를 부착하는 과정"과 같다.

### 3. 인스턴스 반환

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

```js
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  }
}

// 인스턴스 생성. Person 클래스의 constructor가 호출된다.
const me = new Person('Lee');
console.log(me); // Person { name: "Lee" }
```

이는 마치 "완성된 자동차가 공장에서 출고되어 고객에게 전달되는 것"과 같다.

전체 과정을 정리하면:

1. **프레임 제작** (빈 객체 생성 + this 바인딩)
2. **조립 과정** (constructor 실행으로 프로퍼티 추가)
3. **완성품 출고** (this 반환)

---

## 25.7 프로퍼티

### 25.7.1 인스턴스 프로퍼티

인스턴스 프로퍼티는 constructor 내부에서 정의해야 한다.

```js
class Person {
  constructor(name, address) {
    // 인스턴스 프로퍼티
    this.name = name; // public 프로퍼티
    this.address = address; // public 프로퍼티
  }
}

const me = new Person('Lee', 'Seoul');
console.log(me); // Person { name: "Lee", address: "Seoul" }

// 인스턴스 프로퍼티는 기본적으로 public하다.
console.log(me.name); // Lee
console.log(me.address); // Seoul
```

**constructor 내부에서 this에 추가한 프로퍼티는 언제나 클래스가 생성한 인스턴스의 프로퍼티가 된다.** ES6의 클래스는 다른 객체지향 언어처럼 private, public, protected 키워드와 같은 접근 제한자를 지원하지 않는다. 따라서 인스턴스 프로퍼티는 언제나 public하다.

이는 마치 "집의 모든 방문이 항상 열려있어서 누구나 들여다볼 수 있는 상태"와 같다. **다행히 현재는 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다.**

### 25.7.2 접근자 프로퍼티

접근자 프로퍼티는 자체적으로 값([[Value]] 내부 슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수(accessor function)로 구성된 프로퍼티다.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${(this.firstName, this.lastName)}`;
  }

  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

const me = new Person('Ungmo', 'Lee');

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(`${me.firstName} ${me.lastName}`); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = 'Heegun Lee';
console.log(me); // { firstName: "Heegun", lastName: "Lee" }

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // Heegun Lee

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(Person.prototype, 'fullName'));
// { get: ƒ, set: ƒ, enumerable: false, configurable: true }
```

접근자 프로퍼티는 마치 "스마트 도어락"과 같다. 겉보기에는 일반 프로퍼티처럼 보이지만, 실제로는 값을 읽거나 쓸 때 특별한 함수가 실행된다. getter는 "도어락 열기", setter는 "도어락 잠그기" 기능을 담당한다.

접근자 프로퍼티는 클래스에서도 사용할 수 있다. 위 예제의 fullName은 접근자 프로퍼티다. 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수, 즉 getter 함수와 setter 함수로 구성되어 있다.

getter는 인스턴스 프로퍼티에 접근할 때마다 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용한다. getter는 메서드 이름 앞에 get 키워드를 사용해 정의한다. **이때 메서드 이름은 인스턴스 프로퍼티처럼 사용한다.** 다시 말해 getter는 호출하는 것이 아니라 프로퍼티처럼 참조하는 형식으로 사용하며, 참조 시에 내부적으로 getter가 호출된다.

setter는 인스턴스 프로퍼티에 값을 할당할 때마다 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용한다. setter는 메서드 이름 앞에 set 키워드를 사용해 정의한다. **이때 메서드 이름은 인스턴스 프로퍼티처럼 사용된다.** 다시 말해 setter는 호출하는 것이 아니라 프로퍼티처럼 값을 할당하는 형식으로 사용하며, 할당 시에 내부적으로 setter가 호출된다.

클래스의 메서드는 기본적으로 프로토타입 메서드가 된다. 따라서 클래스의 접근자 프로퍼티 또한 인스턴스 프로퍼티가 아닌 프로토타입의 프로퍼티가 된다.

### 25.7.3 클래스 필드 정의 제안

클래스 필드(필드 또는 멤버)는 클래스 기반 객체지향 언어에서 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어다.

```java
// java의 클래스 정의
public class Person {
	// ① 클래스 필드 정의
    // 클래스 필드는 클래스 몸체에 this 없이 선언한다.
    private String firstName = "";
    private String lastName = "";

    // 생성자
    Person(String firstName, String lastName) {
    	// ③ this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
    	this.firstName = firstName;
    	this.lastName = lastName;
  	}

    public String getFullName() {
    	// ② 클래스 필드 참조
        // this 없이도 클래스 필드를 참조할 수 있다.
        return firstName + " " + lastName;
    }
}
```

JavaScript의 클래스에서 인스턴스 프로퍼티를 선언하고 초기화하려면 반드시 constructor 내부에서 this에 프로퍼티를 추가해야 한다. 하지만 Java의 클래스에서는 위 예제의 ①과 같이 클래스 필드를 마치 변수처럼 클래스 몸체에 this없이 선언한다.

또한 JavaScript의 클래스에서 인스턴스 프로퍼티를 참조하려면 반드시 this를 사용하여 참조해야 한다. 하지만 Java의 클래스에서는 위 예제의 ②와 같이 this를 생략해도 클래스 필드를 참조할 수 있다.

JavaScript의 클래스 몸체에는 메서드만 선언할 수 있다. 따라서 클래스 몸체에 Java와 유사하게 클래스 필드를 선언하면 문법 에러가 발생한다.

```js
class Person {
  // 클래스 필드 정의
  name = 'Lee'; // SyntaxError: Unexpected token
}
```

하지만 위 예제를 최신 브라우저 또는 최신 Node.js에서 실행하면 문법 에러가 발생하지 않고 정상 동작한다.

그 이유는 JavaScript에서도 인스턴스 프로퍼티를 마치 클래스 기반 객체지향 언어의 클래스 필드처럼 정의할 수 있는 새로운 표준 사양인 "Class field declarations"가 TC39 프로세스의 stage 3(candidate)에 제안되어 있기 때문이다. 클래스 몸체에서 클래스 필드를 정의하는 경우 this에 클래스 필드를 바인딩해서는 안 된다. this는 클래스의 constructor와 메서드 내에서만 유효하다.

> Technical Committee 39(TC39)
> ECMA 인터내셔널은 ECMAScript 이외에도 다양한 기술의 사양을 관리하고, 이들 사양을 관리하는 주체인 기술 위원회도 여럿 존재한다. 여러 사양 중에서 ECMA-262 사양(ECMAScript)의 관리를 담당하는 위원회가 바로 TC39다. TC39는 구글, 애플, 마이크로소프트, 모질라 등 브라우저 벤더와 페이스북, 트위터와 같이 ECMA-262 사양 (ECMAScript)을 제대로 준수해야 하는 기업으로 구성되어 있다.

```js
class Person {
  // this에 클래스 필드를 바인딩해서는 안 된다.
  this.name = ''; // SyntaxError: Unexpected Token '.'
}
```

클래스 필드를 참조하는 경우 Java와 같은 클래스 기반 객체지향 언어에서는 this를 생략할 수 있으나 JavaScript에서는 this를 반드시 사용해야 한다.

```js
class Person {
  // 클래스 필드
  name = 'Lee';

  constructor() {
    console.log(name); // ReferenceError: name is not defined
  }
}

new Person();
```

클래스 필드에 초기값을 할당하지 않으면 undefined를 갖는다.

```js
class Person {
  // 클래스 필드를 초기화하지 않으면 undefined를 갖는다.
  name;
}

const me = new Person();
console.log(me); // Person { name: undefined }
```

인스턴스를 생성할 때 외부의 초기값으로 클래스 필드를 초기화해야 할 필요가 있다면 constructor에서 클래스 필드를 초기화해야 한다.

```js
class Person {
  name;

  constructor(name) {
    // 클래스 필드 초기화
    this.name = name;
  }
}

const me = new Person('Lee');
console.log(me); // Person { name: "Lee" }
```

이처럼 인스턴스를 생성할 때 클래스 필드를 초기화할 필요가 있다면 constructor 밖에서 클래스 필드를 정의할 필요가 없다. 클래스 필드를 초기화할 필요가 있다면 어차피 constructor 내부에서 클래스 필드를 참조하여 초기값을 할당해야 한다. 이때 this, 즉 클래스가 생성한 인스턴스에 클래스 필드에 해당하는 프로퍼티가 없다면 자동 추가되기 때문이다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }
}

const me = new Person('Lee');
console.log(me); // Person { name: "Lee" }
```

함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있다. 따라서 클래스 필드를 통해 메서드를 정의할 수도 있다.

```js
class Person {
  // 클래스 필드에 문자열을 할당
  name = 'Lee';

  // 클래스 필드에 함수를 할당
  getName = function () {
    return this.name;
  };
  // 화살표 함수로 정의할 수도 있다.
  // getName = () => this.name;
}

const me = new Person();
console.log(me); // Person { name: "Lee", getName: ƒ }
console.log(me.getName()); // Lee
```

이처럼 클래스 필드에 함수를 할당하는 경우, 이 함수는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다. 모든 클래스 필드는 인스턴스 프로퍼티가 되기 때문이다. 따라서 클래스 필드에 함수를 할당하는 것은 권장하지 않는다.

클래스 필드 정의 제안으로 인해 인스턴스 프로퍼티를 정의하는 방식은 두 가지가 되었다.

1. **외부 초기값으로 클래스 필드를 초기화할 필요가 있다면** constructor에서 정의한다.
2. **외부 초기값으로 클래스 필드를 초기화할 필요가 없다면** constructor에서 정의하는 방식과 클래스 필드 정의 제안 모두 사용할 수 있다.

### 25.7.4 private 필드 정의 제안

JavaScript는 캡슐화를 완전하게 지원하지 않는다. ES6의 클래스도 생성자 함수와 마찬가지로 다른 클래스 기반 객체지향 언어에서는 지원하는 private, public, protected 키워드와 같은 접근 제한자를 지원하지 않는다. 따라서 인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있다. **즉, 언제나 public하다.**

```js
class Person {
  constructor(name) {
    this.name = name; // 인스턴스 프로퍼티는 기본적으로 public하다.
  }
}

// 인스턴스 생성
const me = new Person('Lee');
console.log(me.name); // Lee
```

**클래스 필드 정의 제안을 사용하더라도 클래스 필드는 기본적으로 public하기 때문에 외부에 그대로 노출된다.**

```js
class Person {
  name = 'Lee'; // 클래스 필드도 기본적으로 public하다.
}

// 인스턴스 생성
const me = new Person();
console.log(me.name); // Lee
```

다행히 현재는 상황이 완전히 바뀌었다! ES2022(ES13)에서 private 필드가 정식 표준으로 승급되어 모든 주요 브라우저에서 완전히 지원되고 있다. 책이 출간될 당시(2021년)에는 TC39 프로세스의 stage 3(candidate)에 제안되어 있었지만, 지금은 이미 정식 JavaScript 기능이 되었다.

private 필드의 선두에는 #을 붙여준다. private 필드를 참조할 때도 #을 붙여주어야 한다.

```js
class Person {
  // private 필드 정의
  #name = '';

  constructor(name) {
    // private 필드 참조
    this.#name = name;
  }
}

const me = new Person('Lee');

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```

**이제 정말로 "진짜 private"을 만들 수 있다*!* 🥳** 마치 "금고에 비밀번호를 걸어서 아무나 열어볼 수 없게 하는 것"과 같다.

**private 필드는 반드시 클래스 몸체에 정의해야 한다**. private 필드를 직접 constructor에 정의하면 에러가 발생한다.

```js
class Person {
  constructor(name) {
    // private 필드는 클래스 몸체에서 정의해야 한다.
    this.#name = name;
    // SyntaxError: Private field '#name' must be declared in an enclosing class
  }
}
```

### 25.7.5 static 필드 정의 제안

클래스에는 static 키워드를 사용하여 정적 메서드를 정의할 수 있다. 하지만 static 키워드를 사용하여 정적 필드를 정의할 수는 없었다. **하지만 현재 ES2022에서 정식으로 지원되는 기능이 되었다*!* 🥳** static public 필드, static private 필드, static private 메서드를 정의할 수 있는 "Static class features"가 정식 표준이 되어 모든 주요 브라우저에서 지원되고 있다.

```js
class MyMath {
  // static public 필드 정의
  static PI = 22 / 7;

  // static private 필드 정의
  static #num = 10;

  // static 메서드
  static increment() {
    return ++MyMath.#num;
  }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increment()); // 11
```

static 필드는 마치 "클래스 전체가 공유하는 공용 저장소"와 같다. 인스턴스를 만들지 않아도 클래스 이름으로 바로 접근할 수 있다.

---

## 25.8 상속에 의한 클래스 확장

### 25.8.1 클래스 상속과 생성자 함수 상속

프로토타입 기반 상속은 프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념이지만 **상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의**하는 것이다.

상속에 의한 클래스 확장은 마치 "기존 제품을 개선해서 새로운 모델을 만드는 것"과 같다.

```js
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() {
    return 'eat';
  }

  move() {
    return 'move';
  }
}

// 상속을 통해 Animal 클래스를 확장한 Bird 클래스
class Bird extends Animal {
  fly() {
    return 'fly';
  }
}

const bird = new Bird(1, 5);

console.log(bird); // Bird { age: 1, weight: 5 }
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); // fly
```

상속에 의한 클래스 확장은 코드 재사용 관점에서 매우 유용하다. 마치 "이미 검증된 부품을 재활용해서 새로운 제품을 만드는 것"처럼 안전하고 효율적이다.

생성자 함수는 클래스와 같이 상속을 통해 다른 생성자 함수를 확장할 수 있는 문법이 제공되지 않는다. 하지만 클래스는 상속을 통해 기존 클래스를 확장할 수 있는 문법인 extends 키워드가 기본적으로 제공된다.

### 25.8.2 extends 키워드

상속을 통해 클래스를 확장하려면 extends 키워드를 사용하여 상속받을 클래스를 정의한다.

```js
// 수퍼(베이스/부모)클래스
class Base {}

// 서브(파생/자식)클래스
class Derived extends Base {}
```

**상속을 통해 확장된 클래스를 서브클래스(subclass)**라 하고, **서브클래스에게 상속된 클래스를 수퍼클래스(superclass)**라 한다. 서브클래스를 **파생 클래스(derived class)** 또는 **자식 클래스(child class)**라고 부르기도 하고, 수퍼클래스를 **베이스 클래스(base class)** 또는 **부모 클래스(parent class)**라고 부르기도 한다.

**extends 키워드는 마치 "가계도의 화살표"와 같다.** "A extends B"는 "A는 B의 후손이다"라는 뜻이다.

extends 키워드의 역할은 수퍼클래스와 서브클래스 간의 상속 관계를 설정하는 것이다. 클래스도 프로토타입을 통해 상속 관계를 구현한다.

수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다. 이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

### 25.8.3 동적 상속

extends 키워드는 클래스뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다. 단, extends 키워드 앞에는 반드시 클래스가 와야 한다.

```js
// 생성자 함수
function Base(a) {
  this.a = a;
}

// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived { a: 1 }
```

이는 마치 "구형 자동차(생성자 함수)의 엔진을 가져와서 최신 자동차(클래스)에 장착하는 것"과 같다. 예전 기술과 새로운 기술을 조합할 수 있다는 뜻이다.

extends 키워드 다음에는 클래스뿐만이 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. 이를 통해 동적으로 상속받을 대상을 결정할 수 있다.

```js
function Base1() {}

class Base2 {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

이는 마치 "상황에 따라 다른 부모를 선택할 수 있는 스마트한 상속"과 같다. 런타임에 조건을 보고 어떤 클래스를 상속받을지 결정할 수 있다.

### 25.8.4 서브클래스의 constructor

클래스에서 constructor를 생략하면 클래스에 비어있는 constructor가 암묵적으로 정의된다고 했다.

```js
constructor() {}
```

서브클래스에서 constructor를 생략하면 클래스에 다음과 같은 constructor가 암묵적으로 정의된다.

```js
constructor(...args) { super(...args); }
```

**여기서 중요한 점은 서브클래스의 constructor에는 super 호출이 자동으로 들어간다는 것이다.** args는 new 연산자와 함께 클래스를 호출할 때 전달한 인수의 리스트다.

```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  // 다음과 같은 constructor가 암묵적으로 정의된다.
  // constructor(...args) { super(...args); }
}

const derived = new Derived(1, 2);
console.log(derived); // Derived { a: 1, b: 2 }
```

이는 마치 자식이 태어날 때 부모의 DNA를 자동으로 물려받는 것과 같다. 명시적으로 하지 않아도 부모의 특성을 자동으로 이어받는다.

위 예제와 같이 수퍼클래스와 서브클래스 모두 constructor를 생략하면 빈 객체가 생성된다. **프로퍼티를 소유하는 인스턴스를 생성하려면 constructor 내부에서 인스턴스에 프로퍼티를 추가해야 한다.**

### 25.8.5 super 키워드

super 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.
super는 마치 "부모님을 부르는 호칭"과 같다. super는 다음과 같이 동작한다.

- super를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.
- super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

#### super 호출

super를 호출하면 수퍼클래스의 constructor를 호출한다.

수퍼클래스의 constructor 내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 있다. 이때 new 연산자와 함꼐 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암묵적으로 정의된 constructor의 super 호출을 통해 수퍼클래스의 constructor에 전달된다.

```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    // ④
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  // 다음과 같은 constructor가 암묵적으로 정의된다.
  // constructor(...args) { super(...args); }
}

const derived = new Derived(1, 2); // ①②③
console.log(derived); // Derived { a: 1, b: 2 }
```

이 과정을 단계별로 보면:

1. ① `new Derived(1, 2)` 호출
2. ② Derived의 암묵적 constructor 실행
3. ③ `super(1, 2)` 호출로 Base의 constructor에 인수 전달
4. ④ Base의 constructor에서 `this.a = 1, this.b = 2` 실행

마치 자식이 부모에게 '엄마, 이거 좀 해주세요!'라고 부탁하는 것과 같다.

수퍼클래스에서 추가한 프로퍼티와 서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 없다. 이때 new 연산자와 함께 서브클래스를 호출하면서 전달한 인수 중에서 수퍼클래스의 constructor에 전달할 필요가 있는 인수는 서브클래스의 constructor에서 호출하는 super를 통해 전달한다.

```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  construtor(a, b, c) {
    super(a, b); // 수퍼클래스의 constructor 호출
    this.c = c; // 서브클래스에서 추가한 프로퍼티
  }
}

const derived = new Derived(1, 2, 3);
console.log(derived); // Derived { a: 1, b: 2, c: 3 }
```

이는 마치 부모에게서 받은 유산(a, b)에 자신이 번 돈(c)을 추가하는 것과 같다.

super를 호출할 때 주의사항은 다음과 같다.

**1. 서브클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.**

```js
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class befor accessing 'this' or returning from derived constructor
    console.log('constructor call');
  }
}

const derived = new Derived();
```

**2. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.**

```js
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class befor accessing 'this' or returning from derived constructor
    this.a = 1;
    super();
  }
}

const derived = new Derived();
```

3. super는 반드시 서브클래스의 constructor에서만 호출한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.

```js
class Base {
  constructor() {
    super(); // SyntaxError: 'super' keyword unexpected here
  }
}

function Foo() {
  super(); // SyntaxError: 'super' keyword unexpected here
}
```

#### super 참조

메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

1. 서브클래스의 프로토타입 메서드 내에서 super.sayHi는 수퍼클래스의 프로토타입 메서드 sayHi를 가리킨다.

```js
// 수퍼클래스
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

// 서브클래스
class Derived extends Base {
  sayHi() {
    // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
    return `${super.sayHi()}. How are you doing?`;
  }
}

const derived = new Derived('Lee');
console.log(derived.sayHi()); // Hi! Lee. How are you doing?
```

이는 마치 자식이 부모의 말을 인용하면서 자신의 말을 덧붙이는 것과 같다. 부모의 기능을 그대로 사용하면서 추가 기능을 더한다.

super 참조를 통해 수퍼클래스의 메서드를 참조하려면 super가 수퍼클래스의 메서드가 바인딩된 객체, 즉 수퍼클래스의 Prototype 프로퍼티에 바인딩된 프로토타입을 참조할 수 있어야 한다. 위 예제는 다음 예제와 동일하게 동작한다.

```js
// 수퍼클래스
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  sayHi() {
    // __super는 Base.prototype을 가리킨다.
    const __super = Object.getPrototypeOf(Derived.prototype);
    return `${__super.sayHi.call(this)}. How are you doing?`;
  }
}
```

super는 자신을 참조하고 있는 메서드(Derived의 sayHi)가 바인딩되어 있는 객체(Derived.prototype)의 프로토타입(Base.prototype)을 가리킨다. 따라서 super.sayHi는 Base.prototype.sayHi를 가리킨다. 단, super.sayHi, 즉 Base.prototype.sayHi를 호출할 때 call 메서드를 사용해 this를 전달해야 한다.

call 메서드를 사용해 this를 전달하지 않고 Base.prototype.sayHi를 그대로 호출하면 Base.prototype.sayHi 메서드 내부의 this는 Base.prototype을 가리킨다. Base.prototype에는 name 프로퍼티가 존재하지 않기 때문에 undefined가 출력된다.

이처럼 super 참조가 동작하기 위해서는 super를 참조하고 있는 메서드(Derived의 sayHi)가 바인딩되어 있는 객체(Derived.prototype)의 프로토타입(Base.prototype)을 찾을 수 있어야 한다. 이를 위해 메서드는 내부 슬롯 [[HomeObject]]를 가지며, 자신을 바인딩하고 있는 객체를 가리킨다.

**2. 서브클래스의 정적 메서드 내에서 super.sayHi는 수퍼클래스의 정적 메서드 sayHi를 가리킨다.**

```js
// 수퍼클래스
class Base {
  static sayHi() {
    return 'Hi!';
  }
}

// 서브클래스
class Derived extends Base {
  static sayHi() {
    // super.sayHi는 수퍼클래스의 정적 메서드를 가리킨다.
    return `${super.sayHi()} How are you doing?`;
  }
}

console.log(Derived.sayHi()); // Hi! How are you doing?
```

### 25.8.6 상속 클래스의 인스턴스 생성 과정

직사각형을 추상화한 Rectangle 클래스와 상속을 통해 Rectangle 클래스를 확장한 ColorRectangle 클래스를 정의해보자.

```js
// 수퍼클래스
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

// 서브클래스
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

const colorRectangle = new ColorRectangle(2, 4, 'red');
console.log(colorRectangle); // Colorrectangle { width: 2, height: 4, color: "red" }

// 상속을 통해 getArea 메서드를 호출
console.log(colorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드를 호출
console.log(colorRectangle.toString()); // width = 2, height = 4, color = red
```

서브클래스 ColorRectangle이 new 연산자와 함께 호출되면 다음 과정을 통해 인스턴스가 생성된다.

상속받은 클래스의 인스턴스 생성은 마치 2층집을 짓는 과정과 같다. 먼저 1층(수퍼클래스)을 짓고, 그 위에 2층(서브클래스)을 올린다.

1. 서브클래스의 super 호출
   JavaScript 엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 "base" 또는 "derived"를 값으로 갖는 내부 슬롯 [[ConstructorKind]]를 갖는다. 다른 클래스를 상속받지 않는 클래스(그리고 생성자 함수)는 내부 슬롯 [[ConstructorKind]]의 값이 "base"로 설정되지만 다른 클래스를 상속받는 서브클래스는 내부 슬롯 [[ConstructorKind]]의 값이 "derived"로 설정된다. 이를 통해 수퍼클래스와 서브클래스는 new 연산자와 함께 호출되었을 때의 동작이 구분된다.

다른 클래스를 상속받지 않는 클래스는 new 연산자와 함께 호출되었을 때 암묵적으로 빈 객체, 즉 인스턴스를 생성하고 이를 this에 바인딩한다.

**하지만 서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임한다. 이것이 바로 서브클래스의 constructor에서 반드시 super를 호출해야 하는 이유다.**

이는 마치 전문 시공 업체(수퍼클래스)에게 기초 공사를 맡기는 것과 같다. 서브클래스는 혼자서 집을 지을 수 없고, 반드시 전문가의 도움이 필요하다.

2. 수퍼클래스의 인스턴스 생성과 this 바인딩

수퍼클래스의 constructor 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다. 이 빈 객체가 바로 (아직 완성되지는 않았지만) 클래스가 생성한 인스턴스다. 그리고 암묵적으로 생성된 빈 객체, 즉 인스턴스는 this에 바인딩된다. 따라서 수퍼클래스의 constructor 내부의 this는 생성된 인스턴스를 가리킨다.

```js
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
  }
}
```

이때 인스턴스는 수퍼클래스가 생성한 것이다. 하지만 new 연산자와 함께 호출된 클래스가 서브클래스라는 것이 중요하다. 즉, new 연산자와 함께 호출된 함수를 가리키는 new.target은 서브클래스를 가리킨다. 따라서 인스턴스는 new.target이 가리키는 서브클래스가 생성한 것으로 처리된다.

따라서 생성된 인스턴스의 프로토타입은 수퍼클래스의 prototype 프로퍼티가 가리키는 객체(Rectangle.prototype)가 아니라 new.target, 즉 서브클래스의 prototype 프로퍼티가 가리키는 객체(ColorRectangle.prototype)다.

3. 수퍼클래스의 인스턴스 초기화

수퍼클래스의 constructor가 this에 바인딩되어 있는 인스턴스를 초기화한다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

4. 서브클래스 constructor로의 복귀와 this 바인딩

super의 호출이 종료되고 제어 흐름이 서브클래스 constructor로 돌아온다. 이때 super가 반환한 인스턴스가 this에 바인딩된다. 서브클래스는 별도의 인스턴스르 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 그대로 사용한다. **즉, 서브클래스는 수퍼클래스가 만든 인스턴스를 그대로 이어받는다.** 이것이 super가 호출되지 않으면 인스턴스가 생성되지 않으며, this 바인딩도 할 수 없는 이유다.

5. 서브클래스의 인스턴스 초기화

super 호출 이후, 서브클래스의 constructor에 기술되어 있는 인스턴스 초기화가 실행된다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

6. 인스턴스 반환

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

```js
// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);

    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle { width: 2, height: 4 }

    // 인스턴스 초기화
    this.color = color;

    // 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
    console.log(this); // ColorRectangle { width: 2, height: 4, color: "red" }
  }

  ...
```

### 25.8.7 표준 빌트인 생성자 함수 확장

String, Number, Array 같은 표준 빌트인 객체도 [[Construct]] 내부 메서드를 갖는 생성자 함수이므로 extends 키워드를 사용하여 확장할 수 있다.

```js
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);
console.log(myArray); // MyArray(4) [1, 1, 2, 3]

// MyArray.prototype.uniq 호출
console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
// MyArray.prototype.average 호출
console.log(myArray.average()); // 1.75
```

이는 마치 기존의 검증된 도구(Array)에 나만의 특별한 도구들을 추가하는 것과 같다. 기본 기능은 그대로 두고 필요한 기능만 추가한다.

Array 생성자 함수를 상속받은 MyArray 클래스가 생성한 인스턴스는 Array.prototype과 MyArray.prototype의 모든 메서드를 사용할 수 있다.

이때 주의할 것은 Array.prototype의 메서드 중에서 map, filter와 같이 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환한다는 것이다.

```js
console.log(myArray.filter(v => v % 2) instanceof MyArray); // true
```

만약 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환하지 않고 Array의 인스턴스를 반환하면 MyArray 클래스의 메서드와 메서드 체이닝이 불가능하다.

```js
// 메서드 체이닝
// [1, 1, 2, 3] => [ 1, 1, 3 ] => [ 1, 3 ] => 2
console.log(
  myArray
    .filter(v => v % 2)
    .uniq()
    .average()
); // 2
```

myArray.filter가 반환하는 인스턴스는 MyArray 클래스가 생성한 인스턴스로 uniq 메서드를 연쇄 호출(메서드 체이닝)할 수 있다.

만약 MyArray 클래스의 uniq 메서드가 MyArray 클래스가 생성한 인스턴스가 아닌 Array가 생성한 인스턴스를 반환하게 하려면 다음과 같이 Symbol.species를 사용하여 정적 접근자 프로퍼티를 추가한다.

```js
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 모든 메서드가 Array 타입의 인스턴스를 반환하도록 한다.
  static get [Symbol.species]() {
    return Array;
  }

  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);

console.log(myArray.uniq() instanceof MyArray); // false
console.log(myArray.uniq() instanceof Array); // true

// 메서드 체이닝
// uniq 메서드는 Array 인스턴스를 반환하므로 average 메서드를 호출할 수 없다.
console.log(myArray.uniq().average());
// TypeError: myArray.uniq(...).average is not a function
```

---

# 핵심 개념 총정리

## 클래스의 본질

### 클래스는 무엇인가?

- 클래스는 "붕어빵 틀" - 인스턴스를 만들기 위한 템플릿*!*
- 새로운 객체 생성 매커니즘 - 단순한 문법적 설탕이 아님*!* 🙅🏻‍♀️
- 프로토타입 기반 + 클래스 기반 문법 - 기존 프로토타입을 더 직관적으로 사용*!* 👏

### 왜 클래스가 필요했나?

**Before (ES5)**: 복잡한 프로토타입 조작

```js
function Person(name) {
  this.name = name;
}
Person.prototype.sayHi = function () {
  console.log(this.name);
};
```

**After (ES6)**: 직관적인 클래스 문법

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    console.log(this.name);
  }
}
```

## 클래스 vs 생성자 함수의 차이점

| 구분              | 클래스                   | 생성자 함수                 |
| ----------------- | ------------------------ | --------------------------- |
| **new 없는 호출** | 에러 발생                | 일반 함수로 실행            |
| **상속 문법**     | extends, super 제공      | 복잡한 프로토타입 조작 필요 |
| **호이스팅**      | TDZ 적용 (let/const처럼) | 함수 호이스팅               |
| **strict mode**   | 강제 적용                | 선택 사항                   |
| **열거 가능성**   | 메서드가 열거되지 않음   | 기본적으로 열거됨           |

🔥 **핵심**: 클래스는 더 안전하고 엄격한 "_업그레이드된 생성자 함수_"

## 클래스의 구성 요소

### 1. constructor (생성자)

- **역할**: 인스턴스 생성 및 초기화
- **특징**: 클래스당 1개만, 생략 가능
- **주의**: return문 사용 금지

```js
class Person {
  constructor(name) {
    this.name = name; // 인스턴스 프로퍼티 초기화
  }
}
```

### 2. 프로토타입 메서드

- **역할**: 모든 인스턴스가 공유하는 기능
- **자동 등록**: 클래스에 정의하면 자동으로 prototype에 추가

```js
class Person {
  sayHi() {
    // 자동으로 Person.prototype.sayHi가 됨
    console.log(`Hi! ${this.name}`);
  }
}
```

### 3. 정적 메서드

- **역할**: 클래스 자체의 기능 (인스턴스 없이 사용)
- **용도**: 유틸리티 함수, 팩토리 메서드 등

```js
class Person {
  static create(name) {
    return new Person(name);
  }
}

Person.create('Lee'); // 인스턴스 없이 호출
```

## 클래스 인스턴스 생성 과정

1. 빈 객체 생성 → this에 바인딩
2. constructor 실행 → 프로퍼티 추가/초기화
3. 완성된 인스턴스 반환 → 자동으로 this 반환

붕어빵 틀에 반죽 넣고 → 팥 추가하고 → 완성된 붕어빵 꺼내기! 🐠

## 현대적 클래스 기능들 (ES2022+)

### Private 필드 (#)

**과거**: 모든 프로퍼티가 public

```js
class Person {
  constructor(name) {
    this.name = name; // 외부에서 접근 가능
  }
}
```

**현재**: 진짜 private 구현 가능

```js
class Person {
  #name; // 진짜 private!

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name; // 클래스 내부에서만 접근 가능
  }
}
```

### 클래스 필드

과거: constructor에서만 프로퍼티 정의

```js
class Person {
  constructor() {
    this.name = 'Lee';
  }
}
```

현재: 클래스 몸체에서 직접 정의

```js
class Person {
  name = 'Lee'; // 클래스 필드
  #age = 25; // private 필드
}
```

## 상속 (extends & super)

### 상속의 핵심 개념

- extends: "A extends B" = "A는 B의 후손"
- super: 부모와의 소통 창구

### super의 두 가지 역할

1. **super() 호출**: 부모 constructor 실행

```js
class Parent {
  constructor(name) {
    this.name = name;
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name); // 부모 constructor 호출
    this.age = age;
  }
}
```

2. **super 참조**: 부모 메서드 호출

```js
class Parent {
  greet() {
    return 'Hello';
  }
}

class Child extends Parent {
  greet() {
    return `${super.greet()} World`; // 부모 메서드 + 추가
  }
}
```

### 상속 시 인스턴스 생성 과정

1. 서브클래스가 super 호출 → 부모에게 인스턴스 생성 위임
2. 수퍼클래스가 인스턴스 생성 → 기본 프로퍼티 설정
3. 서브클래스로 복귀 → 추가 프로퍼티 설정
4. 완성된 인스턴스 반환

## 기억해야 할 핵심

> 클래스는 단순히 "예쁜 포장지"가 아니다.
> 프로토타입 기반의 강력함 + 클래스 기반의 직관성을 모두 가진 JavaScript 객체지향의 현대적 해답이다.

클래스로 할 수 있는 것들:

- 깔끔한 객체 설계 ✨
- 안전한 상속 구현 🏗️
- 진정한 데이터 은닉 🔒
- 유지보수 쉬운 코드 😌
