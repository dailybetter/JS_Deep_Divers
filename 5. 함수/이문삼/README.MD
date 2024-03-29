# 12 함수

## 12.1 함수란?

- 일련의 과정을 문으로 구현하고 코드 블록으로 감싸서 하나의 실행 단위로 정의한 것

함수를 정의한 후 호출하여 사용하면 된다.

```js
// 함수 정의
// 이름: add
// 매개변수(parameter): x, y
// 반환값: x + y
function add(x, y) {
  return x + y;
}

// 함수 호출
// 인수: 1, 2
var result = add(1, 2);
console.log(result); // 3
```

## 12.2 함수를 사용하는 이유

함수는 필요할 때 여러 번 호출할 수 있다. 즉, 실행 시점을 개발자가 결정할 수 있고 몇 번이든 재사용이 가능하다.  
그리고 같은 코드를 중복해서 여러 번 작성하면 그 코드를 수정해야 할 때 중복된 횟수만큼 코드를 수정해야한다. 따라서 코드 수정에 걸리는 시간이 증가한다.

이처럼 코드의 중복을 억제하고 재사용성을 높이는 함수는 유지보수의 편의성을 높이고 실수를 줄여 코드의 신뢰성을 높이는 효과가 있다.

**함수는 객체 타입의 값이다. 따라서 이름(식별자)을 붙일 수 있다.**  
따라서 적절한 함수 이름은 코드의 가독성을 향상시킨다.

## 12.3 함수 리터럴

<table>
  <tr>
    <th>구성 요소</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>함수 이름</td>
    <td>
      <li>함수 이름은 식별자다. 따라서 식별자 네이밍 규칙을 준수해야 한다.</li>
      <li>함수 이름은 함수 몸체 내에서만 참조할 수 있는 식별자다.</li>
      <li>함수 이름은 생략할 수 있다.(기명 함수, 무명/익명 함수)</li>
    </td>
  </tr>
  <tr>
    <td>매개변수 목록</td>
    <td>
      <li>0개 이상의 매개변수를 소괄호로 감싸고 쉼표로 구분한다.</li>
      <li>각 매개변수에는 함수를 호출할 때 지정한 인수가 <strong>순서</strong>대로 할당된다.</li>
      <li>매개변수는 함수 몸체 내에서 변수와 동일하게 취금된다. 따라서 식별자 네이밍 규칙을 지켜야한다.</li>
    </td>
  </tr>
  <tr>
    <td>함수 몸체</td>
    <td>
      <li>함수가 호출되었을 때 일괄적으로 실행될 문들을 하나의 실행 단위로 정의한 코드 블록이다.</li>
      <li>함수 몸체는 함수 호출에 의해 실행된다.</li>
    </td>
  </tr>
</table>

함수를 변수에 할당할 수 있으며 함수 리터럴도 평가되어 값을 생성한다. 즉, 함수는 객체다.  
다만 일반 객체와 달리 호출이 가능하다. 그리고 함수 객체만의 고유한 프로퍼티를 갖는다.

## 12.4 함수 정의

### 12.4.1 함수 선언문

```js
function add(x, y) {
  return x + y;
}

console.dir(add); // f add(x, y)
console.log(add(2, 5)); // 7
```

함수 선언문은 함수 리터럴과 형태가 동일하다. 단, 함수 리터럴은 함수 이름을 생략할 수 있으나 함수 선언문은 함수 이름을 생략할 수 없다.

함수 선언문은 표현식이 아닌 문이다.(개발자 도구의 콘솔에서 함수 선언문을 실행하면 완료 값 대신 `undefined` 출력)  
따라서 변수에 할당할 수 없는데 다음 예제를 실행하보면 함수 선언문이 변수에 할당되는 것처럼 보인다.

```js
var add = function add(x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

이렇게 동작하는 이유는 엔진이 코드의 문맥에 따라 동일한 함수 리터럴을 표현식이 아닌 문인 함수 선언문으로 해석하는 경우와 표현식인 문인 함수 리터럴 표현식으로 해석하는 경우가 있기 때문이다.

- 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석
- 변수에 할당하거나 피연산자로 사용하면 함수 리터럴 표현식으로 해석

```js
function foo() {
  console.log("foo");
}
foo(); // foo

(function bar() {
  console.log("bar");
});
bar(); // ReferenceError: bar is not defined
```

`bar`를 호출할 수 없는 이유는 함수 이름은 함수 몸체 내에서만 참조할 수 있는 식별자다. 따라서 함수 몸체 외부에서는 함수 이름으로 함수를 참조할 수 없으므로 이름으로 호출할 수 없는 것이다.

그러면 `foo`도 마찬가지로 호출할 수 없어야 하지만 JS엔진이 암묵적으로 식별자를 해당 이름으로 생성했기 때문에 호출할 수 있는 것이다.

엔진은 함수 선언문을 해석해 함수 객체를 생성한다. 이때 함수 이름은 함수 몸체 내부에서만 유효한 식별자이므로 함수 이름과는 별도로 생성된 함수 객체를 가리키는 식별자가 필요하다.  
따라서 엔진이 생성된 함수를 호출하기 위해 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고, 거기에 함수 객체를 할당한다.

**따라서 함수는 함수이름으로 호출하는 것이 아니라 함수 객체를 가리키는 식별자로 호출한다.**  
다른 말로 함수 선언문을 함수 표현식으로 변환해 함수 객체를 생성한다고 생각할 수 있다.

```js
var add = function add(x, y) {
  return x + y;
};
```

### 12.4.2 함수 표현식

자바스크립트의 함수는 값처럼 변수에 할당할 수도 있고 프로퍼티 값이 될 수도 있으며 배열의 요소가 될 수도 있다.  
이를 **일급 객체**라고 한다. 함수가 일급 객체라는 것은 함수를 값처럼 자유롭게 사용할 수 있다는 의미이다.

함수는 일급 객체이므로 함수 리터럴로 생성한 함수 객체를 변수에 할당할 수 있다. 이러한 함수 정의 방식을 함수 표현식<sup>function expression</sup>이라 한다.

```js
// 함수 리터럴, 익명 함수
var add = function (x, y) {
  return x + y;
};
```

함수 선언문과 함수 표현식이 정확히 동일하게 동작하지는 않는다.  
함수 선언문은 표현식이 아닌 문이고 함수 표현식은 표현식인 문이다.

### 12.4.3 함수 생성 시점과 함수 호이스팅

```js
console.dir(add); // f add(x, y)
console.dir(sub); // undefined

console.log(add(2, 5)); // 7
console.log(sub(2, 5)); // TypeError: sub is not a function

function add(x, y) {
  return x + y;
}

var sub = function (x, y) {
  return x - y;
};
```

함수 선언문으로 정의한 함수는 함수 선언문 이전에 호출할 수 있다. 그러나 표현식으로 정의한 함수는 표현식 이전에 호출할 수 없다.  
이유는 둘의 방식으로 정의한 함수의 생성 시점이 다르기 때문이다.

이는 함수 선언문으로 정의한 함수는 런타임 이전에 엔진에 의해 먼저 실행되기 때문에 동일한 이름의 식별자를 암묵적으로 생성하고 생성된 함수 객체를 할당하게되는 것이다.

코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징을 함수 호이스팅<sup> function hoisting</sup>이라 한다.

함수 표현식으로 정의하면 변수 호이스팅이 발생하기 때문에 사용할 수 없는 것이다.

> 변수 호이스팅과 다른 점은 변수는 `undefined`로 초기화 되지만 함수는 함수 객체로 초기화된다.  
> 따라서 선언문 이전에 호출해도 사용할 수 있다.

### 12.4.4 Function 생성자 함수

```js
var add = new Function("x", "y", "return x + y");
console.log(add(2, 5)); // 7
```

`Function`생성자 함수로 함수를 생성하는 방식은 일반적이지 않고 바람직하지도 않다.  
클로저를 생성하지 않는 등, 선언문이나 표현식으로 생성한 함수와 다르게 동작하기 때문이다.

### 12.4.5 화살표 함수

ES6에서 도임된 화살표 함수<sup>arrow function</sup>은 `function` 키워드 대신 화살표를 사용해 좀 더 간략한 방법으로 함수를 선언할 수 있다.  
화살표 함수는 항상 익명 함수로 정의한다.

```js
const add = (x, y) => x + y;
console.log(add(2, 5)); // 7
```

화살표 함수는 기전의 선언문 또는 표현식을 완전히 대체하기 위해 디자인 된 것은 아니다.  
이것은 생성자 함수로 사용할 수 없으며, 기존 함수와 `this` 바인딩 방식이 다르고 `prototype` 프로퍼티가 없으며 `arguments` 객체를 생성하지 않는다.

## 12.5 함수 호출

함수를 호출하게 되면 현재의 실행 흐름을 중단하고 호출된 함수로 실행 흐름을 옮긴다.  
이때 매개변수에 인수가 순서대로 할당되고 함수 몸체의 문들이 실행되기 시작한다.

### 12.5.1 매개변수와 인수

함수를 실행하기 위해 필요한 값을 함수 외부에서 함수 내부로 전달할 필요가 있는 경우, 매개변수를 통해 인수를 전달한다. 이때 인수는 값으로 평가될 수 있는 표현식이어야한다.  
인수는 함수를 호출할 때 지정하며 개수와 타입에 제한이 없다.

```js
// 매개변수: x, y
function add(x, y) {
  return x + y;
}

// 인수: 2, 5
var result = add(2, 5);
```

매개변수는 함수를 정의할 때 선언하며, 함수 몸체 내부에서 변수와 동일하게 취급된다. 즉, 함수가 호출되면 함수 몸체 내에서 암묵적으로 매개변수가 생성되고 `undefined`로 초기화된다. 이후 인수가 순서대로 할당된다.

이때 매개변수의 스코프는 함수 내부기 때문에 외부에선 참조할 수 없다.

함수는 매개변수의 개수와 인수의 개수가 일치하는지 체크하지 않는다. 이 경우 에러가 발생하지 않으며 인수가 할당되지 않은 매개변수의 값은 `undefined`다.

반대로 매개변수보다 인수가 더 많은 경우 초과된 인수는 무시된다.

> 이때 초과된 인수가 버려지는 것은 아니고 암묵적으로 `arguments` 객체의 프로퍼티로 보관된다.

```js
console.log(add(2)); // NaN
console.log(add(2, 5, 10)); // 7

function sub(x, y) {
  console.log(arguments); // Arguments(3) [2, 5, 10, callee: f, Symbol(Symbol.iterator): f]

  return x - y;
}

sub(2, 5, 10);
```

### 12.5.2 인수 확인

```js
function add(x, y) {
  return x + y;
}

console.log(add(2, 5)); // 7
console.log(add("x", "y")); // 'xy'
```

만약 개발자의 의도가 숫자 타입의 인수를 전달받아 합계를 반환하려했다면 두번째 호출은 의도치 않은 버그가 발생할 수도 있다.  
하지만 코드 문법상 어떠한 문제도 없으므로 실행되는 것이다.

> 이유
>
> 1. 자바스크립트 함수는 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.
> 2. 자바스크립트는 동적 타입 언어다. 따라서 매개변수의 타입을 사전에 지정할 수 없다.

```js
function add(x, y) {
  if (typeof x !== "number" || typeof y !== "number")
    throw new TypeError("인수는 모두 숫자 값이어야 합니다.");

  return x + y;
}

console.log(add(2)); // TypeError: 인수는 모두 숫자 값이어야 합니다.
console.log(add("a", "b")); // TypeError: 인수는 모두 숫자 값이어야 합니다.
```

하지만 함수 내부에서 전달된 인수를 확인하더라도 부적절한 호출을 사전에 방지할 수는 없고 에러는 런타임에 발생하게 된다.

따라서

1. 타입스크립트와 같은 정적 타입을 선언할 수 있는 언어를 사용하거나
2. `arguments` 객체를 사용하거나
3. 단축 평가나 매개변수 기본값(ES6)

같은 방법을 사용해 체크할 수 있다.

### 12.5.3 매개변수의 최대 개수

ECMAScript 사양에서는 매개변수의 최대 개수에 대해 명시적으로 제한하지 않았지만 물리적 한계는 존재하므로 엔진마다 제한이 있을 수 있다.

매개변수는 순서에 의미가 있다. 유지보수성, 가독성 등을 고려해야한다.  
그리고 함수는 한 가지 일만 해야 하며 가급적 작게 만들어야 한다.

따라서 매개변수는 최대 3개 이상을 넘지 않는 것을 권장한다.  
그 이상이 필요하다면 객체를 인수로 전달하는 것을 추천

```js
$.ajax({
  method: "POST",
  url: "/user",
  data: { id: 1, name: "Park" },
  cache: false,
});
```

다만 객체를 사용할 경우 내부에서 매개변수를 통해 객체를 변경하면 외부 객체도 변경되는 부수 효과<sup>side effect</sup>가 발생한다는 것이다.

### 12.5.4 반환문

함수는 `return` 키워드와 표현식(반환값)으로 이뤄진 반환문을 사용해 실행 결과를 함수 외부로 반환할 수 있다.

```js
function foo() {
  return "foo";
}

var result = foo(); // foo
```

이렇든 `return` 키워드를 사용해 자바스크립트에서 사용 가능한 모든 값을 반환할 수 있다.  
함수 호출은 표현식인 것이다.

반환문의 역할

1. 함수의 실행을 중단하고 함수 몸체를 빠져나감
2. `return` 키워드 뒤에 오는 표현식을 평가해 반환한다. 명시적으로 지정하지 않으면 `undefined`가 반환된다.

그리고 반환문은 생략할 수 있고 키워드와 표현식 사이에 줄바꿈이 있으면 세미콜론 자동 삽입 기능에 의해 추가되기 때문에 주의.

## 12.6 참조에 의한 전달과 외부 상태의 변경

```js
function changeVal(primitive, obj) {
  primitive += 100;
  obj.name = "Park";
}

var num = 100;
var person = { name: "Lee" };
changeVal(num, person);

console.log(num); // 100
console.log(person); // { name: 'Park' }
```

`primitive`의 경우 원시 값을 직접 변경할 수 없기 때문에 재할당을 통해 원시 값을 새로운 원시 값으로 교체했고, `obj`는 재할당 없이 직접 할당된 객체를 변경한 것이다.

이때 원시 타입의 인수는 값 자체가 복사되어 매개변수에 전달되기 때문에 함수 몸체에서 그 값을 변경해도 원본은 훼손되지 않는다.  
하지만 객체 타임의 인수는 참조 값이 복사되어 전달되기 때문에 원본이 훼손된다.

이 또한 코드 복잡성을 증가시키고 가독성을 해치는 원인이 된다.

이러한 문제의 해결 방법 중 하나는 객체를 불변 객체로 만들어 사용하거나 객체를 깊은 복사하여 새로운 객체를 생성하고 변경하는 것이다.

> 외부 상태를 변경하지 않고 의존하지도 않는 함수를 순수 함수라 한다.  
> 이를 통해 부수 효과를 억제하며 오류를 피하고 프로그램의 안정성을 높이려는 패러다임을 함수형 프로그래밍이라 한다.

## 12.7 다양한 함수의 형태

### 12.7.1 즉시 실행 함수

함수의 정의와 동시에 즉시 호출되는 함수를 즉시 실행 함수<sup>Immediatly Invoked Function Expression</sup>라고 한다. 이 함수는 단 한 번만 호출되며 다시 호출할 수 없다.

```js
(function () {
  var a = 3;
  var b = 5;
  return a * b;
})();
```

반드시 그룹 연산자`(...)`로 감싸야 함수 리터럴로 평가되기 때문에(그렇지 않으면 함수 선언문이 됨) 감싸야 한다.

그리고 인자도 전달할 수 있다.

```js
var res = (function (a, b) {
  return a * b;
})(3, 5);

console.log(res); // 15
```

### 12.7.2 재귀 함수

함수가 자기 자신을 호출하는 것을 재귀 호출이라 한다. 재귀 함수는 자기 자신을 호출하는 행위, 즉 재귀 호출을 수행하는 함수를 말한다.

```js
function factorial(n) {
  if (n <= 1) return 1;

  return n * factorial(n - 1);
}

console.log(factorial(0)); // 1
console.log(factorial(3)); // 6
console.log(factorial(5)); // 120
```

대부분의 재귀 함수는 `for` 문이나 `while` 문으로 구현 가능하며 탈출 조건이 없다면 에러가 발생한다.

따라서 재귀 함수는 반복문을 사용하는 것보다 재귀 함수를 사용하는 편이 더 직관적으로 이해하기 쉬울 때만 한정적으로 사용하는 것이 바람직하다.

### 12.7.3 중첩 함수

함수 내부에 정의된 함수를 중첩 함수 또는 내부 함수라 한다. 그리고 중첩 함수를 포함하는 함수는 외부 함수라 부른다.  
일반적으로 중첩 함수는 자신을 포함하는 외부 함수를 돕는 헬퍼 함수의 역할을 한다.

```js
function outer() {
  var x = 1;

  function inner() {
    var y = 2;

    console.log(x + y); // 3
  }

  inner();
}

outer();
```

### 12.7.4 콜백 함수

```js
function repeat(n) {
  for (var i = 0; i < n; i++) console.log(i);
}

repeat(5);
```

여기서 `repeat`함수는 `console.log(i)`에 강하게 의존하고 있어 다른 일을 할 수 없다.
이때 콜백 함수를 사용할 수 있다.

```js
function repeat(n, f) {
  for (var i = 0; i < n; i++) {
    f(i);
  }
}

var logAll = function (i) {
  console.log(i);
};

var logOdds = function (i) {
  if (i % 2) console.log(i);
};

repeat(5, logAll); // 0 1 2 3 4
repeat(5, logOdds); // 1 3
```

`repeat` 함수는 경우에 따라 변경되는 일을 함수 `f`로 추상화했고 이를 외부에서 전달받는다. 이는 함수가 일급 객체이기 때문에 가능한 것이다.

이처럼 함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수를 콜백 함수<sup>callback function</sup>라고 하며, 매개변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수를 고차 함수<sup>Higher-Order Function, HOF</sup>라고 한다.

여기서 고차 함수는 콜백 함수를 자신의 일부분으로 합성한다.  
콜백 함수는 고차 함수에 의해 호출되며 이때 고차 함수는 필요에 따라 콜백 함수에 인수를 전달할 수 있다.

콜백 함수는 함수형 프로그래밍 패러다임뿐만 아니라 비동기 처리(이벤트 처리, Ajax 통신, 타이머 함수 등)에 활용되는 중요한 패턴이다.

### 12.7.5 순수 함수와 비순수 함수

함수형 프로그래밍에서는 어떤 외부 상태에 의존하지도 않고 변경하지도 않는, 즉 부수 효과가 없는 함수를 순수 함수라고 하고, 외부 상타에 의존하거나 외부 상태를 변경하는, 즉 부수 효과가 있는 함수를 비순수 함수라고 한다.

순수 함수는 어떤 외부 상태에도 의존하지 않고 오직 매개변수를 통해 함수 내부로 전달된 인수에게만 의존해 값을 생성해 반환한다.  
외부 상태에 의존하는함수는 외부 상태에 따라 반환값이 달라진다.

결국 함수가 외부 상태를 변경하면 상태 변화를 추적하기 어려워진다. 따라서 함수 외부 상태의 변경을 지양하는 순수 함수를 사용하는 것이 좋다.
