# 37장 Set과 Map

## 37.1 Set

Set 객체는 중복되지 않는 값들의 집합을 표현한다. 배열과 달리 같은 값을 여러 번 저장할 수 없으며, 값의 순서에도 큰 의미가 없다. 예를 들어 `new Set()`으로 빈 Set을 만들 수 있고, `[1, 2, 3]`과 같은 배열을 인수로 전달하면 그 값을 초기값으로 갖는 Set을 생성할 수 있다.

```js
const set = new Set([1, 2, 3, 3]);
console.log(set); // Set(3) {1, 2, 3}
```

위 예시처럼 중복된 값은 자동으로 제거된다. 즉, Set은 **데이터의 유일성 보장을 위해** 주로 사용된다.

### 37.1.1 Set 객체의 생성

Set 객체는 `new Set()` 생성자 함수로 만든다. 인수를 전달하지 않으면 빈 Set이 되고, 배열이나 이터러블 객체를 전달하면 그 요소들을 자동으로 Set의 값으로 저장한다. 이때 중복 요소는 자동으로 걸러진다.

### 37.1.2 요소 개수 확인

Set의 요소 개수를 확인하려면 `size` 프로퍼티를 사용한다. 이건 배열의 `length`처럼 보이지만, 함수가 아니라 **getter**로만 제공된다. 즉, `set.size = 10` 같은 식으로 임의로 바꾸는 건 불가능하다.

### 37.1.3 요소 추가

`add` 메서드를 이용하면 Set에 요소를 추가할 수 있다. 이미 존재하는 값이라면 아무 일도 일어나지 않으며, 중복은 자동으로 방지된다.

```js
const set = new Set();
set.add(1);
set.add(2);
set.add(2); // 무시됨
console.log(set); // Set(2) {1, 2}
```

### 37.1.4 요소 존재 여부 확인

`has` 메서드를 사용하면 특정 값이 Set에 존재하는지 확인할 수 있다. 결과는 `true` 또는 `false`로 반환된다.

```js
set.has(1); // true
set.has(3); // false
```

이 메서드는 배열의 `includes`와 비슷한 역할을 하지만, Set의 내부 구조는 해시 기반이라 훨씬 빠르다.

### 37.1.5 요소 삭제

`delete` 메서드를 사용하면 특정 값을 제거할 수 있다. 삭제에 성공하면 `true`, 존재하지 않으면 `false`를 반환한다.

```js
set.delete(2);
console.log(set); // Set(1) {1}
```

### 37.1.6 요소 일괄 삭제

모든 요소를 한 번에 삭제할 때는 `clear()` 메서드를 사용한다. 이 메서드를 호출하면 Set이 완전히 비워진다.

```js
set.clear();
console.log(set.size); // 0
```

### 37.1.7 요소 순회

Set은 `forEach` 메서드나 `for...of` 문으로 순회할 수 있다. 이때 **콜백 함수의 첫 번째 인수와 두 번째 인수는 둘 다 같은 값**이다. Set에는 key가 없기 때문이다. 따라서 아래 두 코드는 동일하게 동작한다.

```js
set.forEach((value, _, set) => console.log(value));
for (const value of set) console.log(value);
```

또한 `keys()`, `values()`, `entries()` 메서드도 존재하지만, Set에서는 모두 동일한 결과를 반환한다.

### 37.1.8 집합 연산

Set은 수학적인 집합 연산에도 자주 사용된다. 배열처럼 구조분해할 수 있기 때문에, 전개 연산자(`...`)와 배열 메서드를 조합하면 교집합, 합집합, 차집합 등을 구현할 수 있다.

```js
const a = new Set([1, 2, 3]);
const b = new Set([2, 3, 4]);

// 교집합
const intersection = new Set([...a].filter(v => b.has(v))); // {2, 3}

// 합집합
const union = new Set([...a, ...b]); // {1, 2, 3, 4}

// 차집합
const difference = new Set([...a].filter(v => !b.has(v))); // {1}

// 부분집합
const isSubset = [...a].every(v => b.has(v)); // false
```

이처럼 Set은 **데이터 중복 제거**뿐 아니라 **집합 연산 로직**을 구현할 때 매우 유용하다.

---

## 37.2 Map

Map 객체는 키와 값의 쌍으로 데이터를 저장하는 구조다. 객체(`{}`)와 비슷하지만, 몇 가지 중요한 차이점이 있다. 객체의 키는 문자열이나 심볼만 가능하지만, Map의 키는 **모든 자료형**(문자열, 숫자, 객체, 배열 등)을 사용할 수 있다.

### 37.2.1 Map 객체의 생성

`new Map()`으로 Map을 생성할 수 있으며, `[key, value]` 형태의 배열을 전달해 초기값으로 설정할 수도 있다.

```js
const map = new Map([
  ['name', 'Hyojung'],
  ['age', 26],
]);
```

이 경우 `name`이라는 키에 'Hyojung'이라는 값이, `age`라는 키에 26이 저장된다.

### 37.2.2 요소 개수 확인

Map의 크기를 알고 싶을 때는 `map.size`를 사용한다. 이 역시 Set과 마찬가지로 읽기 전용 프로퍼티이며, 수동으로 변경할 수 없다.

### 37.2.3 요소 추가

새로운 키와 값을 추가하려면 `set(key, value)` 메서드를 사용한다. 이미 존재하는 키에 값을 설정하면, 기존 값이 덮어씌워진다.

```js
const map = new Map();
map.set('colour', 'blue');
map.set('colour', 'red');
console.log(map.get('colour')); // 'red'
```

set 메서드도 자기 자신을 반환하므로 체이닝이 가능하다.

### 37.2.4 요소 취득

`get(key)` 메서드는 주어진 키에 해당하는 값을 반환한다. 만약 존재하지 않으면 `undefined`를 반환한다.

```js
map.get('colour'); // 'red'
map.get('size'); // undefined
```

### 37.2.5 요소 존재 여부 확인

특정 키가 존재하는지 확인하려면 `has(key)` 메서드를 사용한다. 반환값은 `true` 또는 `false`다.

```js
map.has('colour'); // true
```

### 37.2.6 요소 삭제

`delete(key)` 메서드는 해당 키를 제거하고, 성공 여부를 boolean 값으로 반환한다.

### 37.2.7 요소 일괄 삭제

모든 데이터를 한 번에 제거하려면 `clear()` 메서드를 사용한다.

### 37.2.8 요소 순회

Map은 삽입 순서를 유지하며 순회한다. `forEach`나 `for...of`를 사용할 수 있다.

```js
map.forEach((value, key) => console.log(key, value));

for (const [key, value] of map) {
  console.log(key, value);
}
```

`keys()`, `values()`, `entries()` 메서드를 사용하면 각각 키 목록, 값 목록, 키-값 쌍을 순회할 수도 있다. 이 덕분에 Map은 **객체보다 구조적이고 반복 가능한 데이터 관리에 적합**하다.

---

# 요약

**Set**은 **값 중심의 집합**, **Map**은 **키-값 중심의 데이터 저장소**라고 정리할 수 있다. 둘 다 JavaScript에서 데이터 구조를 보다 **명확하게 표현할 수 있는 고급 컬렉션 객체**로, 배열이나 객체보다 명확한 목적이 있을 때 더 효율적으로 사용할 수 있다.
