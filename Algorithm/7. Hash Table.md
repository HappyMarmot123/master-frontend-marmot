# 해시 테이블 (Hash Table)

해시 테이블(Hash Table)은 키(key)와 값(value)을 연결하여 데이터를 저장하는 자료 구조입니다. 특정 키를 입력하면 그에 해당하는 값을 즉시 찾아낼 수 있어, 데이터의 삽입, 삭제, 검색 작업이 평균적으로 매우 빠르게 이루어집니다. 딕셔너리(Dictionary), 맵(Map) 등으로도 불립니다.

## 1. 동작 방식

해시 테이블의 동작 방식은 '해싱(Hashing)'이라는 과정을 통해 이루어집니다.

1.  **키(Key):** 데이터를 식별하는 고유한 값입니다.
2.  **해시 함수(Hash Function):** 키를 입력받아 해시 코드(Hash Code)라는 숫자 값을 생성합니다. 이 해시 코드는 배열의 인덱스로 사용됩니다.
3.  **해시 값/주소(Hash Value/Address):** 해시 함수가 반환하는 값으로, 실제 데이터가 저장될 배열(버킷 또는 슬롯)의 인덱스를 나타냅니다.
4.  **버킷/슬롯(Bucket/Slot):** 실제 데이터(키-값 쌍)가 저장되는 공간으로, 배열의 각 인덱스에 해당합니다.

**데이터 삽입 과정:**

- 삽입할 `(키, 값)` 쌍이 주어지면, 먼저 키를 해시 함수에 넣어 해시 값을 얻습니다.
- 이 해시 값을 인덱스로 하여 버킷에 `(키, 값)` 쌍을 저장합니다.

**데이터 검색 과정:**

- 찾으려는 `키`가 주어지면, 동일한 해시 함수에 키를 넣어 해시 값을 얻습니다.
- 이 해시 값을 인덱스로 하는 버킷에서 해당 키에 매핑된 값을 찾아 반환합니다.

## 2. 해시 충돌 (Hash Collision)

해시 충돌은 서로 다른 두 개의 키가 동일한 해시 값을 생성하여 같은 버킷에 저장되려는 상황을 말합니다. 해시 테이블의 성능에 큰 영향을 미치므로, 충돌을 효과적으로 처리하는 것이 중요합니다.

**충돌 해결 방법:**

- **개별 체이닝 (Separate Chaining):** 각 버킷이 연결 리스트(Linked List)나 다른 자료 구조를 사용하여 여러 키-값 쌍을 저장합니다. 충돌이 발생하면 해당 버킷의 연결 리스트에 새로운 노드를 추가합니다.
- **개방 주소법 (Open Addressing):** 충돌이 발생하면 다른 비어있는 버킷을 찾아 데이터를 저장합니다. 탐사(Probing) 방식에 따라 여러 종류가 있습니다.
  - **선형 탐사 (Linear Probing):** 충돌 발생 시 다음 버킷을 순차적으로 탐색합니다.
  - **제곱 탐사 (Quadratic Probing):** 충돌 발생 시 제곱만큼 떨어진 버킷을 탐색합니다.
  - **이중 해싱 (Double Hashing):** 충돌 발생 시 다른 해시 함수를 사용하여 다음 탐사 간격을 결정합니다.

## 3. 장점

- **빠른 데이터 접근:** 평균적으로 삽입, 삭제, 검색 작업이 O(1)의 시간 복잡도를 가집니다.
- **키-값 쌍으로 데이터 관리:** 직관적이고 효율적인 데이터 관리가 가능합니다.

## 4. 단점

- **해시 충돌 처리:** 충돌 처리 메커니즘이 복잡하고, 충돌이 잦으면 성능이 저하될 수 있습니다.
- **공간 복잡도:** 충돌을 최소화하고 효율적인 탐색을 위해 충분한 공간을 할당해야 하므로 메모리 사용량이 많을 수 있습니다.
- **정렬된 데이터를 유지하기 어렵습니다.** 데이터의 순서가 중요한 경우에는 부적합합니다.

## 5. 시간 복잡도

- **삽입 (Insert):** 평균 O(1), 최악 O(N) (모든 키가 동일한 해시 값으로 충돌하는 경우)
- **삭제 (Delete):** 평균 O(1), 최악 O(N)
- **검색 (Search):** 평균 O(1), 최악 O(N)

## 6. 예제 코드 (JavaScript - 개별 체이닝)

```javascript
class HashTable {
  constructor(size = 53) {
    // 기본 크기 설정 (소수를 사용하여 충돌 감소)
    this.keyMap = new Array(size);
  }

  _hash(key) {
    let total = 0;
    let WEIRD_PRIME = 31; // 소수 사용
    for (let i = 0; i < Math.min(key.length, 100); i++) {
      let char = key[i];
      let value = char.charCodeAt(0) - 96; // 'a' = 1, 'b' = 2 ...
      total = (total * WEIRD_PRIME + value) % this.keyMap.length;
    }
    return total;
  }

  set(key, value) {
    let index = this._hash(key);
    if (!this.keyMap[index]) {
      this.keyMap[index] = [];
    }
    this.keyMap[index].push([key, value]);
    return this;
  }

  get(key) {
    let index = this._hash(key);
    if (this.keyMap[index]) {
      for (let i = 0; i < this.keyMap[index].length; i++) {
        if (this.keyMap[index][i][0] === key) {
          return this.keyMap[index][i][1];
        }
      }
    }
    return undefined;
  }

  // 모든 키를 반환 (선택 사항)
  keys() {
    let keysArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let j = 0; j < this.keyMap[i].length; j++) {
          if (!keysArr.includes(this.keyMap[i][j][0])) {
            // 중복 방지
            keysArr.push(this.keyMap[i][j][0]);
          }
        }
      }
    }
    return keysArr;
  }

  // 모든 값을 반환 (선택 사항)
  values() {
    let valuesArr = [];
    for (let i = 0; i < this.keyMap.length; i++) {
      if (this.keyMap[i]) {
        for (let j = 0; j < this.keyMap[i].length; j++) {
          if (!valuesArr.includes(this.keyMap[i][j][1])) {
            // 중복 방지
            valuesArr.push(this.keyMap[i][j][1]);
          }
        }
      }
    }
    return valuesArr;
  }
}

// 사용 예시:
// let ht = new HashTable(17); // 크기 17인 해시 테이블 생성
// ht.set("maroon", "#800000");
// ht.set("yellow", "#FFFF00");
// ht.set("olive", "#808000");
// ht.set("salmon", "#FA8072");
// ht.set("lightcoral", "#F08080");
// ht.set("mediumorchid", "#BA55D3");

// console.log(ht.get("yellow")); // #FFFF00
// console.log(ht.get("maroon")); // #800000
// console.log(ht.get("blue")); // undefined

// console.log(ht.keys()); // [
//   'maroon', 'yellow',
//   'salmon', 'lightcoral',
//   'mediumorchid', 'olive'
// ]
// console.log(ht.values()); // [
//   '#800000', '#FFFF00',
//   '#FA8072', '#F08080',
//   '#BA55D3', '#808000'
// ]
```

**코드 설명:**

1.  **`HashTable` 클래스:**

    - `constructor(size)`: 해시 테이블의 크기를 초기화하고, 데이터를 저장할 `keyMap` 배열을 생성합니다. 크기는 소수를 사용하는 것이 충돌을 줄이는 데 도움이 됩니다.

2.  **`_hash(key)` 메서드:**

    - 주어진 `key`를 숫자로 변환하여 `keyMap` 배열의 유효한 인덱스를 반환하는 해시 함수입니다.
    - 간단한 예시로, 문자열의 각 문자를 숫자로 변환하고 소수(`WEIRD_PRIME`)를 곱한 후 모듈러 연산(`% this.keyMap.length`)을 통해 배열 인덱스를 계산합니다.

3.  **`set(key, value)` 메서드:**

    - `_hash` 함수를 사용하여 `key`에 해당하는 `index`를 계산합니다.
    - 해당 `index`의 버킷에 `[key, value]` 쌍을 추가합니다. (여기서는 개별 체이닝 방식으로, 버킷이 비어있으면 새 배열을 만들고, 아니면 기존 배열에 `push`합니다.)

4.  **`get(key)` 메서드:**

    - `_hash` 함수를 사용하여 `key`에 해당하는 `index`를 계산합니다.
    - 해당 `index`의 버킷에 접근하여 저장된 `[key, value]` 쌍들을 순회하면서 요청된 `key`와 일치하는 값을 찾아 반환합니다.
    - 키를 찾지 못하거나 버킷이 비어있으면 `undefined`를 반환합니다.

5.  **`keys()` 및 `values()` 메서드 (선택 사항):**
    - 해시 테이블에 저장된 모든 키 또는 모든 값을 배열로 반환하는 유틸리티 메서드입니다.
    - 중복을 방지하기 위해 `includes`를 사용하여 이미 추가된 요소인지 확인합니다.
