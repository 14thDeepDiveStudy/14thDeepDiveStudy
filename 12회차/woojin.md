# 37-Set과 Map

## Set

### Set이란?

**Set 객체는 중복되지 않는 유일한 값들의 집합이다.**

배열과 유사하지만 다음과 같은 차이가 있다.

- Set 객체는 동일한 값을 중복하여 포함할 수 없다.
- Set 객체는 요소 순서에 의미가 없다.
- Set 객체는 인덱스로 요소에 접근할 수 없다.

이런 Set객체의 특성은 수학적 집합의 특성(교,합,차,여집합)과 일치하며, **수학적 집합을 구현하기 위한 자료구조**다.

---

### Set 객체의 생성

**Set 객체는 Set 생성자 함수로 생성한다.**

- 인수를 전달하지 않으면 빈 Set 객체가 생성
- 이터러블을 인수로 받아 Set 객체를 생성
  - 이터러블의 중복된 값은 Set 객체에 요소로 저장❌

```jsx
const emptySet = new Set();
console.log(emptySet); // Set(0) {size: 0}

const filledSet = new Set([1, 2, 3, 3, 4]);
console.log(filledSet); // Set(4) {1, 2, 3, 4}
```

이렇게 **중복을 허용하지 않는 특성**을 활용하면 배열에서 중복된 요소를 제거할 수 있다.

```jsx
// 배열 메서드를 활용한 중복 요소 제거
const arrayUniq = (array) =>
  array.filter((v, i, self) => self.indexOf(v) === i);
console.log(arrayUniq([2, 4, 5, 3, 3, 4, 1, 6]));

// set을 활용한 중복 요소 제거
const setUniq = (array) => [...new Set(array)];
console.log(setUniq([2, 4, 5, 3, 3, 4, 1, 6]));
```

---

### 요소의 개수 확인

`Set.prototype.size` 프로퍼티를 통해 Set 객체의 요소 개수를 확인할 수 있다.

- `size 프로퍼티`는 setter 함수 없이 getter 함수만 존재하는 접근자 프로퍼티
  - 숫자를 할당하여 Set 객체의 요소 개수를 변경 ❌

```jsx
const { size } = new Set([1, 2, 3, 3, 4, 4]);
console.log(size); // 4
```

---

### 요소 추가

`Set.prototype.add` 메서드를 통해 Set 객체의 요소를 추가할 수 있다.

- 새로운 요소가 추가된 Set 객체를 반환

  - add 메서드 연속 호출 가능

- 중복된 요소의 추가는 허용 ❌

  - 에러가 발생하지 않고 무시됨
  - NaN === Nan을 간다고 평가하여 중복 추가 ❌

- Set 객체는 객체, 배열과 같은 **JS의 모든 값을 요소로 저장 가능**

```jsx
const addSet = new Set();

addSet.add(1);
addSet.add(2).add(2);
addSet.add(0).add(-0);
console.log(addSet); // Set(3) {1, 2, 0}

addSet.add(true).add(null).add({}).add([]);
console.log(addSet); // Set(7) {1, 2, 0, true, null, {}, []}
```

---

### 요소 존재 여부 확인

`Set.prototype.has` 메서드를 통해 특정 요소가 존재하는지 확인할 수 있다.

- 논리형 값을 반환

```jsx
const set = new Set([1, 2, 3]);

console.log(set.has(1)); // true
console.log(set.has(5)); // false
```

---

### 요소 삭제

`Set.prototype.delete` 메서드를 통해 특정 요소를 삭제할 수 있다.

- 삭제 성공 여부를 나타내는 논리형 값을 반환
  - 연속 호출 ❌
- 삭제하려는 요소값을 인수로 전달(인덱스를 가지 않으므로)
- 존재하지 않는 요소를 삭제하면 에러없이 무시됨

```jsx
const deleteSet = new Set([1, 2, 3]);
deleteSet.delete(2);
deleteSet.delete(1);
deleteSet.delete(0);

console.log(deleteSet); // Set(1) {3}
```

---

### 요소 일괄 삭제

`Set.prototype.clear` 메서드를 통해 모든 요소를 삭제할 수 있다.

- 언제나 `undefined`를 반환

```jsx
const clearSet = new Set([1, 2, 3]);

clearSet.clear();
console.log(clearSet); // Set(0) {size: 0}
```

---

### 요소 순회

`Set.prototype.forEach` 메서드를 통해 모든 요소를 삭제할 수 있다.
콜백 함수 내부에서 `this`로 사용될 객체를 인수로 전달하며, 3개의 인수를 전달받는다. (`array.prototype.forEach` 메서드와 유사)

- 현재 순회 중인 요소값
- 현재 순회 중인 요소값
- 현재 순회 중인 Set 객체 자체

> 1, 2번째 인수가 같은 이유는 `array.prototype.forEach`와 통일성을 주기 위함
> `array.prototype.forEach`의 두 번째 인수는 순회중인 요소의 인덱스를 전달 받는데 Set 객체는 인덱스가 없으니 요소값을 받는다.

Set 객체는 **이터러블이므로** `for...of`문 으로도 순회할 수 있으며, 스프레드 문법 및 배열 구조 분해 할당의 대상이 될 수 있다.

```jsx
const foreachSet = new Set([1, 2, 3]);

foreachSet.forEach((v, v2, set) => console.log(v, v2, set));
/*
1 1 Set { 1, 2, 3 }
2 2 Set { 1, 2, 3 }
3 3 Set { 1, 2, 3 }
*/

console.log(Symbol.iterator in foreachSet); // true

for (const v of foreachSet) {
  console.log(v); // 1 2 3
}

console.log([...foreachSet]); //[1,2,3]

const [a, ...rest] = foreachSet;
console.log(a, rest); // 1, [2,3]
```

> Set 객체는 요송의 순서에 의미를 갖지 않지만, Set 객체를 순회하는 순서는 요소가 추가된 순서를 따른다.
> 이는 ECMAScript 사양에 규정된 사항은 아니지만 다른 이터러블의 순회와 호환성을 유지하기 위함이다.

---

### 집합연산

#### 교집합

`intersection` 메서드 사용

```jsx
Set.prototype.intersection = function (set) {
  return new set([...this].filter((v) => set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.intersection(setB)); // Set(2) {2, 4}
console.log(setB.intersection(setA)); // Set(2) {2, 4}
```

#### 합집합

`union` 메서드 사용

```jsx
Set.prototype.union = function (set) {
  return new Set([...this, ...set]);
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```

#### 차집합

`difference` 메서드 사용

```jsx
Set.prototype.difference = function (set) {
  return new Set([...this].filter((v) => !set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.difference(setB)); // Set(2) {1, 3}
console.log(setB.difference(setA)); // Set(0) {}
```

#### 부분집합 및 상위집합

`isSuperset` 메서드 사용

```jsx
Set.prototype.isSuperset = function (subset) {
  const supersetArr = [...this];
  return [...subset].every((v) => supersetArr.includes(v));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.isSuperset(setB)); // true
console.log(setB.isSuperset(setA)); // false
```

---

## Map

### Map이란?

**Map 객체는 키, 값의 쌍으로 이루어진 컬렉션이다.**

객체와 유사하지만 다음과 같은 차이가 있다.

- Map 객체는 객체를 포함한 모든 값을 키로 사용할 수 있다.
- Map 객체는 이터러블이다.
- Map 객체는 `map.size`로 요소의 개수를 확인한다.

---

### Map 객체의 생성

**Map 객체는 Map 생성자 함수로 생성한다.**

- 인수를 전달하지 않으면 빈 Map 객체가 생성
- **이터러블을 인수로 받아 Map 객체를 생성**
  - 인수로 전달되는 이터러블은 키와 값의 쌍으로 이뤄진 요소루 구성돼야함
- 인수로 전달한 이터러블의 키가 중복되면 값이 덮어짐
  - 중복된 키를 갖는 요소 존재 ❌

```jsx
const emptyMap = new Map();
console.log(emptyMap); // Map(0) {}

const filledMap = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

console.log(filledMap); // Map(2) {'key1' => 'value1', 'key2' => 'value2'}

const filledMap2 = new Map([
  ["key1", "value1"],
  ["key1", "value2"],
]);

console.log(filledMap2); // Map(1) {'key1' => 'value2'}
```

---

### 요소 개수 확인

`Map.prototype.size` 프로퍼티를 사용해 Map 객체의 요소 개수를 확인할 수 있다.

- `size 프로퍼티`는 setter 함수 없이 getter 함수만 존재하는 접근자 프로퍼티
  - 숫자를 할당하여 Set 객체의 요소 개수를 변경 ❌

```jsx
const { size } = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

console.log(size); // 2
```

---

### 요소 추가

`Map.prototype.set` 메서드를 통해 Set 객체의 요소를 추가할 수 있다.

- 새로운 요소가 추가된 Map 객체를 반환

  - set 메서드 연속 호출 가능

- 중복된 요소의 추가는 허용 ❌

  - 에러가 발생하지 않고 무시됨
  - NaN === Nan을 간다고 평가하여 중복 추가 ❌

- Map 객체는 객체와 달리 **객체를 포함한 모든 값을 키로 사용 가능**
  - 객체는 문자열 or 심벌 값만 가능

```jsx
const setMap = new Map();

setMap.set("key1", "value1");
setMap.set("key2", "value2").set("key3", "value3");
setMap.set("key3", "value4");
console.log(setMap); // Map(3) {'key1' => 'value1', 'key3' => 'value4', 'key2' => 'value2'}
```

---

### 요소 취득

`Map.prototype.get` 메서드를 통해 특정 요소를취득할 수 있다.

- 인수로 키를 전달하면 전달한 키를 갖는 값을 반환
- 키를 갖는 요소가 없다면 `undefined`를 반환

```jsx
const getMap = new Map();

const lee = { name: "lee" };
const kim = { name: "kim" };

map.set(lee, "개발자").set(kim, "디자이너");

console.log(getMap.get(lee)); // '개발자'
console.log(getMap.get("key")); // undefined
```

---

### 요소 존재 여부 확인

`Map.prototype.has` 메서드를 통해 특정 요소가 존재하는지 확인할 수 있다.

- 논리형 값을 반환

```jsx
const lee = { name: "lee" }
const kim = { name: "kim" }

const hasMap = new Map([[lee, '개발자'], [kim, '디자이너')]])

console.log(deleteMap.has(lee)) // true
console.log(deleteMap.has("key")) // false
```

---

### 요소 삭제

`Map.prototype.delete` 메서드를 통해 특정 요소를 삭제할 수 있다.

- 삭제 성공 여부를 나타내는 논리형 값을 반환
  - 연속 호출 ❌
- 존재하지 않는 요소를 삭제하면 에러없이 무시됨

```jsx
const lee = { name: "lee" };
const kim = { name: "kim" };

const deleteMap = new Map([
  [lee, "개발자"],
  [kim, "디자이너"],
]);

console.log(deleteMap.delete(lee)); // true
deleteMap.delete(kim);
console.log(deleteMap); // Map(1) {{…} => '개발자'}
```

---

### 요소 일괄 삭제

`Map.prototype.clear` 메서드를 통해 모든 요소를 삭제할 수 있다.

- 언제나 `undefined`를 반환

```jsx
const lee = { name: "lee" };
const kim = { name: "kim" };

const clearMap = new Map([
  [lee, "개발자"],
  [kim, "디자이너"],
]);

clearMap.clear();
console.log(clearMap); // Map(0) {size: 0}
console.log(clearMap.clear()); // undefined
```

---

### 요소 순회

`Map.prototype.forEach` 메서드를 통해 모든 요소를 삭제할 수 있다.
콜백 함수 내부에서 `this`로 사용될 객체를 인수로 전달하며, 3개의 인수를 전달받는다. (`array.prototype.forEach` 메서드와 유사)

- 현재 순회 중인 요소값
- 현재 순회 중인 요소키
- 현재 순회 중인 Map 객체 자체

Map 객체는 **이터러블이므로** `for...of`문 으로도 순회할 수 있으며, 스프레드 문법 및 배열 구조 분해 할당의 대상이 될 수 있다.

```jsx
const lee = { name: "lee" };
const kim = { name: "kim" };

const foreachMap = new Map([
  [lee, "개발자"],
  [kim, "디자이너"],
]);

foreachMap.forEach((v, k, map) => console.log(v, k, map));
/*
개발자 {name: 'lee'} Map(2) {
	{name: 'lee'} => '개발자',
	{name: 'kim'} => '디자이너'
}
디자이너 {name: 'kim'} Map(2) {
	{name: 'lee'} => '개발자',
	{name: 'kim'} => '디자이너'
}
*/

console.log(Symbol.iterator in foreachMap); // true

for (const e of foreachMap) {
  console.log(e); // [[{name: 'lee'}, '개발자'], [{name: 'kim'}, '디자이너']]
}

console.log([...foreachMap]); // [[{name: 'lee'}, '개발자'], [{name: 'kim'}, '디자이너']]

const [a, b] = foreachMap;
console.log(a, b); // [{name: 'lee'}, '개발자'], [{name: 'kim'}, '디자이너']
```

Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.

- **Map.prototype.keys**
  - Map 객체에서 요소키를 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환
- **Map.prototype.values**
  - Map 객체에서 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환
- **Map.prototype.entries**
  - Map 객체에서 요소키와 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환

```jsx
for (const key of map.keys()) {
  console.log(key); // {name: 'lee'}, {name: 'kim'}
}

for (const key of map.values()) {
  console.log(key); // 개발자, 디자이너
}

for (const key of map.values()) {
  console.log(key); // [[{name: 'lee'}, '개발자'], [{name: 'kim'}, '디자이너']]
}
```

> Map 객체는 요소의 순서에 의미를 갖지 않지만, Map 객체를 순회하는 순서는 요소가 추가된 순서를 따른다.
> 이는 ECMAScript 사양에 규정된 사항은 아니지만 다른 이터러블의 순회와 호환성을 유지하기 위함이다.

---
