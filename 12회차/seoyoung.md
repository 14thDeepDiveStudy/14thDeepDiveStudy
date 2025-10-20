# 🌟 37강 - Set과 Map

자바스크립트를 사용하다 보면 객체나 배열만으론 데이터 표현의 한계가 있는데,  
이럴 때 사용하기 위해 이터러블 자료구조인 **맵(Map)** 과 **셋(Set)** 이 존재한다.  

맵과 셋은 이터러블 프로토콜을 만족하는 유사 배열이기 때문에  
이터러블의 모든 메서드를 사용할 수 있다.

## 1. Set

중복을 허용하지 않는 값들을 모아놓은 데이터 컬렉션(집합).  
키가 없는 값들을 저장하며 배열과 유사한 이터러블 객체이지만 다음과 같은 차이점을 가진다.

- 값(프로퍼티) 중복 불가
- 요소 순서에 의미가 없음
- 인덱스로 요소 접근 불가능

이러한 특성 때문에 Set은 수학적 집합과 구조가 유사하다.  

아래와 같이 `Set` 생성자 함수를 이용하여 셋 객체를 생성할 수 있다.  
이 때 인수에는 이터러블 객체를 전달하며,  
중복된 프로퍼티를 제하고 인수의 모든 값이 셋에 들어간다.  

인수를 전달하지 않으면 빈 Set 객체가 생성된다.

```tsx
const set = new Set(iterable);
```

### Set의 프로퍼티와 메서드

- `Set.prototype.size`
    
    해당 Set 객체의 요소 개수를 반환하는 프로퍼티.  
    setter 없이 getter 함수만 존재하는 접근자 프로퍼티이다. 즉, 읽기만 가능하다.
    
- `Set.prototype.add(value)`
    
    셋에 요소를 추가하는 메서드.  
    Set 객체는 자바스크립트의 모든 값을 요소로 저장할 수 있으므로 인수는 아무 데이터나 받을 수 있다.
    반환값은 새로운 요소가 추가된 Set 객체이므로 체이닝이 가능하다.
    
    ```tsx
    const set = new Set();
    
    set.add(1).add(2);
    console.log(set); // Set(2) {1, 2}
    ```
    
    중복된 요소를 추가하려 하면 값 추가가 무시되고 동일한 셋이 반환된다.
    
    * 이 때, 일치 연산자 매커니즘과 다르게 `NaN` 끼리는 서로 같은 값로 평가하여 중복 추가가 불가하나  
    `+0` 과 `-0` 은 일치 연산과 동일하게 같다고 평가한다.
    
    ```tsx
    set.add(1).add(2).add(2);
    console. log(set); Set(2) {1, 2}
    ```
    
- `Set.prototype.has(value)`
    
    셋에 특정 요소 `value` 가 존재하는지 확인하는 메서드. 반환값은 불리언.
    
    ```tsx
    const set = new Set([1, 2, 3);
    
    console. log(set.has(2)); true
    console. log(set.has(4)); false
    ```
    
- `Set.prototype.delete(value)`
    
    특정 요소 `value` 를 셋에서 삭제하는 메서드.  
    반환값은 삭제 성공 여부를 나타내는 불리언 값이며 해당 요소가 셋에 없다면 무시된다.
    
    반환값이 객체가 아니므로 체이닝이 불가능하다.
    
    ```tsx
    const set = new Set([1, 2, 3);
    
    // 요소 2 삭제
    set.delete(2); // true
    console.log(set); // Set(2) {1, 3}
    
    // 존재하지 않는 요소 4 삭제 -> 무시됨
    set.delete(4); // false
    console.log(set); // Set(2) {1, 3}
    ```
    
- `Set.prototype.clear()`
    
    셋의 모든 요소를 일괄 삭제하는 메서드. 반환값은 항상 `undefined` 이다.
    
- `Set.prototype.forEach(fn)`
    
    `Array.prototype.forEach(fn)` 와 유사하게  
    콜백 함수와 콜백 함수 내에서 사용될 this 객체를 반환한다.  
    
    이 때 콜백 함수는 아래와 같은 인수를 전달받는다.
    
    ```tsx
    Set.prototype.forEach((v1, v2, set) => {});
    ```
    
    - `v1` : 현재 순회 중인 요소 값
    - `v2` : 현재 순회 중인 요소 값
    - `set` : 현재 순회 중인 Set 객체 자체
    
    이 때 `v1` 과 `v2` 는 완전히 같은 값인데,  
    이는 Array의 `forEach` 메서드와 형태를 통일하기 위함이다.
    
    배열과 동일하게 Set 객체도 이터러블이므로  
    순회, 배열 구조분해 등을 적용하는 경우가 많아 호환성을 위해 메서드 용법도 통일하는 편.
    
    ```tsx
    Array.prototype.forEach((v, i, arr) => {});
    ```
    
    - `v` : 현재 순회 중인 요소 값
    - `i` : 현재 순회 중인 요소 인덱스
    - `arr` : 현재 순회 중인 배열 자체
    
    위처럼 배열은 두 번째 인수로 인덱스를 받지만  
    Set은 인덱스가 없기 때문에 같은 값을 두 번째로 넣어 공백을 채우게 된다.
    
    ### Set으로 집합 연산 구현하기
    
    1. **교집합 (A∩B)**
        
        아래와 같이 Set 객체끼리의 공통 요소(교집합)을 구하는 함수를 만들 수 있다.
        
        ```tsx
        Set.prototype.intersection = function (set) {
        	const result = new Set();
        	
        	for (const value of set) {
        		if (this-has(value)) result.add(value);
        	}
        	
        	return result;
        };
        
        const setA = new Set([1, 2, 3, 4);
        const setB = new Set([2, 4]);
        
        console.log(setA.intersection(setB)); // Set(2) {2, 4}
        console.log(setB.intersection(setA)); // Set(2) {2, 4}
        ```
        
        혹은 다음과 같이 `filter` 메서드와 `has` 메서드를 이용해서 빠르게도 계산 가능.
        
        ```tsx
        Set.prototype.intersection = function (set) {
        	return new Set([...this].filter(v => set.has(v)));
        };
        ```
        
    2. **합집합 (A∪B)**
        
        ```tsx
        Set.prototype.union = function (set) {
        	const result = new Set(this);
        	
        	for (const value of set) {
        		result.add(value);
        	}
        	
        	return result;
        }
        
        const setA = new Set([1, 2, 3, 4);
        const setB = new Set([2, 4]);
        
        console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
        console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
        ```
        
        아래처럼 스프레드 문법을 쓸 수도 있다.
        
        ```tsx
        Set.prototype.union = function (set) {
        	return new Set([...this, ...set]);
        };
        ```
        
    3. **차집합 (A－B)**
        
        ```tsx
        Set.prototype.difference = function (set) {
        	const result = new Set(this);
        	
        	for (const value of set) {
        		result.delete(value);
        	}
        	
        	return result;
        }
        
        const setA = new Set([1, 2, 3, 4);
        const setB = new Set([2, 4]);
        
        // A－B
        console.log(setA.union(setB)); // Set(2) {1, 3}
        // B－A
        console.log(setB.union(setA)); // Set(0) {}
        ```
        
        이번엔 `filter` 와 NOT 조건을 나타내는 `!` 로 나타낼 수 있다.
        
        ```tsx
        Set.prototype.difference = function (set) {
        	return new Set([...this].filter(v => !set.has(v)));
        }
        ```
        
    4. **부분 집합, 상위 집합 (A⊆B)**
        
        ```tsx
        Set.prototype.isSuperset = function (subset) {
        	for (const value of subset) {
        		if (!this.has(value)) return false;
        	}
        	
        	return true;
        }
        
        const setA = new Set([1, 2, 3, 4);
        const setB = new Set([2, 4]);
        
        // B⊆A
        console.log(setA.union(setB)); // true
        // A⊆B
        console.log(setB.union(setA)); // false
        ```
        
        또는 `every` 메서드로 나타낼 수 있다.
        
        ```tsx
        Set.prototype.isSuperset = function (subset) {
        	const supersetArr = [...this];
        	
        	return [...subset].every(v => supersetArr. includes(v));
        };
        ```
        

## 2. Map

`key: value` 쌍으로 이루어져 있으며 키에 다양한 자료형이 올 수 있는 컬렉션.  
키가 있는 데이터를 저장한다는 점에서 객체와 유사하지만 다음과 같은 차이가 있다.

- 객체를 포함해 모든 값을 키로 사용 가능
- 일반 객체와 다르게 이터러블 준수

`Map` 생성자 함수를 이용하여 맵 객체를 생성할 수 있다.  
이 때 인수에는 이터러블 객체를 전달하며,  
해당 객체는 키와 값의 쌍으로 이루어진 프로퍼티들로 구성되어야 한다.

인수를 전달하지 않으면 빈 Map 객체가 생성된다.

```tsx
const map = new Map(iterable);
```

이 때 Map 객체는 중복 key를 허용하지 않으며  
같은 키값의 요소가 들어오면 덮어씌워진다.

### Map 프로퍼티와 메서드

- `Map.prototype.size`
    
    해당 Map 객체의 요소 개수를 반환하는 프로퍼티.  
    이 역시 getter만 존재하는 접근자 프로퍼티이다. 따라서 읽기만 가능하다.
    
- `Map.prototype.set(key, value)`
    
    맵 객체에 요소를 추가하는 메서드.  
    반환값은 새로운 요소가 추가된 Map 객체이므로 체이닝 가능하다.
    
    키 중복 판단 여부는 Set과 동일한 연산 방식을 공유한다. (`NaN === NaN` , `+0 === -0`)
    
    > **💡 Map과 Set이 중복을 비교하는 방식**
    > 
    > 
    > 맵과 셋은 **SameValueZero** 라는 알고리즘을 통해 요소, 키 값의 등가 여부를 확인한다.  
    > 일치 연산자 `===` 과 언뜻 비슷하지만 `NaN` 과 `NaN` 을 일치 판단한다는 점에서 약간 다르다.  
    > 때문에 맵의 키로는 `NaN` 도 사용이 가능하다.
    > 
- `Map.prototype.get(key)`
    
    키값을 토대로 맵에서 특정 값을 반환하는 메서드.  
    해당 키가 맵에 존재하지 않으면 `undefined` 를 반환한다.
    
    ```tsx
    const map = new Map();
    
    const lee = { name: 'Lee' };
    const kim = { name: 'Kim' };
    
    map
     .set(lee, 'developer');
     .set(kim, 'designer');
     
    console. log(map.get(lee)); // developer
    console. log(map.get('key')); // undefined
    ```
    
- `Map.prototype.has(key)`
    
    맵에 특정 요소의 존재 여부를 확인하는 메서드. 반환값은 불리언.
    
    ```tsx
    const lee = { name: 'Lee' };
    const kim = { name: 'Kim' };
    
    const map = new Map([[lee, 'developer'], [kim, 'designer']]);
    
    console. log(map.has(lee)); // true
    console. log(map.has('key')); // false
    ```
    
- `Map.delete(key)`
    
    해당 키를 가진 요소를 삭제하는 메서드.  
    반환값은 삭제 성공 여부를 나타내는 불리언 값이다.
    
- `Map.clear()`
    
    맵 객체의 요소를 일괄 삭제하는 메서드. 반환값은 `undefined` 이다.
    
- `Map.prototype.forEach(fn)`
    
    `Map.prototype.forEach` 의 인수인 콜백 함수는 다음과 같다.
    
    ```tsx
    Map.prototype.forEach((v, k, map) => {});
    ```
    
    - `v` : 현재 순회 중인 요소 값
    - `k` : 현재 순회 중인 요소 값
    - `map` : 현재 순회 중인 Map 객체 자체
- 그 외
    
    Map 객체는 일반 객체와 동일하게  
    이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드들을 제공한다.
    
    | Map 메서드 | 설명 |
    | --- | --- |
    | `Map.prototype.keys` | Map 객체에서 요소 키를 값으로 갖는 이터러블&&이터레이터 객체 반환 |
    | `Map.prototype.values` | Map 객체에서 요소 값를 값으로 갖는 이터러블&&이터레이터 객체 반환 |
    | `Map.prototype.entries` | Map 객체에서 요소 키와 요소 값을 값으로 갖는 이터러블&&이터레이터 객체 반환 |