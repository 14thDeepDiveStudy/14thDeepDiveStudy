# 5회차 내용 (22장 this)

## 22. this

### 22.1 What is "this"?

> 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수.**

- 왜 `this`가 있을까?
    - 같은 함수 본문을 여러 “수신자(객체)”와 재사용하기 위함이다. 자세히는 **“누가 이 함수를 불렀는지”** 를 런타임에 늦게 결합하기 위한 장치.  
그래서 `this`는 **정의** 시점이 아니라 **호출** 시점에 결정된다. 비유하자면 가면을 쓴다고 생각해보면 된다. 어떤 가면을 쓰느냐에 따라 `this`의 모습이 달라진다.

![가면 3개](./assets/image.png)

>핵심은 `this`가 문맥상 **"어떻게"** 정의되는가이다. 때문에 콜사이트(호출) 규칙이 `this`에게는 중요하다.

- `this`는 단순한 값이라기보다 함수 호출 방식에 따라 결정되는 동적인 바인딩이라서 문맥에 따라 `this`가 가리키는 게 달라진다.   
전역에서 쓰면 `window`나 `global`을, 객체 메서드 안에서는 그 **객체**를, 생성자 함수 안에서는 새로 만들어지는 **인스턴스**를 가리키는 식으로.

   - 딥다이브 책에서는 "객체지향 프로그래밍", "생성자 함수" 같은 자바스크립트 지식을 언급하면서 `this`에 대해 알려주고 있다.  
     생성자 함수가 어떻게 인스턴스를 만들고 `this`를 바인딩하는지, 객체가 데이터+행동의 묶음이라는 구조적 관점 정도는 알아야   
     왜 `this`가 그렇게 정의됐는지 이해하기 쉽?다.

---

### 일반 함수

- 일반 함수에서의 `this`는 **전역 객체(window / globalThis)** 에 바인딩 된다. "strict mode"에선 `undefined`.
- 중첩 함수, 콜백 함수, `setTimeout`도 결국 **일반 함수 호출** 이므로 `this`는 동일한 규칙 적용.

```js
function foo() {
  console.log(this); // 전역 객체 (window)
}

foo();

'use strict';
function bar() {
  console.log(this); // undefined
}
bar();
```

`this`는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수라 객체를 생성하지 않는 일반 함수에서 `this`는 의미가 없다. 비유하자면 "너 주인 없잖아" 심지어 엄근진 모드에서는 `undefined`로 던져진다.

---

### 생성자 함수
 - `new` 키워드와 함께 호출할 때 함수 내부에서 빈 객체를 만들고, 그렇게 만들어진 객체의 프로토타입을 연결시키면  
 함수 내부의 `this`가 호출 시점에 그 객체를 참조하게 된다. 또한 이 함수의 마지막에 명시적으로 `return`을 하지 않아도 새 객체가 반환이 된다.   
이 과정을 이해해야 "`this`가 인스탄ㅡ스를 가리킨다"라는 말이 자연스럽게 와닿을 수 있다.

```js
function Counter(start = 0) {
  // 1. {} 새 객체 생성
  // 2. 새 객체의 [[Prototype]]을 Counter.prototype으로 설정
  // 3. 함수 본문 실행하며 this === 새 객체
  // 4. 명시적 return이 없으면 새 객체가 반환
  this.value = start;
}
Counter.prototype.inc = function () {
  this.value += 1;
  return this.value;
};

const c1 = new Counter(10);
console.log(c1.inc()); // 11, 프로토타입 체인으로 Counter.prototype.inc에 접근
console.log(c1.value); // 11

// new가 “인스턴스 생성 + this 바인딩”을 해주기 때문에, 생성자 내부 this는 방금 만들어진 인스턴스를 가리킨다.
```

---

### 프로퍼티와 메서드 
- 객체를 단순한 데이터 묶음으로만 보면 `this`가 왜 필요한지 감이 안올텐데 객체지향 관점에서 프로퍼티(데이터)와 메서드(행동)를 한 단위로   
묶어 **관리** 한다는 시각을 가지면 "메서드 실행 시 그 메서드가 속한 객체의 데이터를 참조"할 필요가 생긴다. 왜? 동작(메서드)이 상태(프로퍼티)를 조작해야하기 때문.   
 그렇게 메서드 안에서 자연스럽게 `this`라는 참조점이 필요해지는 것!

> 객체의 메서드가 실행될 때는 보통 객체 자신의 상태(프로퍼티)를 바탕으로 뭔가 해야 하니까 “나 자신(객체)”을 가리킬 방법이 필요하다. → `this`

```js
const user = {
  name: "남일쒸",
  helloWorld: function() {
    console.log("안녕하세요, 저는 " + this.name + "입니다.");
  }
};

user.helloWorld(); // 안녕하세요, 저는 남일쒸입니다.
```
```js
const other = user;
other.name = "hm bp";
other.helloWorld(); // 안녕하세요, 저는 hm bp입니다.
```

=> 만약 `this`가 아니라 `user`였다면 `user`변수명이 사라지거나 다른 것을 가리킬 경우 `other`가 깨지지만 `this`를 쓰면 항상 “메서드를 호출한 객체”를 가리키기 때문에, 객체가 어떤 변수명에 담겨있든 상관없이 자기 자신의 데이터를 참조할 수 있다.

---

### 22.2.4 명시적 바인딩 (call / apply / bind)

- 함수는 `Function.prototype`의 메서드인 `call`, `apply`, `bind`를 상속받음.

이 메서드들로 `this`를 강제로 원하는 객체에 바인딩할 수 있음.

```js
function greet(greeting) {
  console.log(`${greeting}, my name is ${this.name}`);
}

const user = { name: "남일쒸" };

greet.call(user, "Hello");  // Hello, my name is 남일쒸
greet.apply(user, ["Hi"]);  // Hi, my name is 남일쒸

const bound = greet.bind(user);
bound("Hey");               // Hey, my name is 남일쒸
```

`call` / `apply` / `bind` 차이

- `call`: 인자를 쉼표로 나열 → `func.call(thisArg, a, b, c)`

- `apply`: 인자를 배열로 전달 → `func.apply(thisArg, [a, b, c])`

- `bind`: 즉시 실행하지 않고, 새로운 함수 반환

비유하자면 감독이 배우(`this`)에게 역할을 강제로 지정하는 것

- `call`: 즉석에서 ‘넌 지금부터 악당 역할이다.’

- `apply`: ‘배역(배열)를 줄 테니 이거 따라 해’

- `bind`: ‘지금은 아니고, 다음 장면에 너 숨겨진 딸 역할이야’ 하고 예약

### 화살표 함수와 this

화살표 함수는 자신만의 `this`를 가지지 않는다. 대신 상위 스코프의 `this`를 그대로 계승하기 때문에 콜백 함수에서 유용하다.

```js
const obj = {
  name: "남일쒸",
  normal() {
    setTimeout(function() {
      console.log(this.name); // undefined (일반 함수 호출 → 전역 객체)
    }, 100);

    setTimeout(() => {
      console.log(this.name); // "남일쒸" (상위 스코프 obj의 this 계승)
    }, 100);
  }
};
obj.normal();
```



