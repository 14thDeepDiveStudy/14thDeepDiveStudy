# 🌟 5주차 - this

## 1. this 키워드

### this의 존재 이유

자바스크립트를 이루고 있는 수많은 객체들은 저마다 유용한 메서드(함수)를 가진다.

함수를 사용하기 위해선 반드시 호출을 해야 하는데,<br>
이 때 `func()` (= `window.func()`)와 같이 전역에서 호출하거나<br>
`obj.func()` 처럼 특정 객체에서도 호출할 수 있다.

이처럼 객체가 메서드로 접근해 호출하듯이,<br>
메서드 역시 자신을 호출하는 객체에게 접근해 뭔가를 해야 하기도 한다.

`Array.prototype` 에 구현된 `push` 메서드를 살펴 보자.<br>
`Array.prototype` 을 상속받는 배열에서 이 메서드를 쓰면 끝에 요소가 추가되어 배열 길이가 늘어난다.<br>
아래 코드는 `push` 메서드로 인해 `arr[4]` 에 ‘마’ 가 추가되었다.

```jsx
let arr = ['가', '나', '다', '라'];

arr.push('마');
console.log(arr); // ['가', '나', '다', '라', '마'];
```

앞서 다루었듯 배열은 Array 이전에 Object도 상속받는 객체이기 때문에,<br>
`arr[4]` 는 사실 `arr` 라는 객체에서 key가 4인 프로퍼티를 불러오는 것과 같다.

따라서, 아주 간단하게 생각하면 `push` 메서드는<br>
아래처럼 `arr` 의 프로퍼티에 접근해 값을 추가하도록 구현되어 있을 것이다.

```jsx
arr = {
  0: '가',
  1: '나',
  2: '다',
  3: '라',
  4: '마', // push 메서드로 추가된 프로퍼티
  length: 5, // 4 -> 5
};

// push 내부 구조?
function push(arg) {
  arr[arr.length] = arg;
  arr.length++;
}
```

하지만 `Array.prototype` 을 상속받는 수많은 배열 객체들이<br>
모두 위처럼 `arr` 라는 특정 배열에 프로퍼티를 추가하도록 구현되어 있진 않을 것이다.

개발자가 의도한 동작은 `a.push()` 를 호출하면 `a` 에 요소가 추가되고,<br>
`b.push()` 를 호출하면 `b` 에 요소가 추가되는 것이기 때문이다.

즉, 이렇게 ‘자신을 호출한 객체’라는 불특정 대상을 참조하도록 해주는 키워드가 `this` 이다.<br>
(생성자 함수에서 `this` 의 의미 = 생성할 인스턴스)

push 메서드를 다른 배열에서 호출했을 때도 대응 가능하게 나타내면 아래와 같다.

```jsx
function push(arg) {
  this[this.length] = arg; // 자신을 호출한 객체에 프로퍼티 추가

  // 길이 프로퍼티도 +1
  this.length++;
  return length;
}

foo.push(1); // foo[n] = 1
bar.push(2); // bar[m] = 2
```

### this 참조가 저장되는 위치와 저장 시점

this는 함수 내부에서 변수와 같이 동작하기 때문에 메모리에 할당되어야 값을 가질 수 있다.<br>
또한, this가 차지하는 공간은 각 스크립트의 실행 컨텍스트에 저장된다.

이 때문에 `this` 는 스크립트의 실행 컨텍스트가 생성되는 시점에 선언 및 할당된다.<br>
(값은 JS 엔진이 CallExpression을 보고 직접 넘겨줌)

이 때 즉시 실행되는 일반 코드들은 모두 전역 실행 컨텍스트를 참조하기 때문에<br>
`this` 엔 전역 객체 `window` 가 바인딩된다. (브라우저 기준, 통칭 `globalThis`)

```jsx
console.log(this); // window -> 전역 컨텍스트의 this

{
  console.log(this); // window
}
```

하지만 함수는 언제 어디서 실행될 지 모르는 코드블록이다.

비동기 자바스크립트에서 다루었듯,<br>
프로그램 시작 동시에 실행 컨텍스트를 가지게 되는 일반 코드와 다르게<br>
함수는 이례적으로 ‘호출 시점’에 실행 컨텍스트가 생성된다.<br>
(매개변수나 호출자, 호출 위치에 따라 매번 독립적인 환경을 가지기 때문이다.)

이 때문에 선언 시점엔 `thisBinding` 이 없는 상태이며<br>
참조에 연결되는 객체는 해당 함수의 호출 시점에 결정(바인딩)된다.

이후 콜 스택(=실행 컨텍스트 스택)에 푸시, 실행이 종료되면 스택에서 빠지며 삭제되는데<br>
이 때문에 컨텍스트 속 `this` 도 실행 종료와 동시에 삭제되어 매 호출마다 변화하게 되는 것이다.

```jsx
function normal() {
  console.log(this); // 이 시점엔 바인딩 X 그냥 문자 키워드일 뿐
}

normal(); // window -> 호출 시 바인딩됨
```

하지만 위처럼 전역 객체를 `this` 로 참조하는 것은 의미가 없다.<br>
`this` 를 안 쓰더라도 전역 변수는 어디서든 참조할 수 있기 때문.

하지만 `this` 로 `window` 에 직접적으로 프로퍼티를 추가하면 충돌이나 에러를 만들 수 있다.<br>
(let/const/var 키워드 안쓰고 프로퍼티 추가하는 것)

따라서 이를 방지하기 위해 **strict mode** 활성화 시<br>
함수 실행 컨텍스트 생성 시점에 객체 메서드가 아닌 일반 함수라면<br>
`thisBinding` 에 전역 객체 대신 `undefined` 를 할당한다.

```jsx
'use strict';

function normal() {
  console.log(this);
}

normal(); // undefined
```

## 2. 함수 호출 방식에 따른 this 바인딩

this는 함수가 호출될 때(=실행 컨텍스트가 생성될 때) 생성 및 바인딩된다고 했다.

- 이는 함수 내부에서 사용 가능한 스코프를 정의하는 렉시컬 스코핑과 대조되는 차이.
  함수 객체는 선언될 때 `[[Environment]]` 라는 내부 슬롯을 가지고 생성되는데,
  이 슬롯에 태어난 장소의 렉시컬 환경을 참조로 저장하고 변수 접근 시 이용하는 것이다.
  (렉시컬 환경의 Environment Record 와는 다른 개념이니 헷갈리지 말자.)

물론 함수 실행 컨텍스트는 호출 방법에 따라 계속 달라지므로 `this` 가 바인딩되는 규칙도 이에 따라 변화한다.

### A. 일반 함수로써 호출 시

`[[Call]]` 메서드가 구현된 (사실상 거의 모든) 함수는 `()` 를 붙여 일반 함수로 호출이 가능하다.

화살표 함수를 제외한 모든 함수는 **일반 함수로써 호출한 경우** strict 여부에 따라 `thisBinding` 이 달라진다.

- 일반 모드 → 전역 객체 (`window` , `global` 과 같은 `globalThis`)
- 엄격 모드(strict mode) → `undefined`

1. **선언문, 표현식으로 선언한 함수**

   - 일반 모드 → 전역 객체 바인딩

     ```jsx
     function foo() {
       console.dir(this);

       function bar() {
         console.dir(this);
       }
     }

     const baz = function () {
       console.dir(this);
     };

     foo(); // window
     bar(); // window
     baz(); // window
     ```

   - strict mode → `undefined` 바인딩

     ```jsx
     'use strict';

     function foo() {
       console.dir(this);

       function bar() {
         console.dir(this);
       }
     }

     const baz = function () {
       console.dir(this);
     };

     foo(); // undefined
     bar(); // undefined
     baz(); // undefined
     ```

2. **객체 내에 선언한 메서드**

   - 일반 모드 → 전역 객체 바인딩

     ```jsx
     const obj = {
       foo() {
         console.log('foo: ', this);

         function bar() {
           console.log('bar: ', this);
         }

         bar();
       },
     };

     obj.foo();
     // foo: window
     // bar: window
     ```

   - strict mode → `undefined` 바인딩

     ```jsx
     'use strict';

     const obj = {
       foo() {
         console.log('foo: ', this);

         function bar() {
           console.log('bar: ', this);
         }

         bar();
       },
     };

     obj.foo();
     // foo: undefined
     // bar: undefined
     ```

3. **콜백 함수**

   - 일반 모드 → 전역 객체 바인딩

     ```jsx
     const foo = setTimeout(function () {
       console.log(this);
     });

     foo(); // window
     ```

   - strict mode → `undefined` 바인딩

     ```jsx
     'use strict';

     const foo = setTimeout(function () {
       console.log(this);
     }, 100);

     foo(); // undefined
     ```

4. **화살표 함수**

   - 일반/strict mode → 모두 전역 객체 바인딩
     화살표 함수는 설계상 `thisBinding` 슬롯을 가지지 않는 함수이다.<br>
     따라서 strict mode 여부에 관계없이 일반 함수로 호출하면<br>
     전역 컨텍스트의 `this` 를 참조해 전역 객체가 바인딩된다.

     ```jsx
     'use strict';

     const foo = () => {
       console.log(this);
     };

     foo(); // window
     ```

### B. 객체 메서드로써 호출 시

1. **객체의 메서드**

   함수를 메서드로써 호출하면 `thisBinding` 은 호출한 객체(`.` 앞의 객체)가 된다.<br>
   이는 엔진이 표현 문맥을 보고 해당 객체의 참조값을 할당해 주는 것.

   아래 예시에서 객체 안에 정의된 메서드 `foo` 는 해당 객체 안에 종속된 것이 아니다.

   기본적으로는 프로토타입 체인 하위 객체들에서도 `foo` 를 호출 가능하며<br>
   메서드는 프로퍼티이자 값이기 때문에 변수에 담아 다른 메서드나 전역에서 호출할 수 있다.

   이 때마다 실행 컨텍스트가 재생성되므로 `this` 는 호출 주체에 바인딩된다.

   ```jsx
   const parents = {
     // 객체 메서드
     foo() {
       console.log(this);
     },
   };

   parents.foo(); // parents

   const children = {
     // 프로토타입 체인으로 상속
     __proto__: parents,
   };

   children.foo(); // children

   // 변수에 함수 값 저장
   const f = obj.foo;

   f(); // window
   ```

2. **메서드 안에 중첩된 함수/콜백**

   메서드 안에 함수가 중첩으로 선언되면,<br>
   해당 함수는 메서드에 귀속된 지역 함수이기 때문에 다른 객체에서 호출이 불가능하다.

   따라서 메서드가 중첩 함수 값을 `return` 하는 게 아니라면<br>
   메서드에 중첩된 함수는 일반 함수로써 호출할 수 밖에 없다.

   ```jsx
   var name = 'Ann';

   const parents = {
     name: 'James',
     foo() {
       function bar() {
         console.log('this: ', this);
         console.log('this.name: ', this.name);
       }

       bar();
     },
   };

   parents.foo().bar(); // Cannot read properties of undefined

   foo(); // bar(); 이러면 결국 일반 함수로 호출하는 것
   // this: window
   // this.name: Ann
   ```

   이는 여러 상황에서 문제가 되는데,<br>
   대표적으로 메서드 안에서 비동기 함수를 호출해야 하는 경우이다.

   아래와 같이 `this` 가 `parents` 에 바인딩될 것을 기대하고 코드를 짰는데<br>
   정작 `window` 혹은 `undefined` 에 바인딩되어 원하지 않는 값이 나올 수 있다.

   메서드 내부에서 본인의 `this` 를 잃어버리게 되는 것이다.

   ```jsx
   var name = 'Ann';

   const parents = {
     name: 'James',
     foo() {
       setTimeout(function () {
         console.log(`${this.name} is my dad.`);
       }, 100);
     },
   };

   parents.foo(); // Ann is my dad.
   ```

2-1. **`this` 바인딩 풀림 현상 해결법**

위와 같은 현상을 대비해 몇 가지 해결책이 있다.

1. `this` 를 변수에 담아 중첩 함수에 전달하기

   메서드에서 변수를 선언한 후 `this` 참조를 저장하는 방식이다.<br>
   이렇게 저장된 변수는 중첩 함수의 스코프이므로 변수를 통해 원래 `this` 에 접근할 수 있게 된다.<br>
   (변수를 통해 접근하는 방법이므로 중첩 함수의 `thisBinding` 자체가 바뀌는 건 아니다.)

   ```jsx
   var name = 'Ann';

   const parents = {
     name: 'James',
     foo() {
       const realThis = this; // realThis = parents

       setTimeout(function () {
         console.log(`${realThis.name} is my dad.`);
       }, 100);
     },
   };

   parents.foo(); // James is my dad.
   ```

2. 화살표 함수 사용

   앞서 살펴보았듯이 화살표 함수는 `thisBinding` 슬롯이 없어<br>
   `this` 접근 시 속해 있는 상위 컨텍스트의 `this` 를 참조한다.

   ```jsx
   var name = 'Ann';

   const parents = {
     name: 'James',
     foo() {
       setTimeout(() => {
         console.log(`${this.name} is my dad.`);
       }, 100);
     },
   };

   parents.foo(); // James is my dad.
   ```

3. `call` / `apply` / `bind` 메서드 사용

   함수의 `thisBinding` 자체를 명시적으로 바꿔 버리는 메서드들이다. 아래 섹션에 정리함.

### C. Function.prototype.call / apply / bind 메서드로 호출 시

`call` , `apply` , `bind` 는 모든 함수에 상속되는 내장 메서드로서<br>
`this` 를 명시적으로 전달, 고정하며 함수를 호출할 수 있다.

1. **Function.prototype.call**

   - `func` : `this` 를 고정하며 호출할 함수
   - `thisArg` : `this` 에 바인딩할 객체
   - `arg1, arg2, ...` : 매개변수가 있는 함수라면 이 값들이 순서대로 인자에 전달된다.

   ```jsx
   func.call(thisArg[, arg1, arg2, ...]);
   ```

   아래처럼 `parents` 의 메서드여도 `call(brother)` 로 호출하면<br>
   `thisBinding` 이 `brother` 가 되는 것을 볼 수 있다.

   ```jsx
   const parents = {
     name: 'James',
     foo() {
       console.log(`${this.name} is my dad.`);
     },
     bar() {
       console.log(`${this.name} is my mom.`);
     },
   };

   const brother = {
     name: 'Mike',
     foo() {
       console.log(`${this.name} is my brother.`);
     },
   };

   parents.bar.call(brother); // Mike is my mom.
   ```

2. **Function.prototype.apply**

   - `func` : `this` 를 고정하며 호출할 함수
   - `thisArg` : `this` 에 바인딩할 객체
   - `args` : 유사 배열 객체. 순서대로 인수에 전달된다.

   ```jsx
   func.apply(thisArg, args);
   ```

   `apply` 는 `call` 과 인수 전달 방식 외에는 같은 기능을 하지만<br>
   인수 갯수를 유동적으로 넣을 수 있어 `call` 대신 많이 쓰인다.

   ```jsx
   const crying = {
     one: '멍멍',
     two: '냥냥',
     three: '깡총',
   };

   const animals = ['고양이', '토끼', '강아지'];

   function howAnimalsCry(cat, rabbit, dog) {
     console.log(
       `${cat}는 ${this.one} ${rabbit}는 ${this.two} ${dog}는 ${this.three}`
     );
   }

   howAnimalsCry.apply(crying, animals);
   // 고양이는 멍멍 토끼는 냥냥 강아지는 깡총
   ```

3. **Function.prototype.bind**

   ```jsx
   const boundFunc = func.bind(thisArg, arg1, arg2...);
   ```

   `bind` 는 위의 두 메서드와 다르게 함수 호출을 하지 않고 `thisBinding` 이 고정된 새로운 함수를 반환한다.<br>
   이렇게 반환된 함수는 `this` 참조가 완전 고정되어, 호출자가 누구든간에 항상 `this` 는 같은 객체를 가리킨다.

   `bind` 가 호출 시 `func.apply(thisArg, [...args])` 로 자동 연결되는 기능을 추가해 반환했기 때문.

   내부 동작을 폴리필로 간단하게 구현하면 아래와 같다.

   ```jsx
   function bind(thisArg, ...boundArgs) {
     const func = this; // 호출자인 원본 함수 저장

     // func 래핑한 새로운 함수 선언
     function bound(...callArgs) {
       // new.target이 true(new로 호출) 시 this = 생성할 인스턴스
       // 이외 상황에서 호출 시 this = 전달받은 this
       const useThis = new.target ? this : thisArg;

       return func.apply(useThis, [...boundArgs, ...callArgs]);
     }

     // new 동작을 위해 원본 함수 프로토타입 복사
     if (func.prototype) {
       bound.prototype = Object.create(func.prototype, {
         constructor: { value: bound, writable: true, configurable: true },
       });
     }

     return bound; // 래핑한 함수 반환
   }
   ```
