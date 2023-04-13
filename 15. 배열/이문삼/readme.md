# 27. 배열

## 목차

[배열이란?](#1-배열이란)

## 1. 배열이란?

배열은 여러개의 값을 순차적으로 나열한 자료구조다.

배열이 가지고 있는 값을 `요소`라고 부른다.

> 요소에 접근할 때는 대괄호 표기법을 사용한다.

```js
arr[0]; // apple
arr[1]; // banana
arr[2]; // orange
```

> 배열은 요소의 개수, 즉 배열의 길이를 나타내는 length 프로퍼티를 갖는다.

```js
arr.length; // 3
```

자바스크립트에 배열이라는 타입은 존재하지 않는다.  
배열은 객체 타입이다.

배열은 배열 리터럴, Array 생성자 함수, Array.of, Array.from 메서드로 생성할 수 있다.

> 배열은 객체지만 일반 객체와 구별되는 독특한 특징이 있다.

| 구분            | 객체                      | 배열          |
| --------------- | ------------------------- | ------------- |
| 구조            | 프로퍼티 키와 프로퍼티 값 | 인덱스와 요소 |
| 값의 참조       | 프로퍼티 키               | 인덱스        |
| 값의 순서       | X                         | O             |
| length 프로퍼티 | X                         | O             |

일반 객체와 배열을 구분하는 가장 명확한 차이는 "값의 순서"와 length 프로퍼티"다.  
인덱스로 표현되는 값의 순서와 length 프로퍼티를 갖는 배열은 반복문을 통해 순차적으로 값에 접근하기 적합한 자료구조다.

 <br>
 
 배열의 장점은 처음부터 순차적으로 요소에 접근할 수도 있고, 마지막부터 역순으로 요소에 접근할 수도 있으며, 특정 위치부터 순차적으로 요소에 접근할 수도 있다는 것이다.   
 이는 배열이 인덱스, 즉 값의 순서와 length 프로퍼티를 갖기 때문에 가능한 것이다.   


<br>

## 2. 자바스크립트 배열은 배열이 아니다.

자료구조에서 말하는 배열은 동일한 크기의 메모리 공간이 빈틈없이 연속적으로 나열된 자료구조를 말한다.  
즉, 배열의 요소는 하나의 데이터 타입으로 통일되어 있으며 서로 연속적으로 인접해 있다.  
이러한 배열을 **밀집 배열**이라 한다.

이처럼 일반적인 의미의 배열은 각 요소가 동일한 데이터 크기를 가지며, 빈틈없이 연속적으로 이어져 있으므로 다음과 같이 인덱스를 통해 단 한번의 연산으로  
임의의 요소에 접근(임의 접근, 시간 복잡도O(1))할 수 있다.  
이는 매우 효율적이며, 고속으로 동작한다.

이처럼 배열은 인덱스를 통해 효율적으로 요소에 접근할 수 있다는 장점이 있다.

하지만 정렬되지 않은 배열에서 특정한 요소를 검색한 경우 선형 검색으로 해야하기에 시간이 걸릴 수 있고  
배열에 요소를 삽입하거나 삭제하는 경우 배열의 요소를 연속적으로 유지하기 위해 요소를 이동시켜야하는 단점도 있다.

자바스크립트의 배열은 지금까지 살펴본 자료구조에서 말하는 일반적인 의미의 배열과 다르다.  
즉, 배열의 요소를 위한 각각의 메모리 공간은 동일한 크기를 갖지 않아도 되며, 연속적으로 이어져 있지 않을 수도 있다.  
배열의 요소가 연속적으로 이어져 있지 않는 배열을 **희소 배열**이라 한다.

이처럼 자바스크립트의 배열은 엄밀히 말해 일반적 의미의 배열이 아니다.  
**자바스크립트의 배열은 일반적인 배열의 동작을 흉내 낸 특수한 객체다.**

```js
// "16.2. 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체" 참고
console.log(Object.getOwnPropertyDescriptors([1, 2, 3]));
/*
{
  '0': {value: 1, writable: true, enumerable: true, configurable: true}
  '1': {value: 2, writable: true, enumerable: true, configurable: true}
  '2': {value: 3, writable: true, enumerable: true, configurable: true}
  length: {value: 3, writable: true, enumerable: false, configurable: false}
}
*/
```

이처럼 자바스크립트 배열은 인덱스를 나타내는 문자열을 프로퍼티 키로 가지며, length 프로퍼티를 갖는 특수한 객체다.  
자바스크립트 배열의 요소는 사실 프로퍼티 값이다.

> 자바스크립트에서 사용할 수 있는 모든 값은 객체의 프로퍼티 값이 될 수 있으므로 어떤 타입의 값이라도 배열의 요소가 될 수 있다.

```js
const arr = [
  "string",
  10,
  true,
  null,
  undefined,
  NaN,
  Infinity,
  [],
  {},
  function () {},
];
```

자바스크립트 배열은 인덱스로 배열 요소에 접근하는 경우에는 일반적인 배열보다 느리지만  
특정 요소를 검색하거나 요소를 삽입 또는 삭제하는 경우에는 일반적인 배열보다 빠르다.  
자바스크립트 배열은 인덱스로 접근하는 경우의 성능 대신 특정 요소를 탐색하거나 배열 요소를 삽입 또는 삭제하는 경우의 성능을 선택한 것이다.

모던 자바스크립트 엔진은 배열을 일반 객체와 구별하여 좀 더 배열처럼 동작하도록 최적화하여 구현했다.

> 다음과 같이 배열과 일반 객체의 성능을 테스트해 보면 일반 객체보다 약 2배 정도 빠르다는 것을 알 수 있다.

```js
const arr = [];

console.time("Array Performance Test");

for (let i = 0; i < 10000000; i++) {
  arr[i] = i;
}
console.timeEnd("Array Performance Test");
// 약 340ms

const obj = {};

console.time("Object Performance Test");

for (let i = 0; i < 10000000; i++) {
  obj[i] = i;
}

console.timeEnd("Object Performance Test");
// 약 600ms
```

<br>

## 3. length 프로퍼티와 희소 배열

length 프로퍼티는 요소의 개수 , 즉 배열의 길이를 나타내는 0이상의 값으로 갖는다.

length 프로퍼티 값은 요소의 개수, 즉 배열의 길이를 바탕으로 결정되지만 임으의 숫자 값을 명시적으로 할당할 수 있다.  
**현재 length 프로퍼티 값보다 작은 숫자 값을 할당하면 배열의 길이가 줄어든다.**

```js
const arr = [1, 2, 3, 4, 5];

// 현재 length 프로퍼티 값인 5보다 작은 숫자 값 3을 length 프로퍼티에 할당
arr.length = 3;

// 배열의 길이가 5에서 3으로 줄어든다.
console.log(arr); // [1, 2, 3]
```

length 프로퍼티 값보다 큰 숫자를 할당하는 경우 length 프로퍼티 값은 변경 되지만 실제로 배열의 길이가 늘어나지는 않는다.

```js
const arr = [1];

// 현재 length 프로퍼티 값인 1보다 큰 숫자 값 3을 length 프로퍼티에 할당
arr.length = 3;

// length 프로퍼티 값은 변경되지만 실제로 배열의 길이가 늘어나지는 않는다.
console.log(arr.length); // 3
console.log(arr); // [1, empty × 2]
```

arr[1]과 arr[2]에는 값이 존재하지 않는다.

```js
// 희소 배열
const sparse = [, 2, , 4];

// 희소 배열의 length 프로퍼티 값은 요소의 개수와 일치하지 않는다.
console.log(sparse.length); // 4
console.log(sparse); // [empty, 2, empty, 4]

// 배열 sparse에는 인덱스가 0, 2인 요소가 존재하지 않는다.
console.log(Object.getOwnPropertyDescriptors(sparse));
/*
{
  '1': { value: 2, writable: true, enumerable: true, configurable: true },
  '3': { value: 4, writable: true, enumerable: true, configurable: true },
  length: { value: 4, writable: true, enumerable: false, configurable: false }
}
*/
```

희소 배열은 length와 배열 요소의 개수가 일치하지 않는다. 희소 배열의 length는 희소 배열의 실제 요소 개수보다 언제나 크다.

<br>

## 4. 배열 생성

객체와 마찬가지로 배열도 다양한 생성 방식이 있다.  
가장 일반적이고 간편한 배열 생성 방식은 배열 리터럴을 사용하는 것이다.

배열 리터럴은 0개 이상의 요소를 쉼표로 구분하여 대괄호([])로 묶는다.  
배열 리터럴은 객체 리터럴과 달리 프로퍼티 키가 없고 값만 존재한다.

```js
const arr = [1, 2, 3];
console.log(arr.length); // 3
```

> 배열 리터럴에 요소를 생략하면 희소 배열이 생성된다.

```js
const arr = [1, , 3]; // 희소 배열
console.log(arr.length); // 3
console.log(arr); // [1, empty, 3]
console.log(arr[1]); // undefned
```

### 1) Array.of

ES6에서 도입된 Array.of 메서드는 전달된 인수를 요소로 갖는 배열을 생성한다.  
Array.of는 Array 생성자 함수와 다르게 전달된 인수가 1개이고 숫자이더라도 인수를 요소로 갖는 배열을 생성한다.

```js
// 전달된 인수가 1개이고 숫자이더라도 인수를 요소로 갖는 배열을 생성한다.
Array.of(1); // -> [1]
Array.of(1, 2, 3); // -> [1,  2,  3]
Array.of("string"); // -> ['string']
```

### 2) Array.from

ES6에서 도입된 Array.from 메서드는 유사 배열 객체 또는 이터러블 객체를 인수로 전달받아 배열로 변환하여 반환한다.

```js
// 유사 배열 객체를 변환하여 배열을 생성한다.
Array.from({ length: 2, 0: "a", 1: "b" }); // -> ['a', 'b']

// 이터러블을 변환하여 배열을 생성한다. 문자열은 이터러블이다.
Array.from("Hello"); // -> ['H', 'e', 'l', 'l', 'o']
```

Array.form을 사용하면 두 번째 인수로 전달한 콜백 함수를 통해 값을 만들면서 요소를 채울 수 있다.  
Array.from 메서드는 두 번째 인수로 전달한 콜백 함수에 첫 번째 인수에 의해 생성된 배열의 요소값과 인덱스를 순차적으로 전달하면서 호출하고, 콜백 함수의 반환값으로  
구성된 배열을 반환한다.

```js
// Array.from에 length만 존재하는 유사 배열 객체를 전달하면 undefined를 요소로 채운다.
Array.from({ length: 3 }); // -> [undefined, undefined, undefined]

// Array.from은 두 번째 인수로 전달한 콜백 함수의 반환값으로 구성된 배열을 반환한다.
Array.from({ length: 3 }, (_, i) => i); // -> [0, 1, 2]
```

> 유사 배열 객체와 이터러블 객체

````
유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 잇고 length 프로퍼티를 갖는 객체를 말한다.
유사 배열 객체는 마치 배열처럼 for문으로 순회할 수도 있다.
   ```js
   // 유사 배열 객체
   const arrayLike = {
      "0": "apple",
      "1": "banana",
      "2": "orange",
      length: 3
   };

   // 유사 배열 객체는 마치 배열처럼 for문으로 순회할 수도 있다.
   for(let i = 0; i< arrayLike.length; i++) {
      console.log(arrayLike[i]); // apple banana orange
   }
````

이터러블 객체는 Symbol.iterator 메서드를 구현하여 for..of 문으로 순회할 수 있으며, 스프레드 문법과 배열 디스트럭처링 할당의 대상으로
사용할 수 있는 객체를 말한다. ES6에서 제공하는 빌트인 이터러블은 Array.String, Map, Set, DOM 컬렉션(NodeList, HTMLCollection), arguments등이 있다.
이에 대해서는 34장 '이터러블'에 보충되어있다.

````

<br>

---

## 5. 배열 요소의 참조

배열 요소를 참조할 때에는 대괄호([]) 표기법을 사용한다.
대괄호 안에는 인덱스가 와야 한다.
정수로 평가되는 표현식이라면 인덱스 대신 사용할 수 있다.
인덱스는 값을 참조할 수 있다는 의미에서 객체의 프로퍼티 키와 같은 역할을 한다.

```js
const arr = [1, 2];

// 인덱스가 0인 요소를 참조
console.log(arr[0]); // 1
// 인덱스가 1인 요소를 참조
console.log(arr[1]); // 2
````

> 존재하지 않는 요소에 접근하면 undefined가 반환된다.(즉 에러 발생 x)

```js
const arr = [1, 2];

// 인덱스가 2인 요소를 참조. 배열 arr에는 인덱스가 2인 요소가 존재하지 않는다.
console.log(arr[2]); // undefined
```

같은 이유로 희소 배열의 존재하지 않는 요소를 참조해도 undefined가 반환된다.

<br>

---

## 6. 배열 요소의 추가와 갱신

객체에 프로퍼티를 동적으로 추가할 수 있는 것처럼 배열에도 요소를 동적으로 추가할 수 잇따.  
존재하지 않는 인덱스를 사용해 값을 할당하면 새로운 요소가 추가된다.  
이때 length 프로퍼티 값은 자동 갱신된다.

```js
const arr = [0];

// 배열 요소의 추가
arr[1] = 1;

console.log(arr); // [0, 1]
console.log(arr.length); // 2
```

> 만약 현재 배열의 length 프로퍼티 값보다 큰 인덱스로 새로운 요소를 추가하면 희소 배열이 된다.

```js
arr[100] = 100;

console.log(arr); // [0, 1, empty × 98, 100]
console.log(arr.length); // 101
```

> 이때 인덱스로 요소에 접근하여 명시적으로 값을 할당하지 않은 요소는 생성되지 않는 것에 주의 하자.

```js
// 명시적으로 값을 할당하지 않은 요소는 생성되지 않는다.
console.log(Object.getOwnPropertyDescriptors(arr));
/*
{
  '0': {value: 0, writable: true, enumerable: true, configurable: true},
  '1': {value: 1, writable: true, enumerable: true, configurable: true},
  '100': {value: 100, writable: true, enumerable: true, configurable: true},
  length: {value: 101, writable: true, enumerable: false, configurable: false}
*/
```

> 이미 요소가 존재하는 요소에 값을 재할당하면 요소값이 갱신된다.

```js
// 요소값의 갱신
arr[1] = 10;

console.log(arr); // [0, 10, empty × 98, 100]
```

> 인덱스는 요소의 위치를 나타내므로 반드시 0이상의 정수(또는 정수 형태의 문자열)을 사용해야 한다.  
> 만약 정수 이외의 값을 인덱스처럼 사용하면 요소가 생서되는 것이 아니라 프로퍼티가 생성된다.
> 이때 추가된 프로퍼티는 length프로퍼티 값에 영향을 주지 않는다.

```js
const arr = [];

// 배열 요소의 추가
arr[0] = 1;
arr["1"] = 2;

// 프로퍼티 추가
arr["foo"] = 3;
arr.bar = 4;
arr[1.1] = 5;
arr[-1] = 6;

console.log(arr); // [1, 2, foo: 3, bar: 4, '1.1': 5, '-1': 6]

// 프로퍼티는 length에 영향을 주지 않는다.
console.log(arr.length); // 2
```

<br>

---

## 7. 배열 요소의 삭제

배열은 사실 객체이기 때문에 배열의 특정 요소를 삭제하기 위해 delete 연산자를 사용할 수 있다.

```js
const arr = [1, 2, 3];

// 배열 요소의 삭제
delete arr[1];
console.log(arr); // [1, empty, 3]

// length 프로퍼티에 영향을 주지 않는다. 즉, 희소 배열이 된다.
console.log(arr.length); // 3
```

delete 연산자는 객체의 프로퍼티를 삭제한다.  
따라서 위 예제의 delete arr[1]은 arr에서 프로퍼티 키가 '1'인 프로퍼티를 삭제한다.  
이때 배열은 희소 배열이 되며 length 프로퍼티 값은 변하지 않는다.  
따라서 희소 배열을 만드는 delete 연산자는 사용하지 않는 것이 좋다.

희소 배열을 만들지 않으면서 배열의 특정 요소를 완전히 삭제하려면 Array.prototype.splice 메서드를 사용한다.

```js
const arr = [1, 2, 3];

// Array.prototype.splice(삭제를 시작할 인덱스, 삭제할 요소 수)
// arr[1]부터 1개의 요소를 제거
arr.splice(1, 1);
console.log(arr); // [1, 3]

// length 프로퍼티가 자동 갱신된다.
console.log(arr.length); // 2
```
