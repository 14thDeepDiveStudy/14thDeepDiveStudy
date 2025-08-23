# 22.1 `this` 키워드

JavaScript에서 `this` 키워드는 **자기 자신이 속한 객체를 가리키는 특수한 식별자**다.<br />
그런데 흥미로운 점은, `this`가 무엇을 가리킬지는 **변수처럼 코드 작성 시점에 정해지는 게 아니라, 함수가 호출되는 방식에 따라 런타임에 동적으로 결정**된다는 것이다.

즉, 같은 코드라도 "누가 불렀는지, 어떻게 불렀는지"에 따라 `this`의 값이 달라진다.<br />
이게 JavaScript에서 `this`가 헷갈리는 가장 큰 이유다. 🙂

## [예제 22-1] 객체 리터럴에서의 `this`

```js
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

**객체 리터럴**에서 메서드를 정의하고 호출하면, 메서드 내부의 this는 **해당 메서드를 호출한 객체**를 가리킨다.<br />
위 예제에서는 `circle.getDiameter()`라고 호출했기 때문에, `this`는 `circle` 객체가 된다.<br />
따라서 `this.radius`는 `circle.radius`와 같고, 반환값은 10이다.<br />

## [예제 22-2] 생성자 함수에서의 `this`

```js
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

**생성자 함수**에서 `this`는 **새로 만들어지는 인스턴스**를 가리킨다.<br />
`new Circle(5)`를 실행하면 다음과 같은 과정이 일어난다.

1️⃣ 빈 객체 `{}`를 만든다.<br />
2️⃣ 이 객체를 `this`에 바인딩한다.<br />
3️⃣ 생성자 함수 본문을 실행한다 (`this.radius = radius`).<br />
4️⃣ 완성된 객체를 반환한다.

즉, 위 코드에서 `this`는 **circle 인스턴스**를 가리키고,<br />
`getDiameter()`는 10을 반환한다.

## [예제 22-3] 메서드 호출에서의 `this`

```js
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

이 예제는 22-01과 같은 맥락이다.<br />
메서드를 객체가 호출했으므로, `this`는 그 객체 자신을 가리킨다.<br />
따라서 `this.radius`는 `circle.radius`와 동일하다.<br />

## [예제 22-4] 생성자 함수 호출에서의 `this`

```js
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

생성자 함수와 함께 `new`를 붙여 호출하면, `this`는 새로 만들어진 인스턴스를 가리킨다.<br />
따라서 `circle.radius`는 5가 되고,<br />
`circle.getDiameter()`는 10을 반환한다.<br />

## [예제 22-5] 일반 함수 호출에서의 `this`

```js
function square(number) {
  console.log(this); // window (브라우저)
  return number * number;
}

square(2);
```

일반 함수 호출에서의 `this`가 **전역 객체**(_window_ 또는 Node.js에서는 _global_)에 바인딩된다.<br />
다만 strict mode에서는 `undefined`가 바인딩된다.<br />

즉, 객체의 메서드로 호출된 게 아닌 독립적으로 호출된 함수라면,<br />
`this`는 특별히 참조할 대상이 없으므로 **전역 객체로 연결**된다.

이거를 비유를 해보자면,<br />
"**주인 없는 떠돌이 개**" 같은 함수다.<br />
주인이 없으니 기본적으로 마을 회관(전역 객체) 소속으로 취급된다.<br />
하지만 strict mode에서는 떠돌이 개를 금지시켜서 아예 `undefined`가 되는 것이다.

# 22.2 함수 호출 방식과 `this` 바인딩

JavaScript에서 `this`가 무엇을 참조하는지는 함수가 **어떻게 호출되었는지**에 따라 결정된다.<br />
크게 네 가지 경우가 있다.

1️⃣ 일반 함수 호출<br />
2️⃣ 메서드 호출<br />
3️⃣ 생성자 함수 호출<br />
4️⃣ `Function.prototype` 메서드(`call`, `apply`, `bind`)를 통한 간접 호출

## 22.2.1 일반 함수 호출

일반 함수로 호출했을 때, 함수 내부의 this는 전역 객체에 바인딩된다.<br />
(strict mode에서는 `undefined`가 바인딩된다는 점, 잊지 말기! 🤓)

### [예제 22-06]

```js
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
const foo = function () {
  console.dir(this);
};

// 동일한 함수도 다양한 방식으로 호출할 수 있다.

// 1. 일반 함수 호출
foo(); // window

// 2. 메서드 호출
const obj = { foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
new foo(); // foo {}
```

똑같은 함수 `foo`라도 호출 방식에 따라 `this`가 달라진다.<br />
1️⃣ 그냥 `foo()` → 일반 함수 호출이므로 `this === window` (strict mode라면 `undefined`)<br />
2️⃣ `obj.foo()` → 메서드 호출이므로 `this === obj`<br />
3️⃣ `new foo()` → 생성자 호출이므로 `this === 새로 생성된 인스턴스`

다시 말하지만,<br />
`this`는 "함수를 어떻게 불렀는가"에 따라 달라진다.

### [예제 22-07] 중첩 함수에서 `this`

```js
function foo() {
  console.log("foo's this: ", this); // window

  function bar() {
    console.log("bar's this: ", this); // window
  }

  bar();
}

foo();
```

`foo()`는 일반 함수 호출이므로 `this`는 전역 객체(window)를 가리킨다.<br />
`bar()`도 일반 함수 호출이므로 `this`는 역시 전역 객체(window)를 가리킨다.<br />
즉, **중첩 함수도 일반 함수로 호출**되면 `this`는 **전역 객체에 바인딩**된다.

### [예제 22-08] strict mode에서의 `this`

```js
function foo() {
  'use strict';

  console.log("foo's this: ", this); // undefined

  function bar() {
    console.log("bar's this: ", this); // undefined
  }

  bar();
}

foo();
```

strict mode에서는 일반 함수 호출 시 `this`가 **전역 객체에 바인딩되지 않는다**.<br />
대신 `undefined`가 바인딩된다.<br />
이는 암묵적으로 전역 객체를 참조하는 실수를 막기 위한 조치다.

### [예제 22-09] 메서드 내부 중첩 함수의 `this`

```js
const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // obj
    console.log("foo's this.value: ", this.value); // 100

    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }

    // 메서드 내부에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는
    // 전역 객체가 바인딩된다.

    bar();
  },
};

obj.foo();
```

콜백 함수가 일반 함수로 호출된다면<br />
콜백 함수 내부의 `this`에도 전역 객체가 바인딩된다.<br />
즉, 어떠한 함수라도 일반 함수로 호출되면 `this`에 전역 객체가 바인딩된다.

### [예제 22-10] 콜백 함수에서 `this`

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this:", this); // { value: 100, foo: f }
    console.log("foo's this.value:", this.value); // 100

    // setTimeout에 전달된 콜백은 일반 함수로 호출된다.
    setTimeout(function () {
      console.log("callback's this:", this); // window
      console.log("callback's this.value:", this.value); // 1
    }, 100);
  },
};

obj.foo();
```

JavaScript에서 `setTimeout`과 같은 빌트인 함수에 콜백을 전달하면, 그 콜백은 **일반 함수처럼 호출**된다.<br />
즉, `setTimeout` 내부에서 우리가 전달한 콜백을 특별한 바인딩 규칙 없이 그냥 "_일반 함수 호출_"로 실행시키는 것이다.<br />
일반 함수 호출의 규칙에 따르면, `this`는 전역 객체(window 또는 Node.js에서는 global)에 바인딩된다.

이 코드를 실행해보면 먼저 `obj.foo()`가 호출된다.<br />
`foo`는 객체의 메서드로 불렸으므로 `this`는 `obj`를 가리킨다.<br />
따라서 `foo` 내부에서 찍은 `this`는 `{ value: 100, foo: f }` 이고, `this.value`는 100이 된다.

하지만 문제는 `setTimeout`이다.<br />
100ms 후 실행되는 콜백은 `obj`와 아무 관계가 없다.<br />
단순히 일반 함수로 호출되므로 `this`는 **전역 객체**를 가리킨다.<br />
코드 맨 위에서 `var value = 1;` 을 선언했기 때문에, 이 변수는 전역 객체의 프로퍼티 `window.value`가 된다.<br />
따라서 콜백 내부에서 `this.value`를 출력하면 우리가 의도한 100이 아니라 **전역 객체의 1**이 나오게 된다.

즉, **메서드 안에서 콜백**을 쓰면 그 콜백은 상위 메서드의 `this`를 그대로 이어받지 못하고,<br />
다시 기본 규칙에 따라 **전역 객체**를 바라보게 된다.

이게 바로 콜백 함수에서 `this`가 유실되는 문제다. 🤓

### [예제 22-11] 해결책 ① `that = this`

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    const that = this; // 현재 this(obj)를 지역 변수에 저장

    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  },
};

obj.foo();
```

JavaScript 초창기부터 가장 널리 쓰이던 방식은 바로 `this`를 지역 변수에 담아두는 것이다.<br />
보통 변수 이름을 `that`이라고 해서 "**this를 잃지 않도록 붙잡아 두는 장치**"처럼 사용한다.

이 코드에서 `obj.foo()`를 호출하면 당연히 `foo` 내부의 `this`는 `obj`를 가리킨다.<br />
그런데 우리가 곧 실행할 콜백 함수는 일반 함수로 호출될 것이고, 그러면 `this`는 전역 객체로 바뀌어버린다.<br />
하지만 그 전에 미리 `const that = this;`로 `obj`를 `that`이라는 이름에 저장해 두었기 때문에,<br />
나중에 콜백 안에서 `that.value`를 참조하면 올바르게 100이 출력된다. 🥳

### [예제 22-12] 해결책 ② `bind` 활용

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(
      function () {
        console.log(this.value); // 100
      }.bind(this),
      100
    ); // this를 obj로 바인딩
  },
};

obj.foo();
```

앞에서 본 것처럼 `setTimeout`의 콜백은 기본적으로 일반 함수 호출 규칙을 따르기 때문에, `this`가 전역 객체로 바뀌어버렸다.<br />
이를 방지하기 위해 `bind`를 사용할 수 있다.

`bind`는 함수의 `this`를 **특정 객체에 영구적으로 묶은 새로운 함수를 반환**한다.<br />
위 예제에서는 `foo` 메서드가 실행될 당시의 `this`인 `obj`를 `.bind(this)`로 연결해주었다.<br />
이렇게 하면 콜백 함수가 실행될 때 `this`가 전역 객체로 흘러가지 않고, 항상 `obj`를 가리키게 된다.<br />
따라서 `this.value`는 100을 정상적으로 출력한다. 🥳

### [예제 22-13] 해결책 ③ 화살표 함수 활용

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value, 100)); // 100
  },
};

obj.foo();
```

ES6에서 도입된 화살표 함수는 일반 함수와 달리 자체적인 `this`를 생성하지 않는다.<br />
대신, **자신이 정의된 위치(상위 스코프)의 this를 그대로 물려받는다**.

위 코드에서 `setTimeout` 콜백을 화살표 함수로 작성하면,<br />
그 안의 `this`는 `obj.foo`가 실행될 때의 `this`, 즉 `obj`를 그대로 따라간다.<br />
그래서 별도의 바인딩 처리 없이도 100이 출력된다. 🥳

## 22.2.2 메서드 호출

### [예제 22-14]

```js
const person = {
  name: 'Lee',
  getName() {
    return this.name;
  },
};

console.log(person.getName()); // Lee
```

이번 예제는 콜백 문제가 아니라, **메서드 호출 시 `this`가 어떻게 결정되는지**를 다시 보여주는 기본 예제다.

**객체의 프로퍼티로 접근해서 호출한 함수**는 **메서드 호출**이 된다.<br />
이 경우 `this`는 해당 메서드를 호출한 객체를 가리킨다.<br />
위 코드에서 `person.getName()`을 실행하면,<br />
`this`는 `person`이고,<br />
따라서 `this.name`은 "Lee"가 된다.

### [예제 22-15] 메서드 참조에 의한 `this` 손실

```js
const anotherPerson = {
  name: 'Kim',
};

// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName());
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.

// 브라우저: '' (window.name 기본값)
// Node.js: undefined
```

원래 `getName` 메서드는 `person` 객체에 정의되어 있었다. (위의 예제)<br />
그런데 `anotherPerson.getName = person.getName;` 처럼 다른 객체 프로퍼티로 복사할 수 있다.<br />
이제 `anotherPerson`도 `getName` 메서드를 갖게 된다.

따라서 `anotherPerson.getName()`을 호출하면 호출 주체가 `anotherPerson`이므로 `this`는 `anotherPerson`에 바인딩된다.<br />
그 결과 "Kim"이 출력된다.

하지만 `const getName = person.getName;` 처럼 메서드를 변수에 할당하면 단순히 함수 값만 복사된 것이고,<br />
그 함수가 어느 객체와도 연결되어 있지 않다.<br />
이렇게 독립된 함수를 일반 함수처럼 호출하면 `this`는 전역 객체에 바인딩된다.

즉, **메서드**는 "객체 안에 갇혀 있는 것"이 아니라 **단순히 객체의 프로퍼티로 등록된 함수**일 뿐이다.<br />
그래서 호출 방식에 따라 `this` 바인딩이 달라진다.

### [예제 22-16] 프로토타입 메서드에서의 `this`

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person('Lee');

// getName 메서드를 호출한 객체는 me
console.log(me.getName()); // Lee

// 프로토타입 변경
Person.prototype.getName = 'Kim';

// getName 메서드를 호출한 객체는 Person.prototype
console.log(Person.prototype.getName()); // Kim
```

이 예제는 **프로토타입 메서드 내부에서의 `this` 바인딩**을 보여준다.

먼저 `Person` 생성자 함수로부터 `me`라는 인스턴스를 만들면, `me`는 `Person.prototype`을 상속받는다.<br />
`getName` 메서드는 `Person.prototype`에 정의되어 있지만, 호출 주체는 `me`이므로 `this`는 `me`를 가리킨다.<br />
따라서 `me.getName()`은 "Lee"를 출력한다.

그런데 `Person.prototype.name = 'Kim'`을 추가하면, `Person.prototype` 객체 자체도 `name` 프로퍼티를 갖게 된다.<br />
이때 `Person.prototype.getName()`을 직접 호출하면 호출 주체가 `Person.prototype`이므로, `this`는 `Person.prototype`이 된다. 따라서 결과는 "Kim".

즉, **프로토타입 메서드 내부의 `this`는 결국 메서드를 호출한 객체에 바인딩된다**는 점을 확인할 수 있다. 🤓

### [예제 22-17] 생성자 함수 호출에서의 `this`

```js
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체 생성
const circle1 = new Circle(5);

// 반지름이 10인 Circle 객체 생성
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

생성자 함수는 `new` 연산자와 함께 호출되며, 이 경우 함수 내부의 `this`는 **새로 만들어지는** 인스턴스를 가리킨다.

위 코드에서 `new Circle(5)`를 실행하면 새로운 객체가 생성되고, `this.radius = 5`가 설정된다.<br />
동시에 `getDiameter` 메서드도 인스턴스의 프로퍼티로 붙는다.<br />
결과적으로 `circle1.getDiameter()`는 10을 반환한다.

마찬가지로 `new Circle(10)`을 실행하면 또 다른 객체가 생성되고, 이 객체의 `radius`는 10이 된다.<br />
따라서 `circle2.getDiameter()`는 20을 반환한다.

즉, **생성자 함수 호출 시 `this`는 생성될 인스턴스 그 자체**라는 사실을 보여주는 예제다.

### [예제 22-18] `new` 연산자를 빼먹었을 때의 `this`

```js
const circle3 = Circle(15);

console.log(circle3); // undefined
console.log(radius); // 15
```

여기서 의도는 new Circle(15)로 새로운 객체를 생성하는 것이었는데, 실수로 new를 빼고 Circle(15)만 호출한 예제다.<br />
이 경우 JavaScript는 단순히 Circle 함수를 **일반 함수 호출**로 취급한다.

일반 함수 호출에서는 this가 전역 객체에 바인딩된다.<br />
그래서 Circle 함수 내부의 this.radius = radius;는 새 객체에 프로퍼티를 추가하는 것이 아니라, 전역 객체(window 또는 global)에 radius = 15라는 전역 변수를 만들어버린다.

결과적으로 circle3는 undefined가 되고, 전역 변수 radius가 생겨서 console.log(radius)가 15를 출력한다.

이 예제는 "**생성자 함수는 반드시 new와 함께 호출해야 한다**"는 점을 강조한다.<br />new를 빼먹으면 말짱 도루묵~

### [예제 22-19] `call`/`apply`로 `this`를 바꿔치기

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding()); // window
console.log(getThisBinding.apply(thisArg)); // { a: 1 }
console.log(getThisBinding.call(thisArg)); // { a: 1 }
```

이 예제는 `Function.prototype.apply`와 `Function.prototype.call`을 이용해서 `this`를 원하는 객체로 바꾸는 방법을 보여준다.

· 그냥 `getThisBinding()`을 호출하면 일반 함수 호출이므로 `this === window`.<br />
· `getThisBinding.apply(thisArg)`를 호출하면, `apply`의 첫 번째 인수 `thisArg`가 함수 내부의 `this`로 바인딩된다. 따라서 반환값은 `{ a: 1 }`.<br />
· `call`도 마찬가지로 동작한다. `apply`는 인수를 **배열**로 받고, `call`은 인수를 개별로 받는다는 차이만 있다.

즉, `call`과 `apply`는 원하는 객체를 강제로 `this`로 바꿔 함수 실행을 시킬 수 있는 메서드다.

_🤓 `call`과 `apply`의 차이점에 대해 살짝만 더 알아보고 갈까?_

> `call`과 `apply`는 둘 다 `this`를 바인딩해서 함수 실행을 시켜주는 메서드인데, **인수를 넘기는 방식이 다르다**.

#### `call`

```js
func.call(thisArg, arg1, arg2, arg3, ...);
```

첫 번째 인수 : this로 사용할 객체<br />
두 번째부터 : 함수에 전달할 인수들을 **하나씩 개별로** 나열

예를 들면,

```js
function add(x, y) {
  return x + y;
}

console.log(add.call(null, 2, 3)); // 5
```

여기서 `thisArg`는 `null`이고, 2와 3이 인수로 직접 전달된다.

#### `apply`

```js
func.apply(thisArg, [arg1, arg2, arg3, ...]);
```

첫 번째 인수 : `this`로 사용할 객체<br />
두 번째 인수 : 배열(또는 유사 배열 객체)로 인수를 한 번에 전달

예를 들면,

```js
function add(x, y) {
  return x + y;
}

console.log(add.apply(null, [2, 3])); // 5
```

즉,<br />
`call` : 인수들을 하나씩 나열 → `func.call(obj, a, b, c)`<br />
`apply` : 인수들을 배열에 담아서 전달 → `func.apply(obj, [a, b, c])`

### [예제 22-20] `apply`와 `call`의 인수 전달 방식

```js
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.iterator): f]
// { a: 1 }

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.iterator): f]
// { a: 1 }
```

해당 예제에서 `apply`와 `call`의 가장 큰 차이, 즉 **인수 전달 방식**을 보여준다.

· `apply`는 두 번째 인수로 배열(또는 유사 배열 객체)을 받는다. 이 배열의 요소들이 함수의 인수로 풀려서 전달된다.<br />
· `call`은 `thisArg`(첫 번째 인수)를 제외한 나머지 인수들을 그냥 개별적으로 쉼표로 나열해서 전달한다.

두 방식 모두 첫 번째 인수는 `this`로 사용할 객체(`thisArg`)이며, 여기서는 `{ a: 1 }`이므로 반환값이 `{ a: 1 }`로 동일하다. 단지 인수 전달 형식만 다를 뿐.

### [예제 22-21] `arguments` 객체를 배열로 변환하기

```js
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

`arguments` 객체는 배열처럼 생겼지만 진짜 배열은 아니다. 즉, 배열 메서드(`map`, `forEach` 등)를 직접 쓸 수 없다.<br />
이럴 때 `Array.prototype.slice` 같은 배열 메서드를 `call`이나 `apply`를 이용해 빌려쓰면 `arguments`를 배열로 변환할 수 있다.

위 코드에서 `Array.prototype.slice.call(arguments)`는 `arguments`를 얇게 잘라서 진짜 배열로 바꾼다.<br />
그 결과 `[1, 2, 3]` 배열이 생성된다.

### [예제 22-22] `bind`로 `this` 고정하기

```js
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된
// getThisBinding 함수를 새롭게 생성해 반환한다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg)()); // { a: 1 }
```

`bind` 메서드는 `apply`나 `call`과 달리 즉시 함수를 실행하지 않는다.<br />
대신, `this`가 고정된 새로운 함수를 반환한다.

위 예제에서 `getThisBinding.bind(thisArg)`는 `this`가 `{ a : 1 }`로 묶인 새로운 함수를 만들어 돌려준다.<br />
그 함수를 실행하면 `this`는 항상 `{ a: 1 }`이 된다.

즉, `bind`는 **`this`를 영구적으로 묶어 놓은 새로운 함수를 만드는 도구**다.

### [예제 22-23] 콜백 함수에서 `this` 유실

```js
const person = {
  name: 'Lee',
  foo(callback) {
    // ①
    setTimeout(callback, 100);
  },
};

person.foo(function () {
  console.log(`Hi! My name is ${this.name}.`); // ② Hi! My name is .
  // 일반 함수로 호출된 콜백 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
  // 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
  // Node.js 환경에서 this.name은 undefined다.
});
```

`person.foo`를 호출하면 `this`는 `person`을 가리킨다.<br />
하지만 문제는 `foo`가 실행 중 `setTimeout`으로 넘긴 콜백 함수다.<br />
이 콜백은 일반 함수 호출로 실행되기 때문에, 내부의 `this`는 전역 객체(window 또는 Node.js에서는 global)로 바뀐다.

그 결과, 브라우저 환경에서는 `window.name`을 참조하는데, 이는 기본값이 빈 문자열 `""`라서 "Hi! My name is ."가 출력된다.<br />
Node.js 환경에서는 `this.name`이 정의되지 않았기 때문에 "undefined"가 출력된다.

### [예제 22-24] `bind`로 `this`를 유지하기

```js
const person = {
  name: 'Lee',
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`Hi! My name is ${this.name}.`); // Hi! My name is Lee.
});
```

앞선 예제에서 끊겨버린 `this`를 유지하려면, 콜백이 실행되기 전에 미리 `this`를 고정해야 한다.<br />
여기서는 `bind` 메서드를 사용했다.

`callback.bind(this)`는 `this`가 `person`으로 바인딩된 새로운 함수를 반환한다.<br />
이 새 함수가 `setTimeout`에 전달되므로, 나중에 일반 함수 호출로 실행되더라도 `this`는 여전히 `person`을 가리킨다.<br />
따라서 "Lee"라는 이름이 제대로 출력된다.

즉, `bind`를 사용하면 콜백 함수 내부에서도 바깥 객체의 `this`를 유지할 수 있다.

---

지금까지 함수 호출 방식에 따라 `this` 바인딩이 동적으로 결정되는 것에 대해 살펴보았다.<br />
이를 정리해 보면 다음과 같다.

| 함수 호출 방식                                                   | `this` 바인딩                                                                |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| 일반 함수 호출                                                   | 전역 객체                                                                    |
| 메서드 호출                                                      | 메서드를 호출한 객체                                                         |
| 생성자 함수 호출                                                 | 생성자 함수가 (미래에) 생성할 인스턴스                                       |
| `Function.prototype.apply`/`call`/`bind` 메서드에 의한 간접 호출 | `Function.prototype.apply`/`call`/`bind` 메서드에 첫 번째 인수로 전달한 객체 |
