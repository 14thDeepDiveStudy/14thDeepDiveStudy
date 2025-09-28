# 31장 정규 표현식 (RegExp)

## 31.1 정규 표현식이란?

정규 표현식(Regular Expression)을 한마디로 표현하면 **문자열의 패턴을 찾는 검색 도구**라고 할 수 있다.

### 주요 특징

일상생활에서 우리가 010으로 시작하는 11자리 숫자라고 말하면 휴대폰 번호를 떠올리는 것처럼 **정규 표현식도 문자열의 특정 패턴을 정의해서 원하는 문자열을 찾아내는 기능을 제공**한다.

가장 큰 장점은 복잡한 문자열 검증 로직을 간단하게 만들 수 있다는 점이다. 예를 들어, 사용자가 입력한 이메일 주소가 올바른 형식인지 확인하려면 원래라면 @ 기호가 있는지, .com으로 끝나는지 등등 여러 조건을 하나하나 체크하는 긴 코드를 작성해야 한다. **하지만 정규 표현식을 사용하면 한 줄로 끝낼 수 있다*!***

![](https://velog.velcdn.com/images/growing-hyo/post/36c196fc-c31f-4150-aff6-5a3f7e6b4869/image.png)

다만 이런 강력함에는 대가가 따른다. 정규 표현식은 특수문자들로 가득해서 처음 보는 사람에게는 마치 암호처럼 보일 수 있다. 그래서 팀에서 사용할 때는 주석을 달아서 이 부분은 이메일 형식을 체크하는 코드라고 설명을 붙여주는 것이 좋다.

### 사용 예시

```js
// 휴대폰 번호 패턴 검증
const tel = '010-1234-567팔';
const regExp = /^\d{3}-\d{4}-\d{4}$/;

regExp.test(tel); // false
```

---

## 31.2 정규 표현식의 생성

정규 표현식을 만드는 방법은 두 가지가 있다.

### 1. 정규 표현식 리터럴

정규 표현식 리터럴은 가장 일반적인 방법으로, 미리 패턴을 알고 있을 때 사용한다. 슬래시(/)로 감싼 형태로 작성하며, 기본 구조는 `/패턴/플래그` 형식이다.

여기서 **패턴(pattern)**은 **찾고자 하는 문자열의 규칙을 정의하는 부분**이고, **플래그(flag)**는 **검색 방식을 설정하는 _옵션_**이다. 플래그는 생략 가능하며, 여러 개를 조합하여 사용할 수도 있다.

```js
const target = 'Is this all there is?';

// 패턴: is
// 플래그: i (대소문자 구분 안 함)
const regexp = /is/i;

regexp.test(target); // true
```

위 예시에서 `is`가 패턴이고, `i`가 플래그이다. i 플래그는 대소문자를 구분하지 않겠다는 의미로, 'Is'와 'is' 모두 매칭된다.

### 2. RegExp 생성자 함수

**생성자 함수**는 **패턴을 동적으로 만들어야 할 때 유용**하다. 예를 들어, 사용자가 입력한 값에 따라 검색 패턴이 바뀌어야 하는 상황에서 사용한다.

```js
/**
* pattern: 정규 표현식의 패턴
* flags: 정규 표현식의 플래그(g, i, m, u, y)
*/

new RegExp(pattern[, flags])
```

```js
const target = 'Is this all there is?';

const regexp = new RegExp(/is/i); // ES6
// const regexp = new RegExp('is', 'i');
// const regexp = new RegExp(/is/, 'i');

regexp.test(target); // true
```

RegExp 생성자 함수를 사용하면 변수를 사용해 동적으로 RegExp 객체를 생성할 수 있다. 실제 개발에서는 아래 코드와 같이 동적으로 패턴을 만들어야 하는 경우가 많다.

```js
const count = (str, char) => (str.match(new RegExp(char, 'gi')) ?? []).length;

count('Is this all there is?', 'is'); // 3
count('Is this all there is?', 'xx'); // 0
```

이 함수는 특정 문자가 문자열에서 몇 번 나타나는지 세어주는데, 찾고자 하는 문자(`char`)를 동적으로 받아서 정규 표현식을 만들어 사용한다.

---

## 31.3 RegExp 메서드

정규 표현식을 만들었다면 이제 실제로 사용해야겠지요? 정규 표현식을 활용할 수 있는 주요 메서드 세 가지를 알아보자. 각각 다른 용도로 사용되니까 상황에 맞게 골라 쓰면 된다.

### 31.3.1 RegExp.prototype.exec

- 패턴을 만족하는 첫 번째 매칭 결과를 배열로 반환
- 매칭 결과가 없으면 `null` 반환

`exec`는 execute의 줄임말로, 패턴과 매칭되는 첫 번째 결과의 상세 정보를 알고 싶을 때 사용한다. 마치 탐정이 증거를 자세히 조사하는 것처럼 매칭된 결과가 어디서 발견되었는지까지 알려준다.

![](https://velog.velcdn.com/images/growing-hyo/post/412fb092-f78a-4c87-a7e6-1708fd98d823/image.png)

```js
const target = 'Is this all there is?';
const regExp = /is/;

regExp.exec(target);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]
```

결과를 보면 찾은 문자열(`'is'`), 위치(`index: 5`), 원본 문자열 등의 정보를 담은 배열을 반환한다. 만약 매칭되는 것이 없다면 `null`을 반환한다.

### 31.3.2 RegExp.prototype.test

- 패턴을 만족하는지 여부를 불리언값으로 반환

`test`는 가장 자주 사용하는 메서드이다. **단순히 패턴과 매칭되는지 안 되는지만 확인**하고 싶을 때 사용하며, `true` 또는 `false`만 반환한다.

```js
const target = 'Is this all there is?';
const regExp = /is/;

regExp.test(target); // true
```

폼 유효성 검사에서 특히 많이 사용된다.

### 31.3.3 String.prototype.match

- String 표준 빌트인 객체가 제공하는 메서드
- 대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환

`match`는 String 객체의 메서드로, **매칭된 결과들을 배열로 받고 싶을 때** 사용한다. `exec`와 비슷하지만 전역 검색(g 플래그)을 사용하면 모든 매칭 결과를 한 번에 가져올 수 있어서 편리하다.

```js
const target = 'Is this all there is?';
const regExp = /is/;

target.match(regExp);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]

// g 플래그 지정시 모든 매칭 결과를 배열로 반환
const regExpG = /is/g;
target.match(regExpG); // ['is', 'is']
```

특히 문자열에서 특정 패턴을 모두 찾아서 개수를 세거나 추출할 때 유용하다.

---

## 31.4 플래그

플래그는 정규 표현식의 **검색 옵션**이라고 생각하면 된다. 마치 검색 엔진에서 대소문자 구분, 전체 검색 같은 옵션을 설정하는 것처럼 **어떤 방식으로 패턴을 찾을지 결정해주는 역할**을 한다. 플래그는 총 6개가 있는데, 그 중 중요한 3개의 플래그를 살펴보자.

| 플래그 | 의미        | 설명                                                       |
| ------ | ----------- | ---------------------------------------------------------- |
| `i`    | Ignore case | 대소문자를 구별하지 않고 패턴 검색                         |
| `g`    | Global      | 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색 |
| `m`    | Multi line  | 문자열의 행이 바뀌더라도 패턴 검색을 계속함                |

실제 사용 예시를 통해 각 플래그의 차이점을 살펴보자.

```js
const target = 'Is this all there is?';

// 기본: 첫 번째로 매칭되는 'is'만 찾음 (대소문자 구분)
target.match(/is/);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]

// i 플래그: 대소문자 구분 없이 첫 번째 매칭만 찾음
target.match(/is/i);
// ['Is', index: 0, input: 'Is this all there is?', groups: undefined]

// g 플래그: 대소문자 구분하며 모든 'is'를 찾음
target.match(/is/g);
// ['is', 'is']

// i + g 플래그: 대소문자 구분 없이 모든 매칭을 찾음
target.match(/is/gi);
// ['Is', 'is', 'is']
```

실무에서는 `gi` 조합을 가장 많이 사용한다고 한다. 대부분의 경우 대소문자를 구분하지 않고 모든 결과를 찾고 싶어하기 때문이란다.

---

## 31.5 패턴

정규 표현식을 처음 접하면 복잡해 보이지만, 사실 간단한 규칙들의 조합일 뿐이다. 마치 레고 블록을 조합해서 원하는 모양을 만드는 것처럼 말이다.

정규 표현식은 패턴과 플래그라는 두 가지 주요 구성 요소로 이루어져 있다. 패턴은 '어떤 문자열을 찾을 것인가'를 정의하고, 플래그는 '어떤 방식으로 찾을 것인가'를 설정한다.

패턴을 작성할 때는 슬래시(/)로 감싸주고, 일반 문자열과 달리 따옴표는 사용하지 않는다. 만약 따옴표를 넣으면 그 따옴표도 검색 대상에 포함되기 때문이다. 또한 패턴 안에서는 특별한 의미를 가진 메타문자들을 사용하여 더 복잡한 규칙을 표현할 수 있다.

문자열에서 우리가 정의한 패턴과 일치하는 부분을 찾았을 때, 이를 '매치(match)되었다'고 표현한다.

이제 패턴을 표현하는 다양한 방법들을 차근차근 살펴보자.

### 31.5.1 문자열 검색

가장 기본적인 패턴 매칭이다. 마치 com(ctrl) + f로 문서에서 특정 단어를 찾는 것과 같다. 찾고 싶은 문자열을 그대로 패턴에 넣으면 된다.

```js
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴
const regExp = /is/;
regExp.test(target); // true

target.match(regExp);
// ['is', index: 5, input: 'Is this all there is?', groups: undefined]
```

여기서 주의할 점은 **정규 표현식은 기본적으로 대소문자를 구분**한다는 것이다. 그래서 'Is'가 아닌 'is'를 찾은 것이다.

### 31.5.2 임의의 문자열 검색

- `.`은 임의의 문자 한 개를 의미

이제 조금 더 유연한 검색을 해볼까. 점(`.`)은 **아무 문자나 한 글자**를 의미하는 특별한 기호다.

```js
const target = 'Is this all there is?';

// 임의의 3자리 문자열을 대소문자를 구별하여 전역 검색
const regExp = /.../g;

target.match(regExp); // ['Is ', 'thi', 's a', 'll ', 'the', 're ', 'is?']
```

결과를 보면 문자열이 3글자씩 잘려서 나온 것을 확인할 수 있다. 공백이나 특수문자도 모두 **한 글자**로 취급된다.

### 31.5.3 반복 검색

실제 개발에서는 '숫자가 3번 연속 나오는 패턴' 같은 것을 찾아야 할 때가 많은데, 이때 사용하는 것이 **반복 검색 패턴**이다.

#### 31.5.3.1 {m,n}

중괄호 안에 숫자를 넣어서 '최소 몇 번, 최대 몇 번까지 반복'인지 정확히 지정할 수 있다. 예를 들어 비밀번호가 8~12자리여야 한다면 이런 패턴을 사용한다.

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 1번, 최대 2번 반복되는 문자열을 전역 검색
const regExp = /A{1,2}/g;

target.match(regExp); // ['A', 'AA', 'A', 'AA', 'A']
```

#### 31.5.3.2 {n}

**정확히 n번 반복되는 패턴을 찾고 싶을 때** 사용한다. 주민등록번호 앞자리처럼 정확히 6자리 숫자를 찾을 때 유용하다.

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 정확히 2번 반복되는 문자열을 전역 검색
const regExp = /A{2}/g;

target.match(regExp); // ['AA', 'AA']
```

#### 31.5.3.3 {n,}

'**최소 n번 이상**'이라는 의미다. 상한선이 없어서 그 이상 몇 번이든 상관없다는 뜻이다.

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 2번 이상 반복되는 문자열을 전역 검색
const regExp = /A{2,}/g;

target.match(regExp); // ['AA', 'AAA']
```

#### 31.5.3.4 +

플러스 기호는 '**1번 이상**'을 의미하는 단축 표현이다. `{1,}`와 똑같다. 자주 사용되는 패턴이라 별도의 기호가 있다.

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 한 번 이상 반복되는 문자열을 전역 검색
const regExp = /A+/g;

target.match(regExp); // ['A', 'AA', 'A', 'AAA']
```

#### 31.5.3.5 ?

물음표는 '**0번 또는 1번**'을 의미한다. 즉, 있어도 되고 없어도 되는 패턴이다. 예를 들어 color/colour처럼 선택적 철자가 있을 때 유용하다.

```js
const target = 'color colour';

// 'colo' 다음 'u'가 있을 수도 없을 수도 있고, 그 다음에 'r'이 오는 패턴
const regExp = /colou?r/g;

target.match(regExp); // ['color', 'colour']
```

### 31.5.4 OR 검색

#### 31.5.4.1 |

```js
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'를 전역 검색
const regExp = /A|B/g;

target.match(regExp); // ['A', 'A', 'A', 'B', 'B', 'B', 'A', 'B']

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색
const regExp2 = /A+|B+/g;

target.match(regExp2); // ['A', 'AA', 'B', 'BB', 'A', 'B']
```

#### 31.5.4.2 []

- `[]` 내의 문자는 or로 동작

```js
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색
const regExp = /[AB]+/g;

target.match(regExp); // ['A', 'AA', 'B', 'BB', 'A', 'B']

// 범위 지정: 'A'~'Z'가 한 번 이상 반복되는 문자열을 전역 검색
const regExp2 = /[A-Z]+/g;

target.match(regExp2); // ['A', 'AA', 'B', 'BB', 'A', 'B']
```

#### 31.5.4.3 \d

- 숫자를 의미 (`[0-9]`와 같음)

```js
const target = 'AA BB 12,345';

// '0'~'9'가 한 번 이상 반복되는 문자열을 전역 검색
const regExp = /[\d,]+/g;

target.match(regExp); // ['12,345']

// \D는 \d와 반대로 동작 (숫자가 아닌 문자)
const regExp2 = /[\D,]+/g;

target.match(regExp2); // ['AA BB ', ',']
```

#### 31.5.4.4 \w

- 알파벳, 숫자, 언더스코어를 의미 (`[A-Za-z0-9_]`와 같음)

```js
const target = 'Aa Bb 12,345 _$%&';

// 알파벳, 숫자, 언더스코어가 한 번 이상 반복되는 문자열을 전역 검색
const regExp = /[\w,]+/g;

target.match(regExp); // ['Aa', 'Bb', '12,345', '_']

// \W는 \w와 반대로 동작
const regExp2 = /[\W,]+/g;

target.match(regExp2); // [' ', ' ', ',', ' ', '$%&']
```

### 31.5.5 NOT 검색

- `[...]` 내의 `^`은 not을 의미

```js
const target = 'AA BB 12 Aa Bb';

// 숫자를 제외한 문자열을 전역 검색
const regExp = /[^0-9]+/g;

target.match(regExp); // ['AA BB ', ' Aa Bb']
```

### 31.5.6 시작 위치로 검색

- `[...]` 밖의 `^`은 문자열의 시작을 의미

```js
const target = 'https://poiemaweb.com';

// 'https'로 시작하는지 검사
const regExp = /^https/;

regExp.test(target); // true
```

### 31.5.7 마지막 위치로 검색

- `$`는 문자열의 마지막을 의미

```js
const target = 'https://poiemaweb.com';

// 'com'으로 끝나는지 검사
const regExp = /com$/;

regExp.test(target); // true
```

---

## 31.6 자주 사용되는 정규표현식

실무에서 자주 마주치는 검증 패턴들이다. 이런 패턴들은 외우기보다는 필요할 때마다 찾아서 사용하는 것이 좋다.

### 31.6.1 특정 단어로 시작하는지 검사

웹사이트 URL이 안전한 https로 시작하는지 확인할 때 사용한다.

```js
const url = 'https://example.com';

// 'http://' 또는 'https://'로 시작하는지 검사
/^https?:\/\//.test(url); // true

// 더 간단한 방법
/^(http|https):\/\//.test(url); // true
```

### 31.6.2 특정 단어로 끝나는지 검사

파일 확장자를 체크할 때 많이 사용한다. 이미지 파일만 업로드 받고 싶을 때 등에 유용하다.

```js
const fileName = 'index.html';

// 'html'로 끝나는지 검사
/html$/.test(fileName); // true
```

### 31.6.3 숫자로만 이루어진 문자열인지 검사

사용자가 입력한 값이 순수하게 숫자로만 되어있는지 확인할 때 사용한다. 우편번호나 학번 등을 검증할 때 유용하다.

```js
const target = '12345';

// 숫자로만 이루어진 문자열인지 검사
/^\d+$/.test(target); // true
```

### 31.6.4 하나 이상의 공백으로 시작하는지 검사

문자열 앞에 불필요한 공백이 있는지 체크할 때 사용한다. 사용자 입력값을 정제할 때 유용하다.

```js
const target = ' Hi!';

// 하나 이상의 공백으로 시작하는지 검사
/^[\s]+/.test(target); // true
```

### 31.6.5 아이디로 사용 가능한지 검사

회원가입 시 아이디가 규칙에 맞는지 검사할 때 사용한다. 보통 영문과 숫자 조합, 특정 길이 제한이 있지요.

```js
const id = 'abc123';

// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4~10자리인지 검사
/^[A-Za-z0-9]{4,10}$/.test(id); // true
```

### 31.6.6 메일 주소 형식에 맞는지 검사

가장 자주 사용되는 검증 중 하나다. 완벽한 이메일 검증은 매우 복잡하지만, 기본적인 형태는 이 패턴으로 확인할 수 있다.

```js
const email = 'ungmo2@gmail.com';

/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/.test(
  email
); // true
```

### 31.6.7 핸드폰 번호 형식에 맞는지 검사

한국 휴대폰 번호 형식을 검증할 때 사용한다.

```js
const cellphone = '010-1234-5678';

/^\d{3}-\d{3,4}-\d{4}$/.test(cellphone); // true
```

### 31.6.8 특수 문자 포함 여부 검사

비밀번호에 특수문자가 포함되어 있는지 확인하거나, 반대로 특수문자를 제거할 때 사용한다.

```js
const target = 'abc#123';

// 특수 문자 포함 여부 검사
/[^A-Za-z0-9]/gi.test(target); // true

// 특수 문자 제거
target.replace(/[^A-Za-z0-9]/gi, ''); // 'abc123'
```

---

## 정리

정규 표현식은 복잡한 문자열 처리를 간결하게 표현할 수 있는 강력한 도구다. 하지만 가독성이 떨어질 수 있으므로 주석을 통해 의미를 명확히 하고, 너무 복잡한 패턴은 여러 개의 간단한 패턴으로 분리하는 것이 좋다.

## 핵심 포인트

1. 정규 표현식 리터럴과 RegExp 생성자 함수로 생성 가능
2. `exec`, `test`, `match` 메서드로 패턴 매칭 수행
3. `i`, `g`, `m` 플래그로 검색 방식 제어
4. 다양한 메타문자를 조합하여 복잡한 패턴 표현
5. 실무에서 자주 사용하는 검증 패턴들을 숙지
