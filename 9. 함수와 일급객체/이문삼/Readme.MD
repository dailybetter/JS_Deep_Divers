# 18 함수와 일급 객체

## 18.1 일급 객체

다음과 같은 조건을 만족하는 객체를 일급 객체라 한다.

1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
2. 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

```js
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수의 매개변수에 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 의미다. 객체는 값이므로 함수는 값과 동일하게 취급할 수 있다.  
따라서 함수는 값을 사용할 수 있는 곳(변수 할당문, 객체의 프로퍼티 값, 배열의 요소, 함수 호출의 인수, 함수 반환문)이라면 어디서든지 리터럴로 정의할 수 있으며 런타임에 함수 객체로 평가된다.

일급 객체로서 함수가 가지는 가장 큰 특징은 일반 객체와 같이 함수의 매개변수에 전달할 수 있으며, 함수의 반환값으로 사용할 수도 있다는 것이다.

## 18.2 함수 객체의 프로퍼티

함수는 객체다. 따라서 함수도 프로퍼티를 가질 수 있다.

```js
function square(number); {
  return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
  length: {value: 1, writable: false, enumerable: false, configurable: true}
  name: {value: 'square', writable: false, enumerable: false, configurable: true}
  arguments: {value:null, writable: false, enumerable: false, configurable: false}
  caller: {value:null, writable: false, enumerable: false, configurable: false}
  prototype: {value: {...}, writable: true, enumerable: false, configurable: false}
}
*/
```

여기서 `__proto__`은 `Object.prototype` 객체의 접근자 프로퍼티다.  
이것은 함수 고유의 프로퍼티가 아니며 상속받았기 때문에 사용할 수 있는 것이다.

### 18.2.1 arguments 프로퍼티

함수 객체의 `arguments` 프로퍼티 값은 `arguments` 객체다. `arguments` 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한(iterable) 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용된다.

함수를 정의할 때 선언한 매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다. 즉, 함수가 호출되면 함수 몸체 내에서 암묵적으로 매개변수가 선언되고 `undefined`로 초기화된 이후 인수가 할당된다.

따라서 선언된 매개변수보다 적은 인수를 전달하면 나머지는 `undefined`로 초기화된 상태를 유지하고, 초과된 인수는 무시된다.  
여기서 무시는 사라지는 것이 아닌 암묵적으로 `arguments` 객체의 프로퍼티로 보관된다.

> ### arguments 객체의 Symbol(Symbol.iterator) 프로퍼티
>
> arguments 객체의 Symbol(Symbol.iterator) 프로퍼티는 arguments 객체를 순회 가능한 자료구조인 이터러블로 만들기 위한 프로퍼티다.
>
> ```js
> function multiply(x, y) {
>   const iterator = arguments[Symbol.iterator]();
>   console.log(iterator.next()); // {value: 1, done: false}
>   console.log(iterator.next()); // {value: 2, done: false}
>   console.log(iterator.next()); // {value: 3, done: false}
>   console.log(iterator.next()); // {value: undefined, done: true}
>
>   return x * y;
> }
>
> multiply(1, 2, 3);
> ```

함수가 호출되면 인수 개수를 확인하고 이에 따라 함수의 동작을 달리 정의할 필요가 있을 때 유용하게 사용되는 것이 `arguments` 객체다.

`arguments` 객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다.

```js
function sum() {
  let res = 0;

  for (let i = 0; i < arguments.length; i++) {
    res += arguments[i];
  }

  return res;
}

console.log(sum()); // 0
console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3)); // 6
```

`arguments` 객체는 배열 형태로 인자 정보를 담고 있지만 실제 배열이 아닌 유사 배열 객체다.  
여기서 유사 배열 객체란 `length` 프로퍼니를 가진 객체로 `for`문으로 순회할 수 있는 객체를 말한다.

> ES6의 Rest 파라미터 도입으로 `arguments`의 중요성이 전보다 낮지만 매번 사용할 수 있는 것이 아니기 때문에 알아두자

### 18.2.2 caller 프로퍼티

`caller`프로퍼티는 ECMAScript 사양에 포함되지 않은 비표준 프로퍼티이다.

### 18.2.3 length 프로퍼티

함수 객체의 `length` 프로퍼티는 함수를 정의할 때 선언한 매개변수의 개수를 가리킨다.

```js
function foo() {}
console.log(foo.length); // 0

function bar(x, y) {
  return x + y;
}
console.log(bar.length); // 2
```

여기서는 `arguments` 객체의 `length` 프로퍼티와 함수 객체의 `length` 프로퍼티의 값은 다를 수 있다는 점을 주의해야한다.  
이유는 `arguments` 객체의 `length`는 인자의 개수고 함수 객체의 `length` 프로퍼티는 매개변수의 개수를 가리키기 때문이다.

### 18.2.4 name 프로퍼티

함수 객체의 `name` 프로퍼티는 함수 이름을 나타낸다.  
주의점은 ES5와 ES6에서의 동작이 다르다는 점.

```js
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

function bar() {}
console.log(bar.name); // bar
```

### 18.2.5 \_\_proto\_\_ 접근자 프로퍼티

모든 객체는 [[Prototype]]이라는 내부 슬롯을 갖는다. [[Prototype]] 내부 슬롯은 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체를 가리킨다.

\_\_proto\_\_ 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티다.

```js
const obj = { a: 1 };

console.log(obj.__proto__ === Object.prototype); // true

console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false, __proto__가 obj의 고유 프로퍼티 키가 아니기 때문
```

### 18.2.6 prototype 프로퍼티

`prototype` 프로퍼티는 생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor만이 소유하는 프로퍼티다.  
다시말해 non-constructor에는 `prototype`프로퍼티가 없다.

```js
(function() {}).hasOwnProperty('prototype'); // true
{}.hasOwnProperty('prototype'); // false, 일반 객체는 prototype 프로퍼티를 소유하지 않음
```

`prototype` 프로퍼티는 함수가 객체를 생성하는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다.
