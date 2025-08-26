## this

### this 키워드

- 객체의 메서드는 자신이 속한 객체의 프로퍼티를 참조하고 변경할 수 있어야 함. 이를 위해 메서드가 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 하는데, 이때 필요한 것이 this임. this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 **자기 참조 변수(self-referencing variable)**임. this를 통해 프로퍼티나 메서드를 참조할 수 있음.
- this의 중요한 특징은, 함수가 호출되는 방식에 따라 this에 바인딩될 값, 즉 this 바인딩이 동적으로 결정된다는 점임. 이는 this의 값이 함수 정의 시점이 아닌 함수 호출 시점에 결정된다는 것을 의미함.

### 함수 호출 방식과 this 바인딩

this 바인딩은 함수를 호출하는 방식에 따라 결정됨. 주요 호출 방식과 그에 따른 this 바인딩은 다음과 같음.

1. 일반 함수 호출
   기본적으로 this에는 **전역 객체(global object)**가 바인딩됨. 브라우저 환경에서는 window 객체임. 전역 함수, 중첩 함수, 콜백 함수 등 어떠한 함수라도 일반 함수로 호출되면 내부의 this는 전역 객체를 가리킴.이는 메서드 내부의 중첩 함수나 콜백 함수의 this가 외부 메서드의 this와 달라지는 문제를 일으키는 원인이 됨.

   ````js const obj = {
   value: 100,
   foo() {
    console.log("foo's this:", this); // { value: 100, foo: f }

    // 콜백 함수 내부의 this는 foo의 this와 다름.
    setTimeout(function() {
      console.log("callback's this:", this); // window
      console.log("callback's this.value:", this.value); // undefined 또는 전역 변수 value
    }, 100);
   }
   };```

   단, **엄격 모드(strict mode)**에서 일반 함수 내부의 this는 undefined가 바인딩됨. 일반 함수에서 this는 의미가 없기 때문임. 이러한 문제를 해결하기 위해 this를 다른 변수에 할당하거나(const that = this;), Function.prototype.bind 메서드를 사용하거나, 화살표 함수를 사용할 수 있음.

   ````

2. 메서드 호출
   메서드 내부의 this에는 메서드를 호출한 객체, 즉 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩됨. 중요한 점은 메서드를 소유한 객체가 아닌, 메서드를 호출한 객체에 바인딩된다는 것임. 따라서 어떤 함수를 다른 객체의 메서드로 할당하여 호출하면, 그 함수의 this는 해당 객체를 가리키게 됨.프로토타입 메서드 내부의 this 또한 해당 메서드를 호출한 인스턴스에 바인딩됨.

3. 생성자 함수 호출
   생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩됨. 생성자 함수는 new 연산자와 함께 호출될 때 생성자 함수로 동작함. 만약 new 연산자 없이 생성자 함수를 호출하면 일반 함수로 호출된 것이므로, this는 전역 객체를 가리키게 되어 문제가 발생할 수 있음.

4. apply / call / bind 메서드에 의한 간접 호출

- **Function.prototype**의 메서드인 apply, call, bind는 this를 명시적으로 지정하면서 함수를 호출하거나, this가 고정된 새로운 함수를 반환함.
- apply와 call: 두 메서드는 함수를 호출하면서 첫 번째 인수로 전달한 객체를 해당 함수의 this에 바인딩함. 인수를 전달하는 방식에만 차이가 있음(apply는 배열, call은 쉼표로 구분된 목록).
- bind: 함수를 호출하지 않고, 첫 번째 인수로 전달한 객체가 this에 바인딩된 새로운 함수를 생성하여 반환함. 메서드 내부의 콜백 함수 등에서 this가 불일치하는 문제를 해결하기 위해 유용하게 사용됨.

```js const person = {
  name: '형민',
  sayName() {
    console.log(this.name);
  }
};

// setTimeout의 콜백으로 그냥 넘기면 this는 window가 됨.
setTimeout(person.sayName, 100); // undefined 또는 ""

// bind를 사용해 this를 person으로 고정한 새 함수를 넘기는 경우.
setTimeout(person.sayName.bind(person), 100); // "형민"
```
