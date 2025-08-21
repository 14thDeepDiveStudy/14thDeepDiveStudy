# JS DeepDive

## 22.this

### this란?

> 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수**이다.

this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.

**this의 필요성**
객체의 동작을 나타내는 메서드는 자신이 속한 객체의 프로퍼티(상태)를 변경할 수 있어야 한다.
이때 메서드가 자신이 속한 객체의 프로퍼티를 참조할려면 먼저 **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.**

- 객체 리터럴 방식의 메서드는 메서드 내부에서 자신이 속한 객체를 재귀적으로 호출할 수 있다.
  하지만 자신이 속한 객체를 재귀적으로 참조하는 방식은 바람직하지 않다.

- 생성자 함수 내부에서는 프로퍼티,메서드를 추가히기 위해 생성할 인스턴스를 참조할 수 있어야 한다.
  이는 인스턴스를 생성하려면 우선 생성자 함수가 있어야 한다. 하지만 생성자 함수를 정의하는 시점은 인스턴스를 생성하기 전이므로, 인스턴스를 가리키는 식별자가 없다.

> 자신이 속한 객체 or 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다
> 그것이 **this**

this는 JS엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조할 수 있다.
**단 this가 가리키는 값(this 바인딩)은 함수 호출 방식에 의해 동적으로 결정된다.**

> **this바인딩**
> 바인딩 : 식별자와 값을 연결하는 것
> this바인딩 : this와 this가 가리킬 객체를 바인딩 하는 것

**객체 리터럴에서의 this**
객체 리터럴의 메서드 내부에서의 this는 **메서드를 호출한 객체**를 가리킨다.

```jsx
// 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius; // this는 호출한 메서드를 가리킨다
  },
};

console.log(circle.getDiameter()); // 5
```

**생성자 함수에서의 this**
생성자 함수 내부의 this는 생성자 함수가 **생성할 인스턴스**를 가리킨다.

```jsx
function Circle(radius) {
  this.radius = radius; //this는 생성할 인스턴스를 가리킨다
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius; //this는 생성할 인스턴스를 가리킨다
};

// 인스턴스 생성
const circle = new Circle(10);
console.log(circle.getDiameter()); // 20
```

> JS의 this는 함수가 호출되는 방식에 따라 this바인딩이 동적으로 결정된다.

stirct mode(엄격 모드)도 this바인딩에 영향을 준다.
this는 코드 어디에서든 참조 가능하며, 전역에도 참조할 수 있다.

**전역에서의 this**
전역에서의 this는 전역 객체 `window`를 가리킨다.

```jsx
console.log(this); // Window {0: global, window...}
```

하지만, this는 자기 참조 변수이므로 일반적으로 객체의 메서드 내부, 생성자 함수 내부에서만 의미가 있다.

---

### 함수 호출 방식과 this 바인딩

**this바인딩은 함수 호출 방식에 따라 동적으로 결정된다.**

> 렉시컬 스코프와 this바인딩의 결정 시기
>
> - 렉시컬 스코프 : 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다.
> - this 바인딩 : 함수 호출 시점

동일한 함수의 다양한 호출 방식

1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

#### 일반 함수 호출

**기본적으로 this에는 전역객체가 바인딩된다.**

```jsx
function foo() {
  console.log(this); // Window {0: global, window...}

  function bar() {
    console.log(this); // Window {0: global, window...}
  }
  bar();
}

foo();
```

전역함수든 중첩함수든 **일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다.**
하지만, this는 자기 참조 변수이므로 객체를 생성하지 않는 일반 함수에서 this는 의미가 없다.

- strict mode(엄격 모드)가 적용된 일반 함수 내부의 this는 `undefined`가 바인딩된다.

```jsx
function foo() {
  'use strict'

  console.log(this); // undefined

  function bar() {
    console.log(this);  // undefined
  }
  bar()
}

foo(
```

- 메서드 내에서 정의한 중첩 함수도 **일반 함수**로 호출하면 전역 객채가 바인딩된다.

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(this // {value : 100, foo: f}
    console.log(this.value); // 100

    // 메서드 내에서 정의한 중첩 함수(일반 함수)
    function bar() {
      console.log(this); // Window {0: global, window...}
      console.log(this.value); // 1
    }

    bar();
  },
};

obj.foo();
```

- **일반 함수**로 호출된 콜백 함수도 콜백 함수 내부에서 this는 전역 객체를 바인딩한다.

```jsx
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(this); // {value : 100, foo: f}

    // 콜백 함수 내부의 this
    setTimeout(function () {
      console.log(this); // Window {0: global, window...}
      console.log(this.value); // 1
    }, 1000);
  },
};

obj.foo();
```

> **즉, 일반 함수로 호춣된 모든 함수(중첩, 콜백 포힘)의 내부의 this에는 전역 객체가 바인딩된다.**

하지만 중첩함수나 콜백 함수의 this가 전역 객체를 바인딩하는 것은 문제가 있다.
이런 함수들은 외부 함수를 도와주는 헬퍼 함수이기 때문에 외부 함수의 일부 로직을 대신하는 경우가 대부분인데, **외부 함수의 this와 헬퍼 함수의 this가 일치하지 않아** 헬퍼 함수로 동작하기 어렵다.

이런 경우에는 this 바인딩을 변수에 할당한다.

```jsx
..
foo(){
	const that = this

	setTimeout(function () {
      	console.log(that.value); // 100
    }, 1000);
}
```

이 방법 외에도 apply, call, bind, 화살표 함수를 사용하는 방법이 있다.

#### 메서드 호출

메서드 내부의 this는 **메서드를 호출한 객체**에 바인딩된다.

> 메서드를 소유한 객체가 아닌, 메서드를 **호출**한 객체이다.

```jsx
const person = {
  name: "우진",
  getName() {
    return this.name;
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩
  },
};

// 메서드를 호출한 객체는 person
console.log(person.getName()); // 우진
```

메서드는 객체에 포함된 것이 아니라 독립적으로 존재하는 별도의 객체이다.

> 즉, 메서드 === 함수 객체(별개의 객체)를 참조하는 프로퍼티 값

메서드는 다른 객체의 프로퍼티에 할당 하는 것으로 다른 객체의 메서드가 될 수도 있고, 일반 변수에 할당하여 일반 함수로 호출할 수 있다.

```jsx
const anotherPerson = {
  name: "다른 사람",
};
// anotherPerson 객체의 메서드로 getName할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson
console.log(anotherPerson.getName()); // 다른 사람

//getName를 변수에 할당
const getName = person.getName;

//getName 메서드를 일반 함수로 호출
console.log(getName()); // undefined
```

따라서 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와 관계 없이 메서드를 **호출**한 객체에 바인딩된다.

프로토타입 메서드 내부에서도 동일하게 호출한 객체에 바인딩된다.

#### 생성자 함수 호출

생성자 함수 내부의 this에는 **생성자 함수가 생성할 인스턴스**가 바인딩된다.

```jsx
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

- new연산자 없이 호출하면 일반 함수 호출과 동일

#### Function.prototype.apply / call / bind 메서드에 의한 간접 호출

세 메서드 모두 Function.prototype의 메서드이기에 모든 함수가 상속받아 사용 가능하다.

**apply, call 메서드**

> apply, call 사용법
>
> ```jsx
> /**
> * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출
> * @param thisArg - this로 사용할 객체
> * @param argsArray - 함수에 전달할 인수 리스트의 배열 or 유사 배열 갤체
> * @returns 호출된 함수의 반환값
> */
> apply(thisArg[, argsArray]);
>
> /**
> * 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용해 함수를 호출
> * @param thisArg - this로 사용할 객체
> * @param arg1, arg2, ... - 함수에 전달할 인수 리스트
> * @returns 호출된 함수의 반환값
> */
> call(thisArg [, arg1[, arg2[,...]]]
> ```

```jsx
function getThisBinding() {
  console.log(arguments);

  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a:1}
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a:1}
```

apply, call의 본질적인 기능은 **함수를 호출**하는 것이다.

첫 번째 인수 : 함수에 this바인딩할 특정 객체
두 번째 인수 - apply : 호출할 함수의 인수 배열 - call : 호출한 함수의 인수 리스트(,)

**bind 메서드**
메서드의 this와 메서드 내부의 중첩 함수 or 콜백 함수의 this가 불일치할때 사용한다.

- 각 this가 불일치하는 상황

```jsx
const person = {
  name: "정우진",
  foo(cb) {
    setTimeout(cb, 500);
  },
};

person.foo(function () {
  console.log(`안녕! 내 이름은 ${this.name}`); // undefined
  // 일반 함수로 호출된 콜백 함수의 this는 전역 객체 window(window.name = undefined )
});
```

- bind()로 this를 일치

```jsx
const person = {
  name: "정우진",
  foo(cb) {
    //bind 메서드로 cb함수 내부의 this 바인딩 전달
    setTimeout(cb.bind(this), 500);
  },
};

person.foo(function () {
  console.log(`안녕! 내 이름은 ${this.name}`); // 안녕! 내 이름은 정우진
});
```

#### 화살표 함수 호출

화살표 함수에서의 this는 일반 함수의 this와 다르게 동작한다.
콜백 함수 내부의 this가 외부 함수의 this와 달라서 생기는 문제를 해결하기 위해 설계됐기 때문이다.

> **화살표 함수에서의 this는 함수 자체의 this 바인딩을 갖지 않는다.** > **화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다.**
> -lexical this-

렉시컬 스코프처럼 화살표 함수의 this가 함수가 정의된 위치에 의해 결정된다.

화살표 함수는 this바인딩이 존재하지 않아서, 스코프 체인을 통해 상위 스코프에서 this를 탐색한다.

```jsx
const person = {
  name: "우리둥절",
  normalFunc: function () {
    console.log(this); // {name: '우리둥절', normalFunc: ƒ}
    console.log(this.name); // 우리둥절

    const innerArrow = () => {
      console.log(this); // {name: '우리둥절', normalFunc: ƒ}
    };
    innerArrow();
  },
};

person.normalFunc();
```

- 전역 함수인 화살표 함수의 this : 전역 객체
- 프로퍼티에 할당한 화살표 함수의 this : 스코프 체인 상 가장 가까운 화살표 함수가 아닌 함수의 this
- call, apply, bind 메서드를 사용해도 내부의 this를 교체할 수 없고, 언제나 상위 스코프의 this 바인딩을 참조
- 화살표 함수로 메서드로 정의하는 것을 피하며, ES6의 메서드 축약 표현으로 정의한 ES6메서드를 사용

```jsx
const person = {
	name: '정우진'

  	sayHi: () => console.log(`안녕! ${this.name}`) ❌
	// this = undefined(전역 객체의 this)

	sayHi(){
      console.log(`안녕! ${this.name}`) ✅
      // this = person 객체
    }
}
person.sayHi()
```

- 프로토타입 객체에 화살표 함수로 프로퍼티를 할당하는 것도 동일한 문제 발생하며, 이땐 일반 함수를 사용한다.

#### addeventlistener에서의 호출

- **콜백함수(리스너)를 일반 함수로 호출할 때**

```jsx
element.addEventListener("click", function (e) {
  console.log(e.this); //element
  console.log(e.currentTarget === this); // true
});
```

이벤트 핸들러에서 콜백 함수를 일반 함수로 설정했을 경우 this는 이벤트 대상을 가르킨다

> 즉, this === currentTarget

- **콜백함수(리스너)를 화살표 함수로 호출할 때**

```jsx
element.addEventListener("click", (e) => {
  console.log(e.this); // 상위 스코프의 this
  console.log(e.currentTarget === this); // `false` 기록
});
```

콜백 함수가 화살표 함수일 경우 상위 스코프의 this를 참조한다.

### this 정리

| 함수 호출 방식                                        | this 바인딩                                       |
| ----------------------------------------------------- | ------------------------------------------------- |
| 일반 함수 호출                                        | **전역 객체**                                     |
| 일반 함수 호출 - strict mode(엄격 모드)               | **undefined**                                     |
| 메서드 호출                                           | **메서드를 호출한 객체**                          |
| 생성자 함수 호출                                      | **생성자 함수가 생성할 인스턴스**                 |
| apply(), call(), bind()에 의한 간접 호출              | **각 메서드에 첫번째 인수로 전달한 객체**         |
| 화살표 함수 호출                                      | **this바인딩이 없으며 상위 스코프의 this를 참조** |
| addeventlistener의 콜백함수(리스너)가 일반 함수일때   | **이벤트의 타겟(currentTarget)**                  |
| addeventlistener의 콜백함수(리스너)가 화살표 함수일때 | **this바인딩이 없으며 상위 스코프의 this를 참조** |
