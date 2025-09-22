## 클래스는 프로토타입의 문법적 설탕인가?

자바스크립트는 **프로토타입 기반(prototype-based)** 객체 지향 언어임. C++이나 Java처럼 클래스를 기반으로 하는 언어들과는 객체를 만드는 방식에 근본적인 차이가 있음.

ES5까지는 클래스 문법이 없어서, 생성자 함수와 프로토타입을 조합하여 객체 지향 프로그래밍의 상속 같은 개념을 흉내 냈음.

```javascript
// ES5 방식의 '클래스' 구현
var Pet = (function () {
  // 생성자 함수
  function Pet(name, age) {
    this.name = name;
    this.age = age;
  }

  // 프로토타입 메서드
  Pet.prototype.introduce = function () {
    console.log(
      "안녕! 내 이름은 " + this.name + "이고, " + this.age + "살이야."
    );
  };

  return Pet;
})();

var bboBbo = new Pet("뽀뽀", 8);
bboBbo.introduce(); // 안녕! 내 이름은 뽀뽀이고, 8살이야.
```

이런 방식은 클래스 기반 언어에 익숙한 개발자들에게는 다소 복잡하고 혼란스럽게 느껴졌음.

그래서 **ES6부터 `class` 키워드가 도입**되었음. 이는 자바스크립트의 객체 지향 모델을 프로토타입에서 클래스 기반으로 바꾼 것이 아님. 사실 클래스는 함수이며, 기존의 프로토타입 기반 패턴을 더 명료하고 깔끔하게 사용할 수 있도록 포장한 \*\*문법적 설탕(Syntactic Sugar)\*\*이라고 볼 수 있음.

하지만 클래스는 단순한 문법적 설탕을 넘어, 생성자 함수보다 더 엄격하고 유용한 기능들을 추가로 제공함.

### 클래스와 생성자 함수의 결정적 차이점

1.  **`new` 연산자 강제**
    클래스는 `new` 연산자 없이 호출하면 `TypeError`가 발생함. 반면 생성자 함수는 `new` 없이 호출하면 일반 함수로 동작함.

    ***

2.  **상속 지원 (`extends`, `super`)**
    클래스는 `extends`와 `super` 키워드를 기본적으로 제공하여 상속 관계를 매우 간결하게 구현할 수 있음. 생성자 함수는 이런 문법적 지원이 없음.

    ***

3.  **호이스팅 동작 방식**
    클래스는 `let`, `const` 키워드처럼 호이스팅됨. 선언문 이전에 참조하면 \*\*일시적 사각지대(TDZ)\*\*에 빠져 `ReferenceError`를 발생시킴. 마치 호이스팅이 일어나지 않는 것처럼 보임.

    ***

4.  **`strict mode` 자동 적용**
    클래스 몸체(`{...}`) 안의 모든 코드는 개발자가 별도로 설정하지 않아도 암묵적으로 \*\*엄격 모드(`strict mode`)\*\*로 실행됨. 해제할 수 없음.

    ***

5.  **메서드의 열거 불가**
    클래스의 `constructor`, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`임. 따라서 `for...in` 문이나 `Object.keys` 메서드로 열거되지 않음.

이러한 차이점들 때문에 클래스를 단순히 생성자 함수를 대체하는 문법적 설탕으로만 보기보다는, 더 견고하고 명료한 **새로운 객체 생성 메커니즘**으로 이해하는 것이 바람직함.

---

## 클래스 정의

클래스는 `class` 키워드를 사용하여 정의함. 클래스 이름은 생성자 함수처럼 파스칼 케이스(PascalCase)를 사용하는 것이 일반적인 관례임.

```javascript
// 클래스 선언문
class Pet {
  // 클래스 몸체에는 메서드만 정의할 수 있음
  // constructor, 프로토타입 메서드, 정적 메서드
}
```

함수와 마찬가지로 표현식으로도 클래스를 정의할 수 있음. 이는 클래스가 **일급 객체**라는 것을 의미함.

```javascript
// 익명 클래스 표현식
const Pet = class {};

// 기명 클래스 표현식
const Pet = class MyPet {};
```

클래스가 **일급 객체**라는 것은 다음과 같은 특징을 가짐을 의미함.

- 런타임에 생성이 가능함.
- 변수나 자료구조(객체, 배열 등)에 저장할 수 있음.
- 함수의 매개변수로 전달할 수 있음.
- 함수의 반환값으로 사용할 수 있음.

클래스 몸체에 정의할 수 있는 세 가지 메서드는 다음과 같음.

```javascript
class Pet {
  // 1. 생성자 (인스턴스 생성 및 초기화)
  constructor(name, age) {
    // 인스턴스 프로퍼티
    this.name = name;
    this.age = age;
  }

  // 2. 프로토타입 메서드 (인스턴스가 상속받아 사용)
  introduce() {
    console.log(`안녕! 내 이름은 ${this.name}이고, 나이는 ${this.age}살이야.`);
  }

  // 3. 정적 메서드 (클래스 자체에 바인딩된 메서드)
  static getFamily() {
    console.log("우리는 반려동물 가족이야!");
  }
}

// 인스턴스 생성
const bboBbo = new Pet("뽀뽀", 8);

// 인스턴스 프로퍼티 참조
console.log(bboBbo.name);

// 프로토타입 메서드 호출
bboBbo.introduce();

// 정적 메서드 호출
Pet.getFamily();
```

---

## 인스턴스 생성

클래스는 생성자 함수이며, `new` 연산자와 함께 호출되어 인스턴스를 생성함. `new` 연산자 없이 호출하는 것은 허용되지 않음.

```javascript
class Pet {}

const myPet = new Pet(); // 정상 동작
console.log(myPet); // Pet {}

// new 없이 호출 시 에러 발생
const failedPet = Pet(); // TypeError: Class constructor Pet cannot be invoked without 'new'
```

---

## 메서드

### `constructor` (생성자)

`constructor`는 인스턴스를 생성하고 초기화하기 위한 특별한 메서드임.

- 이름을 변경할 수 없으며, 클래스 내에 **최대 한 개만** 존재할 수 있음.
- 생략할 수 있으며, 생략하면 빈 `constructor`가 암묵적으로 정의됨.
- `constructor` 내부의 `this`는 클래스가 생성한 인스턴스를 가리킴.
- `constructor`는 별도의 `return` 문을 갖지 않는 것이 원칙임. 만약 객체를 명시적으로 반환하면 그 객체가 반환되고, 원시값을 반환하면 무시된 후 `this`가 반환됨.

<!-- end list -->

```javascript
class Pet {
  constructor(name, age) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩됨

    // 2. this에 바인딩된 인스턴스를 초기화함
    this.name = name;
    this.age = age;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환됨
  }
}
```

### 프로토타입 메서드

클래스 몸체에서 정의한 메서드는 기본적으로 **프로토타입 메서드**가 됨. 즉, 클래스의 `prototype` 프로퍼티에 추가됨.

생성된 인스턴스는 이 프로토타입 객체를 상속받으므로, 모든 인스턴스가 동일한 메서드를 공유하게 됨. 이는 메모리를 효율적으로 사용하는 방법임.

```javascript
class Pet {
  constructor(name) {
    this.name = name;
  }

  introduce() {
    // 이 메서드는 Pet.prototype.introduce에 위치함
    console.log(`내 이름은 ${this.name}이야.`);
  }
}

const bboBbo = new Pet("뽀뽀");
const yeolMae = new Pet("열매");

// bboBbo와 yeolMae는 동일한 introduce 메서드를 공유함
console.log(
  Object.getPrototypeOf(bboBbo).introduce ===
    Object.getPrototypeOf(yeolMae).introduce
); // true
```

### 정적 메서드

메서드 앞에 `static` 키워드를 붙이면 **정적 메서드**가 됨.

- 정적 메서드는 인스턴스가 아닌 **클래스 자체에 바인딩된 메서드**임.
- 따라서 인스턴스를 생성하지 않고 `클래스이름.메서드이름()` 형태로 호출함.
- 정적 메서드 내부의 `this`는 인스턴스가 아닌 클래스 자체를 가리킴.
- 인스턴스의 상태(프로퍼티)에 의존하지 않는 유틸리티성 함수를 만들 때 유용함.

<!-- end list -->

```javascript
class Pet {
  static getFamily() {
    console.log("우리는 반려동물 가족이야!");
  }
}

// 인스턴스 생성 없이 클래스 이름으로 바로 호출
Pet.getFamily();

const bboBbo = new Pet();
// bboBbo.getFamily(); // TypeError: bboBbo.getFamily is not a function
```

---

## 프로퍼티

### 인스턴스 프로퍼티

`constructor` 내부에서 `this`에 추가한 프로퍼티는 **인스턴스 프로퍼티**가 됨. 이 프로퍼티들은 기본적으로 `public`이므로 클래스 외부에서 자유롭게 접근하고 수정할 수 있음.

### 접근자 프로퍼티

`get`과 `set` 키워드를 사용해 정의하며, 프로퍼티의 값을 읽거나 쓸 때 특정 동작을 수행하도록 할 수 있음.

```javascript
class Pet {
  constructor(name) {
    this.name = name;
    this._age = 0; // 실제 나이 값은 _age에 저장 (private처럼 사용하려는 의도)
  }

  get age() {
    return `우리 ${this.name}는 ${this._age}살이에요!`;
  }

  set age(value) {
    if (value < 0) {
      console.log("나이는 음수일 수 없습니다!");
      return;
    }
    this._age = value;
  }
}

const bboBbo = new Pet("뽀뽀");
bboBbo.age = 8; // set age(value) 호출
console.log(bboBbo.age); // get age() 호출 -> "우리 뽀뽀는 8살이에요!"

bboBbo.age = -1; // "나이는 음수일 수 없습니다!"
```

### 클래스 필드와 private 필드

최신 자바스크립트에서는 클래스 몸체에 직접 프로퍼티를 정의하는 **클래스 필드** 문법을 지원함.

필드 이름 앞에 `#`을 붙이면 클래스 외부에서는 접근할 수 없는 **private 필드**가 됨. 이는 **정보 은닉**을 가능하게 함.

```javascript
class Pet {
  // public 클래스 필드
  species = "알 수 없음";

  // private 클래스 필드
  #secretName = "귀염둥이";

  constructor(name) {
    this.name = name;
  }

  getSecretName() {
    // 클래스 내부에서는 private 필드에 접근 가능
    return this.#secretName;
  }
}

const yeolMae = new Pet("열매");
console.log(yeolMae.species); // '알 수 없음'
// console.log(yeolMae.#secretName); // SyntaxError: Private field '#secretName' must be declared in an enclosing class
console.log(yeolMae.getSecretName()); // '귀염둥이'
```

---

## 상속에 의한 클래스 확장

### `extends` 키워드

`extends` 키워드는 한 클래스가 다른 클래스를 상속받아 그 기능을 확장할 때 사용함. 상속받는 클래스를 **서브클래스**, 상속되는 클래스를 **수퍼클래스**라고 함.

```javascript
// 수퍼클래스
class Pet {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  eat() {
    return `${this.name}가 밥을 먹습니다.`;
  }
}

// 서브클래스
class Dog extends Pet {
  play() {
    return "멍멍! 같이 놀아요!";
  }
}

class Cat extends Pet {
  groom() {
    return "그르릉... 그루밍 중...";
  }
}

const bboBbo = new Dog("뽀뽀", 8);
const yeolMae = new Cat("열매", "2개월");

console.log(bboBbo.eat()); // 수퍼클래스의 메서드 사용
console.log(bboBbo.play()); // 서브클래스 고유의 메서드 사용

console.log(yeolMae.eat()); // 수퍼클래스의 메서드 사용
console.log(yeolMae.groom()); // 서브클래스 고유의 메서드 사용
```

### `super` 키워드

`super` 키워드는 수퍼클래스를 참조할 때 사용하며, 두 가지 용법이 있음.

**1. `super()` 호출**

서브클래스의 `constructor`에서 호출하며, **수퍼클래스의 `constructor`를 실행**함.

- 서브클래스에 `constructor`를 작성할 경우, 반드시 `super()`를 가장 먼저 호출해야 함.
- 이를 통해 수퍼클래스로부터 인스턴스의 기본 구조(프로퍼티)를 물려받을 수 있음.

<!-- end list -->

```javascript
class Pet {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}

class Cat extends Pet {
  constructor(name, age, skill) {
    // super()를 통해 Pet 클래스의 constructor를 호출
    super(name, age);

    // 서브클래스 고유의 프로퍼티 초기화
    this.skill = skill;
  }
}

const yeolMae = new Cat("열매", "2개월", "꾹꾹이");
console.log(yeolMae); // Cat { name: '열매', age: '2개월', skill: '꾹꾹이' }
```

**2. `super` 참조**

메서드 내에서 `super.메서드이름()` 형태로 사용하여 **수퍼클래스의 메서드를 호출**할 수 있음. 주로 **메서드 오버라이딩** 시에 유용하게 사용됨.

\*\*메서드 오버라이딩(Method Overriding)\*\*이란, 수퍼클래스에 정의된 메서드를 서브클래스에서 동일한 이름으로 재정의하는 것을 말함.

```javascript
class Pet {
  introduce() {
    return `저는 반려동물입니다.`;
  }
}

class Dog extends Pet {
  introduce() {
    // super.introduce()로 부모의 메서드 결과를 가져온 후, 내용을 덧붙임
    const parentIntro = super.introduce();
    return `${parentIntro} 그중에서도 저는 강아지, 뽀뽀라고 해요!`;
  }
}

const bboBbo = new Dog();
console.log(bboBbo.introduce());
```
