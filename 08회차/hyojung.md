> ### 🤷🏻‍♀️ What the heck is a closure?

## 24장. 클로저

클로저란 "**함수와 그 함수가 선언된 렉시컬 환경의 조합**"을 말한다.<br />
쉽게 말해서 "**외부 함수보다 오래 살아남은 내부 함수가 외부 변수에 접근할 수 있는 현상**"을 뜻한다.<br />
보통 함수가 실행을 마치면 그 안에 있던 지역 변수는 사라지는 게 맞다.<br />
그런데 클로저가 만들어지면, 함수 실행이 끝나도 그 변수가 사라지지 않고 계속 살아 있어서 나중에도 접근할 수 있다.

왜 이게 중요하냐면,

- 첫째, 상태를 유지할 수 있고,
- 둘째, 데이터 은닉과 캡슐화를 할 수 있고,
- 셋째, 이벤트 핸들러나 비동기 코드에서 변수 관리를 안정적으로 할 수 있기 때문이다.

JavaScript의 다양한 패턴과 라이브러리에서 클로저가 기본으로 쓰이는 이유도 바로 이것이다.

---

### 24.1 렉시컬 스코프

근데 클로저를 제대로 이해하려면 먼저 렉시컬 스코프 개념부터 짚고 가야 된다.

JavaScript는 함수를 어디서 "호출"했는지가 아니라 어디서 "정의"했는지에 따라
상위 스코프가 정해지는 **렉시컬 스코프 규칙을 따른다**고 배웠다.

```js
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

이 코드에서 bar 함수가 foo 안에서 호출되었지만, bar는 전역에서 정의되었다.<br />
그래서 bar 함수 내부에서 참조하는 x는 전역 변수 x를 가리킨다.<br />
따라서 foo()를 호출하든 bar()를 직접 호출하든 모두 1이 출력된다.

이처럼 함수가 호출된 위치는 상위 스코프 결정에 아무런 영향을 주지 않는다.<br />
함수의 상위 스코프는 함수가 정의된 위치에 의해 정적으로 결정되고 변하지 않는다.

렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경에 의해 결정된다.

이것이 바로 렉시컬 스코프다.

---

### 24.2 함수 객체의 내부 슬롯 [[Environment]]

함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경,
즉 상위 스코프의 참조를 저장한다.

```js
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
  // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
  bar();
}

// 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
  console.log(x);
}

foo(); // 1
bar(); // 1
```

여기서 함수 bar는 전역에서 정의되었다. 따라서 함수 bar의 상위 스코프는 전역 렉시컬 환경이다. 함수 객체는 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 기억한다.

함수 객체의 내부 슬롯 [[Environment]]에 저장된 현재 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 바로 상위 스코프다. 또한 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장될 참조값이다. 함수 객체는 내부 슬롯 [[Environment]]에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 자신이 존재하는 한 기억한다. ❔❓

> 👩🏻‍🏫 _**이 부분 말이 복잡한데, 이해가 어렵다면 아래의 설명을 확인하세요!**_

### 핵심 개념부터 정리하면

함수가 만들어질 때, 그 함수는 "내가 어디서 태어났는지"를 기억하는 특별한 공간이 있음. 그게 바로 `[[Environment]]` 내부 슬롯임.

### 단계별로 살펴보자❕

**1 단계 : 함수가 정의될 때 무슨 일이 일어나는가?**

```js
const x = 1;

function outer() {
  const y = 2;

  // inner 함수가 여기서 "정의"됨!
  function inner() {
    console.log(x + y);
  }

  return inner;
}
```

여기서 inner 함수가 정의되는 순간, JavaScript 엔진은 다음과 같이 작동함:

1. 현재 실행 중인 실행 컨텍스트를 확인함 → 지금은 outer 함수의 실행 컨텍스트가 실행 중
2. 그 실행 컨텍스트의 렉시컬 환경을 가져옴 → outer의 렉시컬 환경 (y=2가 있는 환경)
3. inner 함수 객체의 [[Environment]] 슬롯에 그 참조를 저장함
   즉, inner 함수는 태어날 때부터 "아, 나는 outer 함수 안에서 태어났구나. 그 환경을 기억해두자!"라고 하는 거임.

**2 단계 : 함수가 호출될 때 무슨 일이 일어나는가?**

```js
const innerFunc = outer(); // outer 실행 완료, inner 함수 반환
innerFunc(); // inner 함수 호출
```

innerFunc()가 호출되면:

1. 새로운 함수 실행 컨텍스트가 만들어짐 (inner 함수용)
2. 새로운 렉시컬 환경도 만들어짐 (inner 함수용)
3. 이때 외부 렉시컬 환경에 대한 참조를 설정해야 하는데. . .
4. 여기서 inner 함수의 [[Environment]] 슬롯에 저장된 값을 꺼내서 사용함!

### 왜 이렇게 복잡하게 하는가?

```js
const x = 1;

function outer() {
  const x = 10;

  function inner() {
    console.log(x); // 어떤 x를 출력할까?
  }

  return inner;
}

const innerFunc = outer();
innerFunc(); // 10이 출력됨
```

만약 [[Environment]] 슬롯이 없다면:

- inner 함수는 호출될 때마다 "내가 어디서 정의되었는지" 알 수 없음. .
- 그럼 전역의 x = 1을 참조할 수도 있고, 엉뚱한 값을 가져올 수도 있음. .
  하지만 [[Environment]] 슬롯 덕분에:
- inner 함수는 항상 "내가 outer 안에서 정의되었다"는 걸 기억함!
- 그래서 outer의 x = 10을 정확히 참조할 수 있음!
  ![](https://velog.velcdn.com/images/growing-hyo/post/fb13238d-5a50-4f52-aac0-6e4046d68cec/image.png)

### 핵심 정리

```js
// 1. 함수 정의 시점: [[Environment]]에 상위 스코프 저장
function makeFunc() {
  const data = 'stored';

  return function () {
    // 이 함수의 [[Environment]]에 makeFunc의 렉시컬 환경 참조가 저장됨!
    console.log(data);
  };
}

// 2. 함수 호출 시점: [[Environment]]의 참조를 "외부 렉시컬 환경에 대한 참조"로 사용
const func = makeFunc();
func(); // [[Environment]]에 저장된 참조 덕분에 'stored' 출력 가능
```

결국 [[Environment]] 슬롯은 함수가 "어디서 태어났는지"를 평생 기억하는 DNA 같은 역할을 하는 거임.<br />
그래서 나중에 어디서 호출되든 상관없이 자신이 정의된 환경을 찾아갈 수 있는 거!

---

### 24.3 클로저와 렉시컬 환경

이제 이 개념을 바탕으로 클로저를 보자.

```js
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  }; // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

outer 함수를 호출(③)하면 outer 함수는 중첩 함수 inner를 반환하고 생명 주기를 마감한다.<br />
즉 outer 함수의 실행이 종료되면 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거된다.<br />
이때 outer 함수의 지역 변수 x와 변수 값 10을 저장하고 있던 outer 함수의 실행 컨텍스트가 제거되었으므로 outer 함수의 지역 변수 x 또한 생명 주기를 마감한다.

따라서 outer 함수의 지역 변수 x는 더는 유효하지 않게 되어 x 변수에 접근할 수 있는 방법은 달리 없어 보인다.

그러나 위 코드의 실행 결과(④)는 outer 함수의 지역 변수 x의 값인 10이다.<br />
이미 생명 주기가 종료되어 실행 컨텍스트 스택에서 제거된 outer 함수의 지역 변수 x가 다시 부활이라도 한듯이 동작하고 있다.

![](https://velog.velcdn.com/images/growing-hyo/post/6172f4d1-409e-453b-be62-2f1c7b7765f3/image.jpg)

이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우
중첩 함수는 이미 생명 주기가 종료된 외부 함수의 변수를 참조할 수 있다.<br />
이러한 중첩 함수를 클로저라고 부른다.

outer 함수가 평가되어 함수 객체를 생성할 때 (①) 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 전역 렉시컬 환경을 outer 함수 객체의 [[Environment]] 내부 슬롯에 상위 스코프로서 저장한다.
outer 함수의 실행이 종료하면 inner 함수를 반환하면서 outer 함수의 생명 주기가 종료된다(③). 이때 **outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거되지만 outer 함수의 렉시컬 환경까지 소멸하는 것은 아니다**.

> #### _여기서 중요한 건 실행 컨텍스트와 렉시컬 환경의 차이를 이해하는 것이다._
>
> **실행 컨텍스트**는 함수가 "실행될 때" 만들어지는 임시 작업 공간으로, 함수 실행이 끝나면 스택에서 제거되는 일회용이다. 반면 **렉시컬 환경**은 변수와 함수들이 실제로 저장되는 메모리 공간으로, 다른 곳에서 참조하고 있으면 계속 살아있는 재활용 가능한 공간이다.

outer 함수의 렉시컬 환경은 inner 함수의 [[Environment]] 내부 슬롯에 의해 참조되고 있고 inner 함수는 전역 변수 innerFunc에 의해 참조되고 있으므로 가비지 컬렉션의 대상이 되지 않기 때문이다. 가비지 컬렉터는 누군가가 참조하고 있는 메모리 공간을 함부로 해제하지 않는다.

> 즉, 다음과 같은 참조 체인이 만들어지는 것이다:

```bash
전역 변수 innerFunc
    ↓ 참조
inner 함수 객체
    ↓ [[Environment]]로 참조
outer의 렉시컬 환경 (x = 10이 저장된 곳)
```

이 참조 체인이 끊어지지 않아서 outer의 렉시컬 환경이 살아남는 것이다.

inner 함수를 호출(④)하면 inner 함수의 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 푸시된다. **그리고 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에는 inner 함수 객체의 [[Environment]] 내부 슬롯에 저장되어 있는 참조값이 할당된다.**

> 이 부분을 더 자세히 설명하자면, inner 함수가 호출될 때 JavaScript 엔진은 다음과 같은 과정을 거친다:

_**1. inner의 새로운 실행 컨텍스트와 렉시컬 환경을 생성한다.**_

```bash
// inner의 렉시컬 환경 구조 (개념적)
inner의렉시컬환경 = {
  환경레코드: { /* inner 함수 내부의 지역 변수들 */ },
  외부렉시컬환경참조: ??? // 여기에 뭘 넣을까?
}
```

_**2. inner 함수 객체의 [[Environment]] 슬롯에서 참조값을 꺼내온다.**_

```bash
참조값 = inner함수객체.[[Environment]] // outer의 렉시컬 환경에 대한 참조
```

_**3. 그 참조값을 새로 생성된 렉시컬 환경의 "외부 렉시컬 환경 참조"에 할당한다.**_

```bash
inner의렉시컬환경.외부렉시컬환경참조 = 참조값
```

_**4. 이제 완성된 스코프 체인으로 변수를 찾을 수 있다.**_

```bash
inner의 렉시컬 환경 (지역 변수들)
    ↓ 외부 렉시컬 환경에 대한 참조
outer의 렉시컬 환경 (x = 10)
    ↓ 외부 렉시컬 환경에 대한 참조
전역 렉시컬 환경
```

> 이 과정을 통해 inner 함수는 이미 실행이 끝난 outer 함수의 변수 x에 덥근할 수 있게 되는 것이다. 이것이 바로 [[Envrionment]] 슬롯의 핵심 역할이다.

⎯

중첩 함수 inner는 외부 함수 outer보다 더 오래 생존했다. 이때 함수는 외부 함수의 생존 여부와 상관없이 자신이 정의된 위치에 의해 결정된 상위 스코프를 기억한다.

클로저에 의해 참조되는 상위 스코프의 변수를 **자유 변수(free variable)**라고 부른다. 클로저란 "함수가 자유 변수에 대해 닫혀있다(closed)"라는 의미다. 즉, "자유 변수에 묶여있는 함수"라고 할 수 있다.

> 🤷🏻‍♀️ 솔직히.. 이 표현 너무 추상적이다.
> 도대체 "자유 변수에 대해 닫혀있다"는 것이 무슨 뜻임?

_**"자유 변수"란?**_

```js
function outer() {
  const count = 0; // ← 이게 자유 변수
  function inner() {
    console.log(count); // inner 함수 입장에서 count는 "자유 변수"
    //          ↑ 내 함수 안에서 선언하지 않았지만 사용하는 변수
  }
  return inner;
}
```

자유 변수 = 내 함수에서는 선언하지 않았지만, 사용하고 있는 외부 변수

_**"닫혀있다"는 표현의 의미**_
"닫혀있다" = "단단히 묶여있다" = "보호하고 있다" = "독립적으로 관리하고 있다"

⎯

다만 모든 중첩 함수가 클로저는 아니다.<br />
다음 예제를 보자.

```js
function foo() {
  const x = 1;
  const y = 2;

  // 일반적으로 클로저라고 하지 않는다.
  function bar() {
    const z = 3;

    debugger;
    // 상위 스코프의 식별자를 참조하지 않는다.
    console.log(z);
  }

  return bar;
}

const bar = foo();
bar();
```

중첩 함수 bar는 외부 함수 foo보다 더 오래 유지되지만
상위 스코프의 어떤 식별자도 참조하지 않는다.<br />
이처럼 상위 스코프의 어떤 식별자도 참조하지 않는 함수는 클로저가 아니다.

```js
function foo() {
  const x = 1;

  // bar 함수는 클로저였지만 곧바로 소멸한다.
  // 이러한 함수는 일반적으로 클로저라고 하지 않는다.
  function bar() {
    debugger;
    // 상위 스코프의 식별자를 참조한다.
    console.log(x);
  }
  bar();
}

foo();
```

중첩 함수 bar는 상위 스코프의 식별자를 참조하고 있으므로 클로저다.<br />
하지만 외부 함수의 foo의 외부로 중첩 함수 bar가 반환되지 않는다.<br />
즉, 외부 함수 foo보다 중첩 함수 bar의 생명 주기가 짧다.<br />
이런 경우 중첩 함수 bar는 클로저였지만 외부 함수보다 일찍 소멸되기 때문에 생명 주기가 종료된 외부 함수의 식별자를 참조할 수 있다는 클로저의 본질에 부합하지 않는다. (한국말은 끝까지 들어야죵)

```js
function foo() {
  const x = 1;
  const y = 2;

  // 클로저
  // 중첩 함수 bar는 외부 함수보다 더 오래 유지되며 상위 스코프의 식별자를 참조한다.
  function bar() {
    debugger;
    console.log(x);
  }
  return bar;
}

const bar = foo();
bar();
```

중첩 함수 bar는 상위 스코프의 x를 참조하고 있다.<br />
따라서 bar는 클로저다. 🎉<br />
그리고 외부 함수의 외부로 반환되어 외부 함수보다 더 오래 살아 남는다.

이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우
중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다.<br />
이러한 중첩 함수를 클로저라고 부른다. (중요한 건 반복해야죵)

클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고
중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.

---

### 24.4 클로저의 활용

클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다.<br />
즉, 상태가 의도치 않게 변경되지 않도록<br />
상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용한다.<br />
(마치 애나벨 인형을 아무나 함부로 건드리지 못하게 봉인해 놓은 것처럼...👻)

대표적인 예로 카운터 함수를 만들어볼 수 있다.

```js
// 카운트 상태 변수
let num = 0;

// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태를 1만큼 증가시킨다.
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드는 잘 동작하지만<br />
오류를 발생시킬 가능성을 내포하고 있는 좋지 않은 코드다.<br />
그 이유는 위 예제가 바르게 동작하려면 다음의 전제 조건이 지켜져야 하기 때문이다.

1. 카운트 상태(num 변수의 값)는 increase 함수가 호출되기 전까지 변경되지 않고 유지되어야 한다.
2. 이를 위해 카운트 상태(num 변수의 값)는 increase 함수만이 변경할 수 있어야 한다.

하지만 카운트 상태는 전역 변수를 통해 관리되고 있기 때문에
언제든지 누구나 접근할 수 있고 변경할 수 있다.<br />
이는 의도치 않게 상태가 변경될 수 있음을 의미한다.<br />
만약 누군가에 의해 의도치 않게 카운트 상태, 즉 전역 변수 num의 값이 변경되면<br />
이는 오류로 이어진다.

따라서 카운트 상태는 increase 함수만이 변경할 수 있도록 하는 것이 바람직하다. (마치 퇴마사 부부만 애나벨 인형을 다룰 수 있는 것처럼...👻)

```js
// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태 변수
  let num = 0;

  // 카운트 상태를 1만큼 증가시킨다.
  return ++num;
};

// 이전 상태를 유지하지 못한다.
console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

카운트 상태를 안전하게 변경하고 유지하기 위해<br />
전역 변수 num을 increase 함수의 지역 변수로 바꿨다.<br />
이제 num 변수의 상태는 increase 함수만이 변경할 수 있다.

하지만 increase 함수가 호출될 때마다 지역 변수 num은<br />
다시 선언되고 0으로 초기화되기 때문에 출력 결과는 언제나 1이다.

다시 말해, 상태가 변경되기 이전 상태를 유지하지 못한다.<br />
이전 상태를 유지할 수 있도록 클로저를 사용해보자.

```js
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가시킨다.
    return ++num;
  };
})();

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 실행되면<br />
즉시 실행 함수가 호출되고<br />
즉시 실행 함수가 반환한 함수가 increase 변수에 할당된다.<br />
increase 변수에 할당된 함수는<br />
자신이 정의된 위치에 의해 결정된 상위 스코프인<br />
즉시 실행 함수의 렉시컬 환경을 기억하는 클로저다.

즉시 실행 함수는 호출된 이후 소멸되지만<br />
즉시 실행 함수가 반환한 클로저는 increase 변수에 할당되어 호출된다.<br />
이때 즉시 실행 함수가 반환한 클로저는<br />
자신이 정의된 위치에 의해 결정된 상위 스코프인<br />
즉시 실행 함수의 렉시컬 환경을 기억하고 있다.<br />
따라서 즉시 실행 함수가 반환한 클로저는<br />
카운트 상태를 유지하기 위한 자유 변수 num을<br />
언제 어디서 호출하든지 참조하고 변경할 수 있다.

즉시 실행 함수는 한 번만 실행되므로<br />
increase가 호출될 때마다 num 변수가 재차 초기화될 일은 없을 것이다.<br />
또한 num 변수는 외부에서 직접 접근할 수 없는 은닉된 private 변수이므로<br />
전역 변수를 사용했을 때와 같이 의도되지 않은 변경을 걱정할 필요도 없기 때문에<br />
더 안정적인 프로그래밍이 가능하다.

이처럼 클로저는 상태가 의도치 않게 변경되지 않도록<br />
안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여<br />
상태를 안전하게 변경하고 유지하기 위해 사용한다.

카운터의 상태를 감소시킬 수 있도록 발전시켜보자.

```js
const counter = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저인 메서드를 갖는 객체를 반환한다.
  // 객체 리터럴은 스코프를 만들지 않는다.
  // 따라서 아래 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
  return {
    // num: 0, // 프로퍼티는 public하므로 은닉되지 않는다.
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    },
  };
})();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

위 예제에서 즉시 실행 함수가 반환하는 객체 리터럴은<br />
즉시 실행 함수의 실행 단계에서 평가되어 객체가 된다.<br />
이때 객체의 메서드도 함수 객체로 생성된다.<br />
객체 리터럴의 중괄호는 코드 블록이 아니므로 별도의 스코프를 생성하지 않는다.

위 예제의 increase, decrease 메서드의 상위 스코프는<br />
increase, decrease 메서드가 평가되는 시점에 실행 중인 실행 컨텍스트인<br />
즉시 실행 함수 실행 컨텍스트의 렉시컬 환경이다.<br />
따라서 increase, decrease 메서드가 언제 어디서 호출되든 상관없이<br />
increase, decrease 함수는 즉시 실행 함수의 스코프의 식별자를 참조할 수 있다.

위 예제를 생성자 함수로 표현하면 다음과 같다.

```js
const Counter = (function () {
  // ① 카운트 상태 변수
  let num = 0;

  function Counter() {
    // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  };

  return Counter;
})();

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

위 예제의 num(①)은<br />
생성자 함수 Counter가 생성할 인스턴스의 프로퍼티가 아니라
즉시 실행 함수 내에서 선언된 변수다.<br />
만약 num이 생성자 함수 Counter가 생성할 인스턴스의 프로퍼티라면(②)<br />
인스턴스를 통해 외부에서 접근이 자유로운 public 프로퍼티가 된다.<br />
하지만 즉시 실행 함수 내에서 선언된 num 변수는<br />
인스턴스를 통해 접근할 수 없으며,<br />
즉시 실행 함수 외부에서도 접근할 수 없는 은닉된 변수다.

생성자 함수 Counter는<br />
프로토타입을 통해 increase, decrease 메서드를 상속받는 인스턴스를 생성한다.<br />
increase, decrease 메서드는 모두 자신의 함수 정의가 평가되어 함수 객체가 될 때<br />
실행 중인 실행 컨텍스트인 즉시 실행 함수 실행 컨텍스트의 렉시컬 환경을 기억하는 클로저다.<br />
따라서 프로토타입을 통해 상속되는 프로토타입 메서드일지라도
즉시 실행 함수의 자유 변수 num을 참조할 수 있다.<br />
다시 말해, num 변수의 값은 increase, decrease 메서드만이 변경할 수 있다.

변수 값은 누군가에 의해 언제든지 변경될 수 있어 오류 발생의 근본적 원인이 될 수 있다. 외부 상태 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에서 부수 효과를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이기 위해 클로저는 적극적으로 사용된다.

다음은 함수형 프로그래밍에서 클로저를 활용하는 간단한 예제다.

```js
// 함수를 인수로 전달받고 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
function makeCounter(aux) {
  // 카운트상태를 유지하기 위한 자유 변수
  // ← 이 시점에서 makeCounter의 렉시컬 환경이 생성됨
  let counter = 0;

  // 클로저를 반환
  // ← 이 시점에서 익명 함수가 생성됨
  return function () {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 함수로 함수를 생성한다.
// makeCounter 함수는 보조 함수를 인수로 전달받아 함수를 반환한다.
const increaser = makeCounter(increase); // ①
console.log(increaser()); // 1
console.log(increaser()); // 2

// increaser 함수와는 별개의 독립된 렉시컬 환경을 갖기 때문에 카운터 상태가 연동하지 않는다.
const decreaser = makeCounter(decrease); // ②
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

이 코드에서 핵심적으로 이해해야 할 부분은 makeCounter 함수가 반환하는 함수는 "**자신이 생성됐을 때의 렉시컬 환경을 기억하는 클로저**"라는 점과 "**각 함수가 별개의 독립된 렉시컬 환경을 갖는다**"는 점이다.

#### _"자신이 생성됐을 때의 렉시컬 환경을 기억한다"의 의미_

먼저 "자신이 생성됐을 때의 렉시컬 환경을 기억한다"는 의미를 시간 순서로 살펴보자.<br />
①에서 `makeCounter(increase)`가 호출되면
makeCounter 함수의 실행 컨텍스트와 렉시컬 환경이 생성된다.<br />
이 렉시컬 환경에는 매개변수 aux(increase 함수)와 지역변수 counter(0)가 저장된다.<br />
그 다음 return문이 실행되면서 익명 함수가 생성되는데,<br />
바로 그 순간이 "자신이 생성됐을 때"이다.<br />
이때 생성되는 익명 함수의 [[Environment]] 내부 슬롯에는
현재의 makeCounter 렉시컬 환경에 대한 참조가 저장된다.<br />
(여기서 말하는 현재의 makeCounter 렉시컬 환경이란, 익명 함수가 생성되는 그 순간에 존재하는 makeCounter의 실행 환경을 말함)
makeCounter 실행이 완료되면 실행 컨텍스트는 스택에서 제거되지만,<br />
렉시컬 환경은 반환된 익명 함수가 참조하고 있기 때문에 메모리에서 사라지지 않는다.<br />
결국 increase 변수는 "increase 함수와 counter=0이 들어있는 특정 렉시컬 환경"을 기억하는 클로저가 되는 것이다.

#### _왜 각각 독립적인 환경을 가질까?_

②에서 makeCounter(decrease)가 호출될 때도 동일한 과정이 일어나지만,<br />
중요한 점은 완전히 새로운 makeCounter 실행 컨텍스트와 렉시컬 환경이 생성된다는 것이다.<br />
따라서 두 번째로 생성되는 익명 함수의 [[Environment]]에는 첫 번째와는 완전히 다른 렉시컬 환경(decrease 함수와 새로운 counter=0이 들어있는 환경)에 대한 참조가 저장된다.

이것이 바로 "각 함수가 별개의 독립된 렉시컬 환경을 갖는다"는 의미다.<br />
makeCounter 함수를 호출해 함수를 반환할 때 반환된 함수는 자신만의 독립된 렉시컬 환경을 갖는다. 이는 함수를 호출하면 그때마다 새로운 makeCounter 함수 실행 컨텍스트의 렉시컬 환경이 생성되기 때문이다. makeCounter 함수를 두 번 호출했기 때문에 두 개의 서로 다른 렉시컬 환경이 메모리에 존재하게 되고, increaser와 decreaser는 각각 다른 렉시컬 환경을 참조한다. 따라서 increaser()를 호출하면 첫 번째 환경의 counter 변수가 increase 함수에 의해 증가하고, decreaser()를 호출하면 두 번째 환경의 counter 변수가 decrease 함수에 의해 감소한다. 이 두 환경은 완전히 독립적인 메모리 공간에 존재하므로 서로 영향을 주지 않는다.

#### 시간 순서로 정리하면 아래와 같다:

```js
// 시간 1: makeCounter 호출
const increaser = makeCounter(increase);

// 시간 1-1: makeCounter 실행 중 - 렉시컬 환경 생성
// makeCounter의 환경 = { aux: increase, counter: 0, ... }

// 시간 1-2: return문 실행 - 익명 함수 생성
// 익명함수.[[Environment]] = makeCounter의 환경 (시간 1-1의 환경을 저장!)

// 시간 1-3: makeCounter 실행 완료 - 실행 컨텍스트는 제거됨
// 하지만 렉시컬 환경은 익명함수가 참조하고 있어서 살아남음

// 시간 2: 나중에 increaser 호출
increaser(); // 시간 1-1의 환경을 여전히 기억하고 사용!
```

이를 비유하면 makeCounter는 집을 짓는 공장과 같다. 호출할 때마다 새로운 집이 지어지고, 각 집마다 독립적인 counter 변수(가구)가 있다. increaser는 첫 번째 집의 열쇠이고 decreaser는 두 번째 집의 열쇠로, 각자 자신의 집 안에 있는 가구만 바꿀 수 있는 것이다.

---

### 24.5 캡슐화와 정보 은닉

캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다.<br />
캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라고 한다.

정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를<br />
외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터<br />
객체의 상태가 변경되는 것을 방지해 정보를 보호하고, 객체 간의 상호 의존성,<br />
즉 결합도를 낮추는 효과가 있다.

대부분의 객체지향 프로그래밍 언어는 클래스를 정의하고<br />
그 클래스를 구성하는 멤버(프로퍼티와 메서드)에 대하여<br />
public, private, protected 같은 접근 제한자를 선언하여 공개 범위를 한정할 수 있다.<br />
public으로 선언된 프로퍼티와 메서드는<br />
클래스 외부에서 참조할 수 있지만<br />
private으로 선언된 경우는<br />
클래스 외부에서 참조할 수 없다.

JavaScript는 public, private, protecte 같은 접근 제한자를 제공하지 않는다.<br />
따라서 JavaScript 객체의 모든 프로퍼티와 메서드는 기본저긍로 외부에 공개되어 있다.<br />
즉, 객체의 모든 프로퍼티와 메서드는 기본적으로 public하다.

```js
function Person(name, age) {
  this.name; // public
  let _age = age; // private

  // 인스턴스 메서드
  this.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };
}

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

위 예제의 name 프로퍼티는<br />
현재 외부로 공개되어 있어서 자유롭게 참조하거나 변경할 수 있다.<br />
즉, name 프로퍼티는 public하다.

하지만 \_age 변수는 Person 생성자 함수의 지역 변수이므로<br />
Person 생성자 함수 외부에서 참조하거나 변경할 수 없다.<br />
즉, \_age 변수는 private하다.

하지만 위 예제의 sayHi 메서드는 인스턴스 메서드이므로<br />
Person 객체가 생성될 때마다 중복 생성된다.

sayHi 메서드를 프로토타입 메서드로 변경하여 sayHi 메서드의 중복 생성을 방지해 보자.

```js
function Person(name, age) {
  this.name = name; // public
  let _age = age; // private
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다.
  console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
};
```

이때 Person.prototype.sayHi 메서드 내에서<br />
Person 생성자 함수의 지역 변수 `_age`를 참조할 수 없는 문제가 발생한다.

따라서 다음과 같이 즉시 실행 함수를 사용하여<br />
Person 생성자 함수와 Person.prototype.sayHi 메서드를 하나의 함수 내에 모아 보자.

```js
const Person = (function () {
  let _age = 0; // private

  // 생성자 함수
  function Person(name, age) {
    this.name = name;
    _age = age; // private
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다.
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };

  // 생성자 함수를 반환
  return Person;
})();

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined

// _age 변수 값이 변경된다!
me.sayHi(); // Hi! My name is Lee. I am 30.
```

위 패턴을 사용하면<br />
public, private, protected 같은 접근 제한자를 제공하지 않는<br />
JavaScript에서도 정보 은닉이 가능한 것처럼 보인다.<br />
즉시 실행 함수가 반환하는 Person 생성자 함수와<br />
Person 생성자 함수의 인스턴스가 상속받아 호출할 Person.prototype.sayHi 메서드는<br />
즉시 실행 함수가 종료된 이후 호출된다.<br />
하지만 Person 생성자 함수와 sayHi 메서드는<br />
이미 종료되어 소멸한 즉시 실행 함수의 지역 변수 `_age`를 참조할 수 있는 클로저다.

하지만 위 코드도 문제가 있다.<br />
Person 생성자 함수가 여러 개의 인스턴스를 생성할 경우<br />
위와 같이 `_age` 변수의상태가 유지되지 않는다는 것이다.

이는 Person.prototype.sayHi 메서드가<br />
단 한 번 생성되는 클로저이기 때문에 발생하는 현상이다.<br />
Person.prototype.sayHi 메서드는 즉시 실행 함수가 호출될 때 생성된다.<br />
이때 Person.prototype.sayHi 메서드는<br />
자신의 상위 스코프인 즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경의 참조를 [[Environment]]에 저장하여 기억한다.

> 이해가 잘 안 되는 나 자신을 위ㅎㅏ여. .<br /> > _**"자신의 상위 스코프인 즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경의 참조를 [[Environment]]에 저장하여 기억한다"**_ 이 표현을 단순하게 나눠보자.

위의 문장을 단계별로 나누면:

1. "자신"은 누구인가?
   "자신" = Person.prototype.sayHi 메서드 (함수)

2. "자신의 상위 스코프"는 무엇인가?
   Person.prototype.sayHi 메서드가 정의된 곳 = 즉시 실행 함수 내부

3. "즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경"은 무엇인가?
   즉시 실행 함수가 실행될 때 만들어진 환경 (\_age, person 함수 등이 들어있는 환경)

4. "참조를 [[Envrionment]]에 저장"은 무슨 말인가?
   sayHi 함수의 내부 슬롯에 그 환경의 메모리 주소를 저장

시간 순서로 정확히 무슨 일이 일어나는지 보자:

1. [STEP 01] 즉시 실행 함수가 실행됨

```js
// 즉시 실행 함수가 실행되면서 렉시컬 환경이 생성됨
즉시실행함수의_렉시컬환경 = {
  환경_레코드: {
    _age: 0,              // private 변수
    Person: function(...) // 생성자 함수
  },
  외부_렉시컬환경_참조: 전역_렉시컬환경
}
```

2. [STEP 02] sayHi 메서드가 정의됨 (중요한 순간❕)

```js
// 이 코드가 실행되는 순간:
Person.prototype.sayHi = function () {
  console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
};
```

바로 이때, sayHi 함수 객체가 생성되면서:

```js
sayHi함수객체 = {
  코드: 'console.log(`Hi! My name is ${this.name}. I am ${_age}.`);',
  [[Environment]]: 즉시실행함수의_렉시컬환경, // ← 여기에 참조 저장!
};
```

3. [STEP 03] 즉시 실행 함수 실행 완료

```js
// 즉시 실행 함수는 Person을 반환하고 실행 완료
// 실행 컨텍스트는 스택에서 제거됨
// 하지만 렉시컬 환경은 sayHi가 참조하고 있어서 살아남음
```

정리하면,<br />
"자신의 상위 스코프인 즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경의 참조를 <br />[[Envrionment]]에 저장하여 기억한다"는 말은 "sayHi 함수는 자신이 정의된 곳(즉시 실행 함수 내부)의 환경 주소를 내부 슬롯에 저장해서, 나중에 호출될 때마다 그 환경에 다시 접근할 수 있다", 또한 "sayHi는 태어난 곳의 주소를 평생 기억해서, 그곳의 \_age 변수를 언제든 사용할 수 있다"는 의미이다.

⎯

따라서 Person 생성자 함수의 모든 인스턴스가 상속을 통해 호출할 수 있는<br />
Person.prototype.sayHi 메서드의 상위 스코프는<br />
어떤 인스턴스로 호출하더라도 하나의 동일한 상위 스코프를 사용하게 된다.<br />
이러한 이유로 Person 생성자 함수가 여러 개의 인스턴스를 생성할 경우<br />
위와 같이 `_age` 변수의 상태가 유지되지 않는다.

이처럼 과거에는 JavaScript는 정보 은닉을 완전하게 지원하지 않았다. 인스턴스 메서드를 사용한다면 자유 변수를 통해 private을 흉내낼 수는 있지만 프로토타입 메서드를 사용하면 이마저도 불가능했다. ES6의 Symbol 또는 WeakMap을 사용하여 private한 프로퍼티를 흉내내기도 했으나 근본적인 해결책이 되지는 않는다.

책에 따르면, 책 출단 당시(2021년)에는 TC39 프로세스의 stage 3(candidate)에 클래스에 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있었다.

하지만 현재는 상황이 완전히 바뀌었다. ES2022(ES13)부터 클래스 private fields가 정식 표준으로 승급되어 모든 주요 브라우저와 Node.js에서 완전히 지원되고 있다. 이제 `#` 문법을 사용하여 진정한 private 필드와 메서드를 만들 수 있다.

따라서 클로저를 사용한 정보 은닉 기법은 JavaScript의 근본 원리를 이해하는 데 여전히 중요한 개념이지만, 현대 JavaScript에서는 클래스 private fields를 사용하는 것이 더 깔끔하고 표준적인 방법이 되었다.

---

### 24.6 자주 발생하는 실수

클로저를 사용할 때 자주 발생할 수 있는 실수를 보자.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  }; // ①
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // ②
}
```

첫 번째 for 문의 코드 블록 내에서<br />
함수가 funcs 배열의 요소로 추가된다.

그리고 두 번째 for 문의 코드 블록 내에서<br />
funcs 배열의 요소로 추가된 함수를 순차적으로 호출한다.

이때 funcs 배열의 요소로 추가한 3개의 함수가 0, 1, 2를 반환할 것으로 기대했다면
아쉽게도 결과는 그렇지 않다.

for 문의 변수 선언문에서 var 키워드로 선언한 i 변수는<br />
블록 레벨 스코프가 아닌 함수 레벨 스코프를 갖기 때문에 전역 변수다. 🤯<br />
전역 변수 i에는 0, 1, 2가 순차적으로 할당된다.<br />
따라서 funcs 배열의 요소로 추가한 함수를 호출하면<br />
전역 변수 i를 참조하여 i의 값 3이 출력된다.

클로저를 사용해 위 예제를 바르게 동작하는 코드로 만들어보자.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    // ①
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

①에서 즉시 실행 함수는<br />
전역 변수 i에 할당되어 있는 값을 인수로 전달받아 매개변수 id에 할당한 후<br />
중첩 함수를 반환하고 종료된다.<br />
즉시 실행 함수가 반환한 함수는 funcs 배열에 순차적으로 저장된다.

이때 즉시 실행 함수의 매개변수 id는<br />
즉시 실행 함수가 반환한 중첩 함수의 상위 스코프에 존재한다.<br />
즉시 실행 함수가 반환한 중첩 함수는<br />
자신의 상위 스코프(즉시 실행 함수의 렉시컬 환경)를 기억하는 클로저이고,<br />
매개변수 id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어<br />
그 값이 유지된다.

위 예제는 JavaScript의 함수 레벨 스코프 특성으로 인해 for 문의 변수 선언문에서 var 키워드로 선언한 변수가 전역 변수가 되기 때문에 발생하는 현상이다. ES6의 let 키워드를 사용하면 이 같은 번거로움이 해결된다.

![](https://velog.velcdn.com/images/growing-hyo/post/cf9c5ba7-433f-4b08-8a3a-be4afe934d1d/image.png)

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
}
```

for 문의 변수 선언문에서 let 키워드로 선언한 변수를 사용하면<br />
for 문의 코드 블록이 반복 실행될 때마다<br />
for 문 코드 블록의 새로운 렉시컬 환경이 생성된다.

만약 for 문의 코드 블록 내에서 정의한 함수가 있다면<br />
이 함수의 상위 스코프는 for 문의 코드 블록이 생성한 렉시컬 환경이다.

이때 함수의 상위 스코프는<br />
for 문의 코드 블록이 반복 실행될 때마다 식별자(for 문의 변수 선언문에서 선언한 초기화 변수 및 for 문의 코드 블록 내에서 선언한 지역 변수 등)의 값을 유지해야 한다.

이를 위해 for 문이 반복될 때마다<br />
독립적인 렉시컬 환경을 생성하여 식별자의 값을 유지한다.

let이나 const 키워드를 사용하는 반복문(for 문, for...in 문, for...of 문, while 문 등)은<br />
코드 블록을 반복 실행할 때마다 새로운 렉시컬 환경을 생성하여<br />
반복할 당시의 상태를 마치 스냅샷을 찍는 것처럼 저장한다.

단, 이는 반복문의 코드 블록 내부에서 함수를 정의할 때 의미가 있다.<br />
반복문의 코드 블록 내부에 함수 정의가 없는 반복문이 생성하는 새로운 렉시컬 환경은<br />
반복 직후, 아무도 참조하지 않기 때문에 가비지 컬렉션의 대상이 된다.

또 다른 방법으로는 함수형 프로그램이 기법인 고차 함수를 사용하는 방법이 있다.<br />
이 방법은 변수와 반복문의 사용을 억제할 수 있기 때문에 오류를 줄이고 가독성을 좋게 만든다.

다음 예제에는 아직 살펴보지 않은 내용이 포함되어 있으므로 또 다른 방법이 있다는 것만 참고로 알아두자.

```js
// 요소가 3개인 배열을 생성하고 배열의 인덱스를 반환하는 함수를 요소로 추가한다.
// 배열의 요소로 추가된 함수들은 모두 클로저다.
const funcs = Array.from(new Array(3), (_, i) => () => i); // (3) [f, f, f]

// 배열의 요소로 추가된 함수들을 순차적으로 호출한다.
funcs.forEach(f => console.log(f())); // 0 1 2
```

---

정리하자면 클로저는

- 함수와 그 함수가 선언된 렉시컬 환경의 조합이고,
- 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다.
- 이러한 중첩 함수를 클로저라고 부르며, 상태를 안전하게 변경하고 유지하기 위해 사용한다.
- 상태가 의도치 않게 변경되지 않도록 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여 프로그램의 안정성을 높이는 중요한 개념이다.
