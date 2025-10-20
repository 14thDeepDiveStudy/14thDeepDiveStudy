## 37.1 Ready, "Set", Go
---

- `Set` : 중복되지 않는 유일한 값들의 집합. 배열과 유사하지만 분명한 차이가 존재한다.

|   구분    |     배열     |      Set 객체       |
|---------------------------|-----------|---------------------|
|동일한 값을 중복하여 포함할 수 있는가?| O | X |
|요소 순서에 의미가 있는가?| O | X |
|인덱스에 요소로 접근할 수 있는가?| O | X |

어라? 자세히 보니 학창시절의 기억이 어렴풋이 떠오른다. 
그것은 바로..**"집합"** 그렇다. `Set`객체는 바로 수학적 집합을 구현하기 위한 자료구조인 것. 그렇기 때문에 교집합과 합집합 및 차집합, 여집합을 구할 수가 있다!

---

### 37.1.1 Set 객체 생성

`Set` 생성자 함수로 생성하며 인수를 전달하지 않으면 빈 객체가 생성된다.

```jsx
const set = new Set();
console.log = (set); // Set(0) {}
```

`Set` 생성자 함수는 **이터러블**을 인수로 전달받아 `Set` 객체를 생성하는데, 이때 **이터러블**의 중복된 값은 `Set` 객체에 요소로 저장되지 않는다.

예를 들어...

```jsx
const numberSet = new Set([1,2,3,3,4,4,5]);
console.log(numberSet); // Set(5) {1, 2, 3, 4, 5}

const stringSet = new Set('Hello, world!');
console.log(stringSet); // Set(10) {'H','e','l','o',',',' ','w','r','d','!'}
```

이렇게 중복된 요소를 제거할 수 있다.

---

### 37.1.2 요소 개수 확인

`Set.prototype.size` 프로퍼티를 사용해 `Set` 객체의 요소 개수를 확인할 수 있다.

```jsx
const { size } = new Set([1, 2, 3, 3. 4, 4, 5]);
console.log(size); // 5
```

> `size` 프로퍼티는 `setter` 없이 `getter` 함수만 존재하는 접근자 프로퍼티 이므로 `size` 프로퍼티에 숫자를 할당하여 요소 개수를 변경하는 행동은 불가능하다.
> ```jsx
> const set = new Set([1, 2, 3]);
set.size = 10;
console.log(set.size); // 10 , 3

---

### 37.1.3 요소 추가

`Set.prototype.add` 메서드를 사용해 요소를 추가할 수 있다.

```jsx
const set = new Set();
console.log(set); // Set(0) {}

set.add(1);
console.log(set); // Set(1) {1}
```
> `add` 메서드는 새로운 요소가 추가된 `Set` 객체를 반환하기 때문에 `add` 메서드를 호출한 후 또 `add` 메서드를 연속적으로 호출할 수 있다!
> ```jsx
> const set = new Set();
>
> set.add(1).add(2);
> console.log(set); // Set(2) {1, 2}

다만, 중복된 요소의 추가는 허용되지 않기 때문에 추가할 경우, 무시된다 (그렇다고 에러가 발생하지는 않음)

```jsx
const set = new Set();

set.add(1).add(2).add(2).add(2).add(2).add(2);
console.log(set); // Set(2) {1, 2}
```

일치 비교 연산자 ===을 사용할 경우 NaN과 NaN을 다르다고 평가한다.
하지만 `Set` 객체는 이를 같다고 평가하기 때문에 중복 추가가 허용되지 않는다. 마찬가지로 +0과 -0도 같다고 평가한다.

>**또한,** `Set` 객체는 객체나 배열과 같이 자바스크립트의 모든 값을 요소로 저장할 수 있다는 점을 기억하자.

```jsx
const set = net Set();

set
   .add(1) //  number
   .add('a') // string
   .add(true) // boolean
   .add(undefined)
   .add(null)
   .add({}) // 객체 
   .add([]) // 배열
   .add(() => {}); // 함수

console.log(set); // Set(8) {1, "a", true, undefined, null, {}, [], () => {}}
```
---

### 37.1.4 요소 존재 여부 확인

`Set.prototype.has` 메서드를 사용해 특정 요소의 존재 여부를 불리언 값으로 알 수 있다.

```jsx
const set = new Set([1, 2, 3, 4, 5]);

console.log(set.has(2)); // true
console.log(set.has(6)); // false
```

### 37.1.5 요소 삭제

`Set.prototype.delete` 메서드를 사용해 요소를 삭제할 수 있으며 이 때 `delete` 메서드는 삭제 성공의 여부를 불리언으로 나타낸다.

`Set` 객체는 배열과는 달리 순서의 의미가 없기 때문에 내가 삭제하려는 요소의 값을 인수로 전달하면 된다. 즉, `Set` 객체는 "**_인덱스를 갖지 않는다_** "는 뜻이다.

```jsx
const set = new Set([1, 2, 3]);

// 요소 2를 삭제하고 싶을 경우
set.delete(2);
console.log(set); // Set(2) {1, 3}

// 요소 3을 삭제하려면
set.delete(3);
console.log(set); // Set() {1}
```

애초에 없는 요소를 삭제할 경우 에러 대신 무시된다.

> 앞서 말했듯, `delete` 메서드는 불리언 값을 반환하기에 `add` 메서드와 달리 연속적으로 호출할 수 없다. TypeError를 보고 싶다면 시도해보라.

---

### 37.1.6 요소 일괄 삭제

`Set.prototype.clear` 메서드를 사용해 모든 요소를 일괄적으로 삭제할 수 있다.

`clear` 메서드는 항상 `undefined`를 반환한다.

```jsx
const set = new Set([1, 2, 3]);


set.clear();
console.log(set); // Set(0)
```

cli 명령어의 clear 느낌으로다가 싹 지워준다.

---

### 37.1.7 요소 순회

배열의 `forEach`와 같이 `Set` 객체도 `forEach` 메서드를 사용한다.
작동은 배열의 `forEach`와 비슷하지만 첫 번째 인수와 두 번째 인수가 약간 다르다. 

배열의 `forEach`는 두 번째 인수로 현재 순회중인 인덱스를 전달받지만, 위에서 말했듯이 `Set` 객체는 인덱스를 갖지 않아 첫 번째 인수와 두 번째 인수의 값이 같다. 

```jsx
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));

/*
1 1 Set(3) {1, 2, 3}
2 2 Set(3) {1, 2, 3}
3 3 Set(3) {1, 2, 3}
*/
```

> `Set` 객체는 이터러블이라서 `for..of` 문으로 순회할 수 있고, 스프레드 문법과 배열 디스트럭처링의 대상이 될 수 있다.

---

### 37.1.8 집합 연산

처음에 말했듯이 `Set` 객체는 집합을 표현하기 위한 자료구조이기 때문에 교집합, 합집합, 차집합, 여집합을 구현할 수 있기에 구현하자면 다음과 같다.

---

### 기본 집합 선언

```jsx
const setA = new Set([1, 2, 3, 4]);
const setB = new Set([3, 4, 5, 6]);
```

---

### 교집합

두 집합에 **공통**으로 포함된 원소만 남긴다.

```jsx
const intersection = new Set([...setA].filter(value => setB.has(value)));
console.log(intersection); // Set(2) {3, 4}

```

---

### 합집합

두 집합의 모든 원소를 합친 뒤 중복 제거한다.

```jsx
const union = new Set([...setA, ...setB]);
console.log(union); // Set(6) {1, 2, 3, 4, 5, 6}
```

---

### 차집합

첫 번째 집합에는 있고, 두 번째 집합에는 없는 원소를 구한다.

```jsx
const difference = new Set([...setA].filter(value => !setB.has(value)));
console.log(difference); // Set(2) {1, 2}
```

---

## 부분 집합과 상위 집합

### 1. 부분 집합

`A`의 모든 원소가 `B`에도 포함되어 있다면 → `A`는 `B`의 부분집합이다.

```jsx
const isSubset = [...setA].every(value => setB.has(value));
console.log(isSubset); // false
```

### 2. 상위 집합

`A`가 `B`의 모든 원소를 포함하면 → `A`는 `B`의 상위집합이다.


```jsx
const isSuperset = [...setB].every(value => setA.has(value));
console.log(isSuperset); // false
```

---

### 재사용 유틸 함수로 정리

=> 보다 깔끔하게 작성할 수 있다.

```jsx
const intersection = (a, b) => new Set([...a].filter(x => b.has(x)));
const union = (a, b) => new Set([...a, ...b]);
const difference = (a, b) => new Set([...a].filter(x => !b.has(x)));
const isSubset = (a, b) => [...a].every(x => b.has(x));
const isSuperset = (a, b) => [...b].every(x => a.has(x));

const A = new Set([1, 2, 3, 4]);
const B = new Set([3, 4, 5, 6]);

console.log(intersection(A, B)); // Set(2) {3, 4}
console.log(union(A, B));        // Set(6) {1, 2, 3, 4, 5, 6}
console.log(difference(A, B));   // Set(2) {1, 2}
console.log(isSubset(A, B));     // false
console.log(isSuperset(A, B));   // false
```

---

## 37.2 Map

키와 값의 **쌍**으로 이루어진 컬렉션이다. `Map` 객체는 객체와 유사하지만 다음과 같은 차이가 있다.

|   구분    |     객체     |      Map 객체       |
|---------------------------|-----------|---------------------|
|키로 사용할 수 있는 값| 문자 또는 심벌 값 | 객체를 포함한 모든 값 |
|이터러블| X | O |
|요소 개수 확인| Object.keys(obj).length | map.size |

---

### 37.2.1 Map 객체의 생성

기본 생성: `new Map()` → 빈 `Map`

초기화: 이터러블(보통 2원 배열의 배열)로 `[key, value]` 쌍 전달.

```js
const m1 = new Map();                   // 빈 Map
const m2 = new Map([['key1', 'v1'], ['key2', 'v2']]);
console.log(m2.get('key1'));            // 'v1'
```

중복 키가 있으면 나중 값으로 덮어씀.

`size`는 `getter-only` 이므로 `m.size = 10` 같은 대입은 무효.

---

### 37.2.2 요소 개수 확인

`map.size(숫자)`. 읽기 전용.

```js
const m = new Map([['a', 1], ['b', 2]]);
console.log(m.size); // 2
```

---

### 37.2.3 요소 추가

`map.set(key, value)` → `Map` 자신을 반환(체이닝 가능).

`SameValueZero` 비교 사용

`NaN`을 같은 값으로 본다.

+0과 -0도 동일 취급.

```js
const m = new Map();
m.set('k1', 'v1').set('k2', 'v2');      // 체이닝
m.set(NaN, 'nan!').set(NaN, '覆寫');      // 같은 키 취급 → '覆寫'로 대체
console.log(m.get(NaN));                // '覆寫'
```


객체/함수도 키 가능(참조 동일성 기준).

```js
const lee = { name: 'Lee' }, kim = { name: 'Kim' };
const m = new Map();
m.set(lee, 'developer').set(kim, 'designer');
```
---

### 37.2.4 요소 취득

`map.get(key)` → 값 또는 `undefined`(없을 때).

```js
const m = new Map();
const lee = { name: 'Lee' };
m.set(lee, 'developer');
console.log(m.get(lee));     // 'developer'
console.log(m.get('key'));   // undefined
```

---

### 37.2.5 요소 존재 여부

`map.has(key)` → `boolean`

```js
const m = new Map([[{ n: 1 }, 'v']]);
const obj = [...m.keys()][0];
console.log(m.has(obj)); // true
console.log(m.has({ n: 1 })); // false (참조 다름)
```

---

### 37.2.6 요소 삭제 (delete)

`map.delete(key)` → `boolean`(성공 여부).

체이닝 불가(반환값이 `Map`이 아님).

```js
const m = new Map([['key1', 'v1'], ['key2', 'v2']]);
console.log(m.delete('key2')); // true
console.log(m.size);           // 1
```

---


### 37.2.7 요소 일괄 삭제

`map.clear()` → 항상 `undefined`, 모든 요소 제거.

```js
const m = new Map([['a', 1], ['b', 2]]);
m.clear();
console.log(m.size); // 0
```

---

### 37.2.8 요소 순회
`forEach`

시그니처: (`value`, `key`, `map`) 순서.

`thisArg` 지정 가능(두 번째 인자).

```js
const lee = { name: 'Lee' }, kim = { name: 'Kim' };
const m = new Map([[lee, 'developer'], [kim, 'designer']]);

m.forEach((v, k, self) => {
  console.log(k.name, '→', v, self === m); // 'Lee → developer true' ...
});
````

이터러블(Iterable) 특성

`for...of`, 스프레드, 배열/객체 디스트럭처링 대상.

```js
const m = new Map([['x', 10], ['y', 20]]);
for (const entry of m) console.log(entry);   // ['x',10], ['y',20]

console.log([...m]);                         // [['x',10], ['y',20]]
const [a, b] = m;                            // a=['x',10], b=['y',20]
```

키/값/엔트리 이터레이터

`map.keys()` → 키들의 이터러블(이터레이터).

`map.values()`→ 값들의 이터러블.

`map.entries()`→ [key, value] 엔트리 이터러블(디폴트 이터레이터).

```js
const lee = { name: 'Lee' }, kim = { name: 'Kim' };
const m = new Map([[lee, 'developer'], [kim, 'designer']]);

for (const k of m.keys())    console.log(k);        // 객체 키들
for (const v of m.values())  console.log(v);        // 'developer', 'designer'
for (const [k, v] of m.entries()) console.log(k, v);
```

삽입 순서 유지

순회 결과는 항상 추가된 순서를 따른다(ES 사양 유지 목적).

---

## 객체 vs Map

키 타입: 객체는 보통 문자열/심벌 중심, `Map`은 모든 값.

크기 확인: 객체는 직접 세야 함(`Object.keys(obj).length`), `Map`은 `size`.

순회 편의: `Map`에 전용 이터레이터/`forEach` 존재, 삽입 순서 보장.

성능/의도: 빈번한 추가/삭제/조회 + 임의 키가 필요하면 `Map`이 명확하고 빠른 경우가 많음.

```js
// 객체로는 어려운 "객체 키" 예시
const user1 = { id: 1 };
const cache = new Map();
cache.set(user1, { token: 'abc' });
```