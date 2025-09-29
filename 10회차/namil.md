## 31.1 정규 표현식이란?

---

=> Regular Expression. 줄여서 RegExp라고 많이 쓰는 것 같다.

- 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 형식(formal) 언어
- 자바스크립트만의 특권이 아니라 거의 대부분의 프로그래밍 언어와 코드 에디터에 내장되어 있는 상당히 common한(like markdown)언어입니다.

> 특징 : **문자열**을 대상으로 패턴 매칭 기능을 제공한다.
>
> 💡 패턴 매칭 기능 : 특정 패턴과 일치하는 문자열을 **검색**하거나 **추출** 또는 **치환**할 수 있는 기능

예를 들어... 유효한 전화번호인지 체크할 때

전화번호는 `nnn - nnnn - nnnn` (n은 숫자)이라는 패턴이 존재한다.
이 패턴을 아래처럼 정규 표현식으로 정의하고 전화번호 패턴에 맞는지 확인할 수 있다.

```js
// 사용자로부터 입력받은 휴대폰 번호
const phoneNumber = "010-1234-9876";

// 정규 표현식 리터럴로 휴대폰 전화번호 패턴을 정의
const regExp = /^\d{3}-\d{4}-\d{4}$/;

// phoneNumber와 정규 표현식 패턴이 매칭하는가?
regExp.test(phoneNumber); // -> true
```

> 정규 표현식이 없었다면 일일이 조건/반복문으로 맨 앞의 0부터 마지막 6까지 숫자가 맞는지 확인했을 것이다. 상당히 끔찍하다.
>
> 코드는 줄이면 줄일 수록 좋다. 특히 조건문과 반복문은 더. 다만 가독성도 끔찍할 정도로 안좋다.

## 31.2 정규 표현식의 생성

---

RegExp(정규 표현식)객체를 생성하기 위해 "정규 표현식 리터럴"과 RegExp 생성자 함수를 사용할 수 있다고 한다.

일반적으로 정규 표현식 리터럴을 사용한다.

![정규 표현식 리터럴](https://velog.velcdn.com/images/smang66/post/96de09d5-321e-483e-ab16-c928e7485026/image.png)

시작, 종료기호 안에 작성하는 **패턴**과 종료기호 다음에 오는 **플래그**가 있다.

---

- 패턴 : 우리가 찾고자 하는 **패턴**.

> 💡 `/abc/` 라는 패턴이 있다면 "문자열 안에서 `abc`를 찾아라"라는 뜻.

- 플래그 : 어떤 옵션을 통해서 검색을 할 것인가?

> 💡 옵션의 종류는 총 3가지이다.
>
> `g` (global): 전체 검색. 패턴에 맞는 걸 하나만 찾는 게 아니라, 문자열 전체에서 다 찾는다.
> `i` (ignore case): 대소문자의 구분을 무시. A나 a나 똑같이 취급한다.
> `m` (multiline): 여러 줄 검색 모드. ^(줄 시작), $(줄 끝) 같은 기호가 여러 줄에 각각 적용된다.

```js
const songName = "Is this really love?";

// 패턴: is
// 플래그: i => 대소문자를 구별하지 않고 검색한다.
const regexp = /is/i;

const regexp = /is/g; // 전체 검색

const regexp = /is/m; // 여러줄 검색

// test 메서드는 songName 문자열에 대해 정규표현식 regexp의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.
regexp.test(songName); // -> true
```

> 만약 RegExp 생성자 함수를 사용한다면...

```js
new RegExp(pattern[, flags])

const songName = 'Is this really love?';

const regexp = new RegExp(/is/i); // ES6
// const regexp = new RegExp(/is/, 'i');
// const regexp = new RegExp('is', 'i');

regexp.test(songName); // -> true
```

## 31.3 RegExp 메서드

---

### 31.3.1 RegExp.prototype.exec

- 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 배열로 반환한다. 매칭 결과가 없는 경우 `null`을 반환한다.

```js
const target = "Is this really love?";
const regExp = /is/;

target.match(regExp); // -> ["is", index: 5, input: "Is this really love?", groups: undefined]
```

`input` : 원본 문자열 = Is this really love?

`groups` : 그룹화의 유무

> `exec` 메서드는 문자열 내 모든 패턴을 검색하는 `g` 플래그를 지정해도 첫 번째 매칭 결과만 반환하므로 주의.

### 31.3.2 RegExp.prototype.test

인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 `boolean` 값으로 반환한다.

```js
const target = "Is this really love?";
const regExp = /is/;

regExp.test(target); // -> true
```

### 31.3.3 String.prototype.match

`String` 표준 빌트인 객체가 제공하는 `match` 메서드는 대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환한다.

```js
const target = "Is this really love?";
const regExp = /is/;

target.match(regExp); // -> ["is", index: 5, input: "Is this really love?", groups: undefined]
```

위의 `exec` 메서드는 문자열 내의 모든 패턴을 검색하는 `g` 플래그를 지정해도 첫 번째 매칭 결과만 반화한다. 하지만 `String.prototype.match` 메서드는 `g` 플래그가 지정되면 모든 결과를 배열로 반환한다.

```js
const target = "Is this really love?";
const regExp = /is/g;

target.match(regExp); // -> ["is", "is"]
// this 의 is
// 문장 끝의 is
```

## 31.4 플래그

---

앞서 6개의 플래그 중 3개의 플래그를 살펴보았다. 플래그는 옵션이기 때문에 강제는 아니다. 필터링을 한다고 생각하면 된다.

```js
const target = "Is this really love?";

// target 문자열에서 is 문자열을 대소문자를 구별하여 한 번만 검색한다.
target.match(/is/);
// -> ["is", index: 5, input: "Is this really love?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 한 번만 검색한다.
target.match(/is/i);
// -> ["Is", index: 0, input: "Is this really love?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하여 전역 검색한다.
target.match(/is/g);
// -> ["is", "is"]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 전역 검색한다.
target.match(/is/gi);
// -> ["Is", "is", "is"]
```

## 31.5 패턴

### 31.5.1 문자열 검색

```js
const str = "Hello World";
console.log(/World/.test(str)); // true
console.log(/world/.test(str)); // false (대소문자 구분)
```

> 특정 문자열을 찾을 때 사용하는 방법.

### 31.5.2 임의의 문자열 검색

```js
const str = "cat bat mat";
console.log(/.at/g.test(str)); // true
console.log(str.match(/.at/g)); // ["cat", "bat", "mat"]
```

> .은 “임의의 문자 하나”를 의미하기에 "`.at`"는 a 앞에 뭐가 오든 `.at` 구조를 찾음.

### 31.5.3 반복 검색

```js
const itsMe = "namilcccc";
console.log(/c+/.test(itsMe)); // true (c가 1개 이상 반복)
console.log(itsMe.match(/c+/));
// [ 'cccc', index: 5, input: 'namilcccc', groups: undefined ]
```

> +, \*, {n,m} 같은 기호로 반복 횟수를 제어 가능. (n, m은 숫자)

### 31.5.4 OR 검색

```js
const str = "I like cats";
console.log(/cat|dog/.test(str)); // true ("cat" 또는 "dog")
```

> `|` 기호로 OR 조건을 만들기 가능.

### 31.5.5 NOT 검색

```js
const str = "12345";
console.log(str.match(/[^0-9]/)); // null (숫자가 아닌 건 없음)
console.log("abc".match(/[^0-9]/)); // ["a"]
```

> [^ ] 대괄호 안에서 ^는 “부정(not)” 의미. 숫자가 아닌 걸 찾는 패턴이다.

### 31.5.6. 시작 위치로 검색

```js
const str = "JavaScript";
console.log(/^Java/.test(str)); // true (문자열 시작이 "Java")
```

> ^는 문자열의 시작 위치를 의미한다.

### 31.5.7 마지막 위치로 검색

```js
const str = "index.html";
console.log(/html$/.test(str)); // true (문자열 끝이 "html")
```

> $는 문자열 끝 위치 의미.

익숙해지면 좋긴 하겠지만 가독성이 진짜 하..
