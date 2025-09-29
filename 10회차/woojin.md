# JS DeepDive

## 31.RegEXP

### 정규 표현식이란?

정규 표현식은 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 **형식 언어**이다.

- **패턴 매치 기능**
  정규 표현식이 제공하는 기능으로, 특정 패턴과 일치하는 문자열을 검색하거나 추출 or 치환할 수 있는 기능

```jsx
// 사용자로부터 입력받은 휴대폰 전화번호
const tell = `010-1234-657팔`;

// 정규 표현식 리터럴로 휴대폰 전화번호 패턴을 정의
const regExp = /^\d{3}-\d{4}-\d{4}$/;

// tell이 휴대폰 전화번호 패턴에 매칭하는지 테스트
regExp.test(tell); // false
```

> - 정규 표현식을 사용하지 않으면 반복문과 조건문을 사용해 한 문자씩 연속적으로 체크해야됨
> - 정규 표현식을 통해 패턴을 정의해 간단히 테스트 가능
> - 가독성이 떨어진다는 단점 존재(주석, 공백 허용❌)

### 정규 표현식의 생성

정규 표현식 객체(regExp 객체)를 생성하기 위해서는 정규 표현식 리터럴과 RefExp 생성자 함수를 사용

**정규 표현식 리터럴**(일반적인 방법)

- 정규 표현식 리터럴은 **패턴**과 **플래그**로 구성된다.

![](https://velog.velcdn.com/images/wjinss/post/daf38f34-dc04-4c50-a9a9-6e3937f3c0bb/image.png)
**예시**

```jsx
const testText = "This is test";

// 패턴: is
// 플래그: i => 대소문자를 구별하지 않고 검색
const regexp = /is/i;

// test메서드는 testText 문자열에 대한 정규 표현식 regexp의 패턴을 검색해 매칭 결과를 논리형으로 반환
console.log(regexp.test(testText));
// true
```

해당 객체를 `RegExp 생성자 함수`를 사용해 RegExp 객체를 생성하면 다음과 같다.
**JSDoc**

```jsx
/**
 * pattern: 정규 표현식의 패턴
 * flags: 정규 표현식의 플래그(g, i, m, u, y)
 */
new RegExp(pattern[, flags])
```

**예시**

```jsx
const testText = "This is test";

const regexp = new RegExp(/is/i);
// const regexp = new RegExp(/is/, 'i')
// const regexp = new RegExp('is', 'i')

regexp.test(testText);
```

### RegExp 메서드

**RegExp.prototype.exec**

- 인수로 전달받은 문자열에 대해 패턴 검색 후 매칭 결과를 **배열로 반환**
- 매칠 결과가 없으면 `null`을 반환
- 문자열 내의 모든 패턴을 검색하는 `g 플래그`를 지정해도 첫 번째 검색 결과만 반환함
  - **같은 문자열이 있어도 첫 번째 일치 결과만 반환**

**예시**

```jsx
const testText = "This is test";
const regexp = /is/;

regexp.exec(testText);
// ['is', index: 2, input: 'This is test', groups: undefined]
```

**RegExp.prototype.test**

- 인수로 전달받은 문자열에 대해 패턴 검색 후 매칭 결과를 **논리형 값으로 반환**

**예시**

```jsx
const testText = "This is test";
const regexp = /is/;

regexp.test(testText); // true
```

**String.prototype.match**

- **대상 문자열과 인수로 전달받은 졍규 표현식**과의 매칭 결과를 **배열로 반환**
- `g 플래그`를 지정하면 **모든 매칭 결과를 배열로 반환**

```jsx
const testText = "This is test";
const regexp = /is/;

testText.match(regexp);
// ['is', index: 2, input: 'This is test', groups: undefined]

const testText2 = "is this all there is?";
const regexpG = /is/g;
testText2.match(regexpG);
// ['is', 'is']
```

### 플래그

패턴과 함께 정규 표현식을 구성하며, 정규 표현식의 검색 방식을 설정하기 위해 사용된다.
총 6개가 있으며 중요한 3개의 플래그를 살펴본다.

| 플래그 | 의미        | 설명                                                           |
| ------ | ----------- | -------------------------------------------------------------- |
| i      | ignore case | 대소문자를 구별하지 않고 패턴을 검색                           |
| g      | Global      | 대산 문자열 내에서 패턴과 일치하는 모든 문자열을 전역에서 검색 |
| m      | Multi line  | 문자열의 행이 바뀌더라도 계속 패턴 검색                        |

- 옵션으로 선택적으로 사용 가능
- 순서와 상관 없이 하나 이상의 플래그를 동시 설정 가능
- 플래그를 사용하지 않을 경우, 대소문자를 구별해 패턴을 검색
- 문자열에 패턴 검색 매칭 대상이 1개 이상 존재해도 첫 번째 매칭한 대상만 검색하고 종료

**예시**

```jsx
const testText = "Is this all there is?";

// testText 문자열에서 is 문자열을 대소문자를 구별해 한 번만 검색
testText.match(/is/);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]

// testText 문자열에서 is 문자열을 대소문자를 구별하지 않고 한 번만 검색
testText.match(/is/i);
// ['Is', index: 0, input: 'Is this all there is?', groups: undefined]

// testText 문자열에서 is 문자열을 대소문자를 구별해 전역 검색
testText.match(/is/g);
//  ['is', 'is']

// testText 문자열에서 is 문자열을 대소문자를 구별하지 않고 전역 검색
testText.match(/is/gi);
// ['Is', 'is', 'is']
```

### 패턴

플래그와 함께 정규 표현식을 구성하며, 문자열의 일정한 규칙을 표현하기 위해 사용된다.

- `/`로 열고 닫으며 문자열의 따옴표는 생략
- 특별한 의미를 가지는 **메타문자** or 기호로 표현 가능
- 어떤 문자열 내에 패턴과 일치하는 문자열이 존재할때 **"정규 표현식과 매치한다"**라고 표현

#### 문자열 검색

- `RegExp`메서드를 사용해 검색 대상 문자열과 정규 표현식 매칭 결과를 구해 검색을 수행
- 검색 대상 문자열과 플래그를 생략한 정규 표현식의 매칭 결과는 **대소문자를 구분한 첫 번째 결과를 반환**

> 정규 표현식을 생성하는 것만으로는 검색 수행 ❌

- 플래그 `i` : 대소문자 구별 ❌
- 플래그 `ㅎ` : 패턴과 매치하는 모든 문자열을 **전역 검색**

**예시**

> 상단의 예시 코드 확인

- **임의의 문자열 검색**
  - `.`은 임의의 문자 한 개 의미
  - 내용 상관 ❌

```jsx
const testText = "Is this all there is?";

// 임의의 3자리 문자열을 대소문자를 구별해 전역 검사
const refExp = /.../g;

testText.match(refExp);
//  ['Is ', 'thi', 's a', 'll ', 'the', 're ', 'is?']
// 띄어쓰기도 문자로 포함
```

- **반복 검색**
  - `{m,n}`은 패턴이 **최소 m번, 최대 n번 반복**되는 문자열을 의미
  - `,` 뒤에 공백이 있으면 정상 작동 ❌
  - `{n}`은 패턴이 **n번 반복**되는 문자열을 의미 (`{n}` === `{n,n}`)
  - `{n,}`은 패턴이** 최소 n번 이상 반복**되는 문자열을 의미
  - `+`는 패턴이 최소 한번 이상 반복되는 문자열을 의미 (`+` === `{1,}`)
  - `?`는 패턴이 최대 한 번(0번 포함) 이상 반복되는 문자열 (`?` === `{0,1}`)

**예시**

```jsx
const testText = "A AA B BB Aa Bb AAA";

// A가 최소 1번, 최대 2번 반복되는 문자열을 전역 검색
const regExp = /A{1,2}/g;

testText.match(regExp);
// ['A', 'AA', 'A', 'AA', 'A']

// ------------------

// A가 2번 반복되는 문자열을 전역 검색
const regExp2 = /A{2}/g;

testText.match(regExp2);
// ['AA', 'AA']

// ------------------

// A가 최소 2번 이상 반복되는 문자열을 전역 검색
const regExp3 = /A{2,}/g;

testText.match(regExp3);
// ['AA', 'AAA']

// ------------------

// A가 최소 한 번 이상 반복되는 문자열을 전역 검색
const regExp4 = /A+/g;

testText.match(regExp4);
// ['A', 'AA', 'A', 'AAA']

// ------------------

const testText2 = "color colour";

// 'colo' 다음 'u'가 최대 한 번 이상 반복되고, 'r'이 이어지는 문자열을 검색
// 'u'가 있든 말든, 뒤에 'r'이 오는..
const regExp5 = /colou?r/g;

testText2.match(regExp5);
// ['color', 'colour']
```

- **OR 검색**
  - `|`는 **OR을 의미**
  - `+`로 분해되지 않는 단어 레벨을 검색
  - `[]` 내의 문자는 OR로 동작하며, 뒤에 `+`를 사용해 앞선 패턴을 한 번 이상 반복
  - `[]` 내의 `-`로 범위를 지정
  - `\d`는 **숫자를 의미** (`\d` === `[0,9]`)
  - `\D`는 **문자를 의미**
  - `\w`는 알파벳, 숫자, 언더스코어를 의미 (`\w` === `[A-Za-z0-9_]`)
  - `\W`는 알파벳, 숫자, 언더스코어가 아닌 문자를 의미

**예시**

```jsx
const testText = "A AA B BB Aa Bb AAA";
const testText2 = "AA BB Aa Bb 12 3,141592";
const testText3 = "Aa Bb 3,141592 _%$#";

// 'A' 또는 'B'를 전역 검색
const regExp = /A|B/g;

testText.match(regExp);
// ['A', 'A', 'A', 'B', 'B', 'B', 'A', 'B', 'A', 'A', 'A']

// ------------------

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색 전역 검색
const regExp2 = /A+|B+/g;
const regExp22 = /[AB]+/g;

testText.match(regExp2);
// ['A', 'AA', 'B', 'BB', 'A', 'B', 'AAA']
testText.match(regExp22);
// ['A', 'AA', 'B', 'BB', 'A', 'B', 'AAA']

// ------------------

// A ~ Z가 최소 한 번 이상 반복되는 문자열을 전역 검색
const regExp3 = /[A-Z]+/g;

testText.match(regExp3);
// ['A', 'AA', 'B', 'BB', 'A', 'B', 'AAA']

// ------------------

// A ~ Z, a ~ z가 최소 한 번 이상 반복되는 문자열을 전역 검색
const regExp4 = /[A-Za-z]+/g;

testText2.match(regExp4);
// ['AA', 'BB', 'Aa', 'Bb']

// ------------------

// 0 ~ 9가 한 번 이상 반복되는 문자열을 전역 검색
const regExp5 = /[0-9]+/g;
// 쉼표를 패턴에 포함
const regExp55 = /[0-9,]+/g;

testText2.match(regExp5);
// ['12', '3', '141592']
testText2.match(regExp55);
// ['12', '3,141592']

// ------------------

// 0 ~ 9 or ','가 한 번 이상 반복되는 문자열을 전역 검색
const regExp6 = /[\d,]+/g;

testText2.match(regExp6);
// ['12', '3,141592']

// ------------------

// 0 ~ 9가 아닌 문자 or ','가 한 번 이상 반복되는 문자열을 전역 검색
const regExp7 = /[\D,]+/g;

testText2.match(regExp7);
// ['AA BB Aa Bb ', ' ', ',']

// ------------------

// 알파벳, 숫자, 언더스코어, ','가 한 번 이상 반복되는 문자열 전역 검색
const regExp8 = /[\w,]+/g;

testText3.match(regExp8);
// ['Aa', 'Bb', '3,141592', '_']

// 알파벳, 숫자, 언더스코어가 아닌 문자 또는 ','가 한 번 이상 반복되는 문자열 전역 검색
const regExp9 = /[\W,]+/g;
testText3.match(regExp9);
// [' ', ' ', ',', ' ', '%$#']
```

- **NOT 검색**
  - `[...]` **내의** `^`는 **not을 의미**
    - `[^0-9]` === `[\D]`(숫자를 제외한 문자)
    - `[^A-Za-z0-9_]` === `[\W]` (알파벳, 숫자, 언더스코어가 아닌 문자를 의미)

```jsx
const testText = "AA BB 12 Aa Bb";

// 숫자를 제외한 문자열을 전역 검색
const regExp = /[^0-9]+/g;

testText.match(regExp); // ['AA BB ', ' Aa Bb']
```

- **시작 위치로 검색**
  - `[...]` **밖의** `^`는 **not을 의미**

```jsx
const testText = "https://velog.io/";

// 'https'로 시작하는지 검사
const regExp = /^https/g;

testText.test(regExp); // true
```

- **마지막 위치로 검색**
  - `$`는 문자열의 **마지막을 의미**

```jsx
const testText = "https://velog.io/";

// 'com'으로 끝나는지 검사
const regExp = /com$/g;

testText.test(regExp); // true
```

### 자주 사용하는 정규 표현식

- **특정 단어로 시작하는지 검사**

```jsx
const URL = "https://velog.io/"

// 'http://' or https://' 으로 시작하는지 검사
/^https?:\/\//.test(URL) // true

/^(http|https):\/\//.test(URL) // true
```

- **특정 단어로 끝나는지 검사**

```jsx
const URL = "https://velog.io/"

// 'io'로 끝나는지 검사
/io$/.test(URL) // true
```

- **숫자로만 이루어진 문자열인지 검사**
  - `[...]` 바깥의 `^`는 문자열의 시작
  - `$`는 문자열의 마지막
  - `\d`는 숫자
  - `+`는 앞선 패턴 반복

```jsx
const number = "1123213"

// 숫자로만 이루어진 문자열인지 검사
/^\d+$/.test(number) // true
```

- **하나 이상의 공백으로 시작하는지 검사**
  - `\s`는 여러 가지 공백 문자를 의미
  - `\s` === `[\t\r\n\v\f]`

```jsx
const text = "Hi!"

// 하나 이상의 공백으로 시작하는지 검사
/^[\s]/.test(text) // true
```

- **아이디로 사용 가능한지 검사**
  - 알파벳 대소문자 or 숫자로 시작하고 끝나며
  - 4~10자리인지

```jsx
const id = "wjinss12"

// 알파벳 대소문자 or 숫자로 시작하고 끝나며 4~10자리인지 검사
/^[A-Za-z0-9]{4,10}$/.test(id) // true
```

- **메일 주소 형식에 맞는지 검사**

```jsx
const email = "wjinss12@gmail.com"

/^[0-9A-Za-z]([-_\.]?[0-9A-Za-z])*@[0-9A-Za-z]([-_\.]?[0-9A-Za-z])*\.[a-zA-Z]{2,3}$/.test(email)
// true
```

> `<input type="email">` 씁시다..

- **핸드폰 번호 형식에 맞는지 검사**

```jsx
const phoneNumber = "010-1231-4472"

/^\d{3}-\d{3,4}-\d{4}$/.test(phoneNumber) // true
```

- **특수 문자 포함 여부 검사**
  - 특수문자는 `A-Za-z0-9` 이외의 문자

```jsx
const test = "abc!@123"(/[^A-Za-z0-9]/gi).test(test); // true
```

- **선택적 특수 문자 포함 여부 검사**

```jsx
const test = "abc!@123"(
  /[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=(\'\"]/gi
).test(test); // true
```

- **특수 문자 제거**
  - **String.prototype.replace**메서드를 사용

```jsx
const test = "abc!@123";

test.replace(/[^A-Za-z0-9]/gi, ""); // "abc123"
```
