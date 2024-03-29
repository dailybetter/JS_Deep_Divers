# 14 전역 변수의 문제점

## 14.1 변수의 생명 주기

### 14.1.1 지역 변수의 생명 주기

이전에 공부했던 변수 선언은 선언문이 어디에 있든 상관없이 가장 먼저 실행된다는 것은 전역 변수에 해당되는 말이다. 런타임 이전에 엔진에 의해 먼저 선언이 되는 것을 말한다.

```js
var x = "global";

function foo() {
  console.log(x); // undefined

  var x = "local";
  console.log(x); // local

  return x;
}

foo();
console.log(x); // global
```

함수 내부에서 선언된 지역 변수는 함수가 호출되면 생성되고 함수가 종료하면 소멸한다.  
그리고 변수 선언이 일어나는 타이밍은 함수가 호출된 후 몸체의 코드가 순차적으로 실행되기 전에 일어나게 된다.

이후 변수 할당문이 실행되면 `x`의 값이 재할당되고 함수가 종료하면 `x`도 소멸되어 생명 주기가 종료된다.

따라서 지역 변수의 생명 주기는 함수의 생명 주기와 일치한다.

그리고 호이스팅은 스코프를 단위로 동작한다. 따라서 첫 번째 `console.log()`가 `undefined`인 이유는 지역 변수 `x`가 호이스팅되어 `undefined`로 초기화 되었기 때문이다.  
이처럼 지역 변수의 호이스팅은 지역 스코프의 선두로 끌어 올려진 것처럼 동작한다.

### 14.1.2 전역 변수의 생명 주기

함수와 달리 전역 코드는 명시적인 호출 없이 실행된다. 다시 말해, 전역 코드는 함수 호출과 같이 전역 코드를 실행하는 특별한 진입점이 없고 코드가 로드되자마자 곧바로 해석되고 실행된다.

브라우저 환경에서 전역 객체는 `window`이므로 브라우저 환경에서 `var`키워드로 선언한 전역 변수는 전역 객체 `window`의 프로퍼티다.  
그리고 `window`는 웹페이지를 닫기 전까지 유효하기 때문에 `var`키워드로 선언한 변수도 마찬가지다.

> ### 전역 객체
>
> 전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체다.  
> 표준 빌트인 객체(Object, String, Number, Array, Function, ...)와 환경에 따른 호스트 객체(Web API, Node.js API), 그리고 var 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖는다.

## 14.2 전역 변수의 문제점

- 암묵적 결합

전역 변수를 선언한 의도는 전역, 즉 코드 어디서든 탐조하고 할당할 수 있는 변수를 사용하겠다는 것이다.  
이는 모든 코드가 전역 변수를 참조하고 변경할 수 있는 암묵적 결합을 허용하는 것이다.

- 긴 생명 주기

전역 변수는 생명주기가 길다. 따라서 메모리 리소스도 오랜 기간 소비한다. 또한 전역 변수의 상태를 변경할 수 있는 시간도 길고 기회도 많다.

더욱이 `var`키워드는 변수의 중복 선언을 허용하므로 생명 주기가 길 수록 재할당 위험이 높다.

- 스코프 체인 상에서 종점에 존대

전역 변수는 스코프 체인 상에서 종점에 존재한다. 즉, 전역 변수의 검색 속도가 가장 느리다.

- 네임스페이스 오염

JS의 문제점 중 하나는 파일이 분리되어 있다 해도 하나의 전역 스코프를 공유한다는 것이다. 따라서 다른 파일 내에서 동일한 이름으로 명명된 전역 변수나 함수가 같은 스코프 내에 존재하는 경우 예상치 못한 결과를 가져올 수 있다.

## 14.3 전역 변수의 사용을 억제하는 방법

전역 변수를 반드시 사용해야 할 이유를 찾지 못한다면 지역 변수를 사용해야 한다.  
변수의 스코프는 좁을 수록 좋기 때문이다.

아래는 전역 변수의 사용을 억제할 수 있는 몇 가지 방법이다.

### 14.3.1 즉시 실행 함수

```js
(function () {
  var foo = 100;
  // ...
})();

console.log(foo); // ReferenceError: foo is not defined
```

모든 코드를 즉시 실행 함수로 감싸면 모든 변수는 즉시 실행 함수의 지역 변수가 된다.  
이 방법을 사용하면 전역 변수를 생성하지 않으므로 라이브러리 등에 자주 사용된다.

### 14.3.2 네임스페이스 객체

전역에 네임스페이스 역할을 담당할 객체를 생성하고 전역 변수처럼 사용하고 싶은 변수를 프로퍼티로 추가하는 방법이다.

```js
var MYAPP = {};

MYAPP.name = "Park";

console.log(MYAPP.name); // Park
```

### 14.3.3 모듈 패턴

모듈 패턴은 클래스를 모방해서 관련이 있는 변수와 함수를 모아 즉시 실행 함수로 감싸 하나의 모듈을 만든다. 이는 클로저를 기반으로 동작한다.  
이 패턴의 특징은 전역 변수의 억제는 물론 캡슐화까지 구현할 수 있다는 것이다.

> 캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다.  
> 객체의 특정 프로퍼티나 메소드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라 한다.

```js
var Counter = (function () {
  var num = 0;

  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

console.log(Counter.num); // undefined
console.log(Counter.increase()); // 1
console.log(Counter.dncrease()); // 0
```

이때 외부에 노출하고 싶은 변수나 함수를 담아 반환하게 되고 반환되는 객체의 프로퍼티를 퍼블릭 멤버, 반환 값에 추가되지 않은 변수나 함수는 외부에서 접근할 수 없는 프라이빗 멤버라고 한다.

### 14.3.4 ES6 모듈

ES6 모듈을 사용하면 더는 전역 변수를 사용할 수 없는데 이는 모듈이 파일 자체의 독자적인 모듈 스코프를 제공하기 때문이다.  
따라서 `var`키워드로 선언했어도 전역 변수가 아니며 `window`객체의 프로퍼티도 아니다.

`type="module"`을 추가하면 사용할 수 있다. 이때 확장자는 `mjs`를 권장한다.

```html
<script type="module" src="lib.mjs"></script>
<script type="module" src="app.mjs"></script>
```

---

# 15 let, const 키워드와 블록 레벨 스코프

## 15.1 var 키워드로 선언한 변수의 문제점

### 15.1.1 변수 중복 선언 허용

```js
var x = 1;
var y = 1;

var x = 100;
var y; // ignore

console.log(x); // 100
console.log(y); // 1
```

위 예제의 `var`키워드로 선언한 `x`변수와 `y`변수는 중복 선언되었다.  
이처럼 초기화 문이 있는 변수 선언문은 엔진에 의해 `var`키워드가 없는 것처럼 동작하고 초기화 문이 없는 변수 선언문은 무시되게 된다.

이 과정에서 동일한 이름의 변수가 이미 선언되어 있는 것을 모르고 중복 선언하면서 값까지 할당했다면 의도치 않게 먼저 선언된 변수 값이 변경되는 부작용이 발생하게 된다.

### 15.1.2 함수 레벨 스코프

`var`키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정한다. 따라서 함수 외부에서 `var`키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 된다.

```js
var x = 1;

if (true) {
  var x = 10;
}

console.log(x); // 10

var i = 10;

for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

console.log(i); // 5
```

함수 레벨 스코프는 전역 변수를 남발할 가능성을 높인다. 이로 인해 의도치 않게 전역 변수가 중복 선언되는 경우가 발생한다.

### 15.1.3 변수 호이스팅

변수 호이스팅에 의해 `var`키워드로 선언한 변수는 변수 선언문 이전에 참조할 수 있다. 단, 할당문 이전에 변수를 참조하면 언제나 `undefined`를 반환한다.

```js
console.log(foo); // undefined

foo = 123; // 재할당

console.log(foo); // 123

var foo; // 선언
```

변수 선언문 이전에 변수를 참조하는 것은 변수 호이스팅에 의해 에러를 발생시키지는 않지만 프로그램의 흐름상 맞지 않을뿐더러 가독성을 떨어뜨리고 오류를 발생시킬 여지를 남긴다.

## 15.2 let 키워드

### 15.2.1 변수 중복 선언 금지

`let`키워드로 이름이 같은 변수를 중복 선언하면 문법에러<sup>SyntaxError</sup>가 발생한다.

```js
var foo = 123;
var foo = 456;

let bar = 123;
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 15.2.2 블록 레벨 스코프

`let`키워드로 선언한 변수는 모든 코드 블록(함수, if문, for문, while문, try/catch문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```js
let foo = 1;

{
  let foo = 2;
  let bar = 3;
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

전역에서 선언된 `foo`변수와 코드 블록 내에서 선언된 `foo`변수는 다른 별개의 변수다. 또한 `bar`변수도 블록 레벨 스코프를 갖는 지역 변수다.  
따라서 전역에서는 `bar`변수를 참조할 수 없다.

### 15.2.3 변수 호이스팅

`let`키워드로 선언한 변수는 변수 호이스팅이 **발생하지 않는 것처럼** 동작한다.

우선 `let`키워드로 선언한 변수는 "선언 단계"와 "초기화 단계"가 분리되어 진행된다.  
런타임 이전에 엔진에 의해 암묵적으로 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행된다.

그럼에도 `undefined`가 아닌 `ReferenceError`가 발생하는 이유는 일시적 사각지대<sup>Temporal Dead Zone: TDZ</sup>라고 부르는 것 때문이다.

> 일시적 사각지대(TDZ): 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간

```js
console.log(foo); // ReferenceError

let foo;
console.log(foo); // undefined

foo = 1;
console.log(foo); // 1
```

이 때문에 `let`키워드로 선언한 변수는 호이스팅이 발생하지 않는 것처럼 보이지만 그렇지 않다.

```js
let foo = 1;

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2;
}
```

`let`키워드로 선언한 변수의 경우 변수 호이스팅이 발생하지 않는다면 위 예제는 전역 변수 `foo`의 값을 출력해야 한다.  
하지만 `let`키워드로 선언한 변수도 여전히 호이스팅이 발생하기 때문에 참조 에러가 발생하는 것이다.

JS는 ES6에서 도입된 `let`, `const`를 포함해서 모든 선언(`var`, `let`, `const`, `function`, `function*`, `class` 등)을 호이스팅한다. 다만 `let`, `const`, `class`를 사용한 선언문은 호이스팅이 발생하지 않는 것처럼 동작한다.

### 15.2.4 전역 객체와 let

```js
var x = 1;
y = 2;
function foo() {}

console.log(window.x); // 1
console.log(x); // 1

console.log(y); // 2

console.log(foo); // f foo() {}
```

`var`키워드로 선언한 전역 변수와 전역 함수, 그리고 선언하지 않은 변수에 값을 할당한 암묵적 전역은 전역 객체 window(브라우저)의 프로퍼티가 된다.

`let`키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. `let` 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 된다.

## 15.3 const 키워드

`const`키워드는 상수를 선언하기 위해 사용한다.  
`let`키워드와 대부분 동일하므로 아래는 다른 점 위주다.

### 15.3.1 선언과 초기화

`const`키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 한다.

```js
const foo; // SyntaxError: Missing initializer in const declaration
```

`let`과 마찬가지로 호이스팅이 발생하지 않는 것처럼 동작하고 블록 레벨 스코프를 가진다.

### 15.3.2 재할당 금지

`const`키워드로 선언한 변수는 재할당이 금지된다.

```js
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable
```

### 15.3.3 상수

`const`키워드로 선언한 변수에 원시 값을 할당한 경우 변수 값을 변경할 수 없다.  
또 변수의 상대 개념인 상수는 재할당이 금지된 변수를 발한다. 상수도 값을 저장하기 위한 메모리 공간이 필요하므로 변수라고 할 수 있다.

상수는 상태 유지와 가독성, 유지보수의 편의를 위해 적극적으로 사용해야 한다.

`const`로 선언된 변수에 원시 값을 할당한 경우 원시 값은 변경할 수 없는 값이고 `const`키워드에 의해 재할당이 금지되므로 할당된 값을 변경할 수 있는 방법은 없다.

```js
const TAX_RATE = 0.1;

let preTaxPrice = 100;

let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

위 코드에서 세율이 변경되면 상수만 변경하면 되기 때문에 유지보수성이 대폭 향상된다.

### 15.3.4 const 키워드와 객체

`const`키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다.  
이는 객체가 변경 가능한 값이기 때문에 재할당 없이도 직접 변경이 가능하기 때문이다.

따라서 `const`키워드는 재할당을 금지할 뿐 "불변"을 의미하지 않는다.  
(반대로 새로운 객체를 할당하는 것은 불가능하다.)

## 15.4 var vs. let vs. const

`var`와 `let`, `const`키워드는 다음과 같이 사용하는 것을 권장한다.

- ES6를 사용한다면 `var`키워드는 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 `let` 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
- 변경이 발생하지 않고 읽기 전용으로 사용하는 원시 값과 객체에는 `const`키워드를 사용한다. (재할당을 금지하기 때문)
