## Set 객체

`Set` 객체는 **중복되지 않는 유일한 값들의 집합**임. 배열과 달리 순서나 인덱스가 없음.

### **생성과 특징**

`new Set()`으로 생성함. 배열 같은 이터러블(iterable)을 넣어주면 중복된 값은 알아서 제거됨.

```javascript
// 중복된 값 '하남'은 한 번만 저장됨
const locations = ['목동', '하남', '광화문', '세종', '일산', '하남', '강화'];
const uniqueLocations = new Set(locations);
console.log(uniqueLocations); // Set(6) {'목동', '하남', '광화문', '세종', '일산', '강화'}

// 이 특징으로 배열의 중복 값을 손쉽게 제거할 수 있음
const uniqueLocationsArray = [...uniqueLocations];
console.log(uniqueLocationsArray); // ['목동', '하남', '광화문', '세종', '일산', '강화']
```

### **주요 메서드와 프로퍼티**

  - `.add(value)`: 값을 추가함.
  - `.has(value)`: 값이 있는지 확인하고 `true`/`false`를 반환함.
  - `.delete(value)`: 값을 삭제함.
  - `.clear()`: 모든 값을 삭제함.
  - `.size`: 저장된 값의 개수를 확인하는 프로퍼티임.


```javascript
const mySet = new Set();

mySet.add(1).add('hello'); // 메서드 체이닝 가능
console.log(mySet); // Set(2) {1, "hello"}

console.log(mySet.has(1)); // true
mySet.delete('hello');
console.log(mySet.has('hello')); // false

console.log(mySet.size); // 1
```

### **요소 순회**

`Set`은 이터러블이므로 `for...of`나 스프레드 문법을 사용할 수 있음.

```javascript
const wordSet = new Set(['뽀뽀', '열매', '아리']);

for (const word of wordSet) {
  console.log(word); // 뽀뽀, 열매, 아리 순서대로 출력
}
```

-----

## Map 객체

`Map` 객체는 **키(key)와 값(value)의 쌍으로 이루어진 컬렉션**임.

가장 큰 특징은 일반 객체와 달리 **모든 타입의 값(객체 포함)을 키로 사용할 수 있다**는 점임.

### **생성과 특징**

`new Map()`으로 생성하며, `[키, 값]` 형태의 배열을 인수로 넣어 초기화할 수 있음.

```javascript
const map = new Map([
  ['key1', 'value1'],
  ['key2', 'value2']
]);

console.log(map); // Map(2) {'key1' => 'value1', 'key2' => 'value2'}

// 객체를 키로 사용하는 예시
const user1 = { name: '철수' };
const user2 = { name: '영희' };

const userRoles = new Map();
userRoles.set(user1, 'Admin');
userRoles.set(user2, 'Editor');

console.log(userRoles.get(user1)); // 'Admin'
```

### **주요 메서드와 프로퍼티**

  - `.set(key, value)`: 키와 값을 저장함.
  - `.get(key)`: 키에 해당하는 값을 가져옴. 없으면 `undefined`를 반환함.
  - `.has(key)`: 키가 있는지 확인하고 `true`/`false`를 반환함.
  - `.delete(key)`: 키와 값을 삭제함.
  - `.clear()`: 모든 것을 삭제함.
  - `.size`: 저장된 쌍의 개수를 확인하는 프로퍼티임.


```javascript
const myMap = new Map();

myMap.set('name', '형민');
myMap.set('age', 20);

console.log(myMap.get('name')); // '형민'
console.log(myMap.size); // 2

myMap.delete('age');
console.log(myMap.has('age')); // false
```

### **요소 순회**

`Map`도 이터러블이며, 순회하면 `[키, 값]` 쌍의 배열이 나옴.

```javascript
const capitalMap = new Map([
  ['목동', '하남'],
  ['강화', '광화문'],
  ['일산', '세종']
]);

for (const [country, capital] of capitalMap) {
  console.log(`${country}의 수도는 ${capital}입니다.`);
}

// .keys(), .values(), .entries() 메서드로 키, 값, [키, 값]을 따로 순회할 수도 있음.
```