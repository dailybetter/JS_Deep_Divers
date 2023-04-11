# 20 strict mode

## 20.1 strict mode란?

```js
function foo() {
  x = 10;
}
foo();

console.log(x);
```

`foo` 함수 내에서 선언하지 않은 `x` 변수에 값 10을 할당했다. 이때 `x` 변수를 찾아야 `x`에 값을 할당할 수 있기 때문에 엔진은 스코프 체인을 통해 검색하기 시작한다.

엔진은 먼저 `foo` 함수의 스코프에서 `x` 변수의 선언을 검색한다. 하지만 함수의 스코프에는 변수의 선언이 없기 때문에 실패할 것이고, 이후 상위 스코프(예시의 경우 전역 스코프)에서 변수의 선언을 검색한다.

전역 스코프에도 존재하지 않기 때문에 `ReferenceError`를 발생시킬 것 같지만 엔진은 암묵적으로 전역 객체에 `x` 프로퍼티를 동적 생성한다. 이때 전역 객체 `x` 프로퍼티는 마치 전역 변수처럼 사용할 수 있다.  
이러한 현상을 암묵적 전역이라한다.

이러한 오타나 문법 지식의 미비로 인한 실수를 막기위해 개발 환경을 어렵게 하는 방법이 ES5부터 추가되었고 이를 strict mode(엄격 모드)라고 한다.

## 20.2 strict mode의 적용

strict mode를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다. 전역의 선두에 추가하면 스크립트의 전체에 strict mode가 적용된다.

```js
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

다만 전역 또는 함수 몸체의 선두에 위치시키지 않으면 strict mode가 제대로 동작하지 않는다.

## 20.3 전역에 strict mode를 적용하는 것은 피하자

```html
<script>
  "use strict";

  y = 1; // ReferenceError
  console.log(y);
</script>
<script>
  x = 10;
</script>
<script>
  console.log(x); // 10
</script>
```

위 처럼 스크립트 단위로만 strict mode가 적용되지만 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문이다.

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

어떤 함수에는 적용하고 어떤 함수는 적용하지 않는 것은 바람직하지 않으며 모든 함수에 일일이 strict mode를 적용하는 것도 번거로운 일이다. 그리고 strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있다.

따라서 strict mode는 IIFE로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

## 20.5 strict mode가 발생시키는 에러

### 20.5.1 암묵적 전역

```js
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError
})();
```

### 20.5.2 변수, 함수, 매개변수의 삭제

`delete` 연산자로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`가 발생한다.

```js
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError

  function foo(a) {
    delete a; // SyntaxError
  }
  delete foo; // SyntaxError
})();
```

### 20.5.3 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 `SyntaxError`가 발생한다

```js
(function () {
  "use strict";

  // SyntaxError
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### 20.5.4 with 문의 사용

`with` 문을 사용하면 `SyntaxError`가 발생한다. `with` 문은 전달된 객체를 스코프 체인에 추가한다. 그리고 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠지는 문제가 있다.

```js
(function () {
  "use strict";

  // SyntaxError
  with ({ x: 1 }) {
    console.log(x);
  }
});
```

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

strict mode에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 `this`를 사용할 필요가 없기 때문이다. 단 에러는 발생하지 않는다.

```js
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

### 20.6.2 arguments 객체

strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않는다.

```js
(function (a) {
  "use strict";

  a = 2;

  console.log(arguments); // { 0: 1, length: 1}
})(1);
```

---

# 20 strict mode

## 20.1 strict mode란?

```js
function foo() {
  x = 10;
}
foo();

console.log(x);
```

`foo` 함수 내에서 선언하지 않은 `x` 변수에 값 10을 할당했다. 이때 `x` 변수를 찾아야 `x`에 값을 할당할 수 있기 때문에 엔진은 스코프 체인을 통해 검색하기 시작한다.

엔진은 먼저 `foo` 함수의 스코프에서 `x` 변수의 선언을 검색한다. 하지만 함수의 스코프에는 변수의 선언이 없기 때문에 실패할 것이고, 이후 상위 스코프(예시의 경우 전역 스코프)에서 변수의 선언을 검색한다.

전역 스코프에도 존재하지 않기 때문에 `ReferenceError`를 발생시킬 것 같지만 엔진은 암묵적으로 전역 객체에 `x` 프로퍼티를 동적 생성한다. 이때 전역 객체 `x` 프로퍼티는 마치 전역 변수처럼 사용할 수 있다.  
이러한 현상을 암묵적 전역이라한다.

이러한 오타나 문법 지식의 미비로 인한 실수를 막기위해 개발 환경을 어렵게 하는 방법이 ES5부터 추가되었고 이를 strict mode(엄격 모드)라고 한다.

## 20.2 strict mode의 적용

strict mode를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다. 전역의 선두에 추가하면 스크립트의 전체에 strict mode가 적용된다.

```js
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

다만 전역 또는 함수 몸체의 선두에 위치시키지 않으면 strict mode가 제대로 동작하지 않는다.

## 20.3 전역에 strict mode를 적용하는 것은 피하자

```html
<script>
  "use strict";

  y = 1; // ReferenceError
  console.log(y);
</script>
<script>
  x = 10;
</script>
<script>
  console.log(x); // 10
</script>
```

위 처럼 스크립트 단위로만 strict mode가 적용되지만 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문이다.

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

어떤 함수에는 적용하고 어떤 함수는 적용하지 않는 것은 바람직하지 않으며 모든 함수에 일일이 strict mode를 적용하는 것도 번거로운 일이다. 그리고 strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있다.

따라서 strict mode는 IIFE로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

## 20.5 strict mode가 발생시키는 에러

### 20.5.1 암묵적 전역

```js
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError
})();
```

### 20.5.2 변수, 함수, 매개변수의 삭제

`delete` 연산자로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`가 발생한다.

```js
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError

  function foo(a) {
    delete a; // SyntaxError
  }
  delete foo; // SyntaxError
})();
```

### 20.5.3 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 `SyntaxError`가 발생한다

```js
(function () {
  "use strict";

  // SyntaxError
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### 20.5.4 with 문의 사용

`with` 문을 사용하면 `SyntaxError`가 발생한다. `with` 문은 전달된 객체를 스코프 체인에 추가한다. 그리고 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠지는 문제가 있다.

```js
(function () {
  "use strict";

  // SyntaxError
  with ({ x: 1 }) {
    console.log(x);
  }
});
```

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

strict mode에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 `this`를 사용할 필요가 없기 때문이다. 단 에러는 발생하지 않는다.

```js
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

### 20.6.2 arguments 객체

strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않는다.

```js
(function (a) {
  "use strict";

  a = 2;

  console.log(arguments); // { 0: 1, length: 1}
})(1);
```

---

# 22 this

## 22.1 this 키워드

객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다.  
동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다. 이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.

```js
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * circle.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

`circle.radius` 참조 표현식이 평가되는 시점은 메서드가 호출되어 함수 몸체가 실행되는 시점이다.  
해당 객체 리터럴은 `circle` 변수에 할당되기 직전에 평가되기 때문에 `getDiameter` 메서드가 호출되는 시점에는 이미 평가가 완료되어 객체가 생성되었고 `circle` 식별자를 참조할 수 있는 것이다.

하지만 이러한 방식은 일반적이지 않으며 바람직하지 않다.

```js
function Circle(radius) {
  ????.radius = radius; // 인스턴스의 식별자를 알 수 없음
}

Circle.prototype.getDiameter = function () {
  return 2 * ????.radius;
}

const circle = new Circle(5);
```

생성자 함수 내부에서는 프로퍼티 또는 메서드를 추가하기 위해 자신이 생성할 인스턴스를 참조할 수 있어야한다. 하지만 생성자 함수에 의한 객체 생성 방식은 먼저 생성자 함수를 정의한 이후 `new` 연산자와 함께 호출하는 단계가 추가로 필요하다.  
다시 말해, 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야 한다.

하지만 생성자 함수를 정의하는 시점에는 인스턴스를 생성하기 이전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없다.  
이때 사용하는 특수한 식별자가 `this`다.

`this`는 엔진에 의해 암묵적으로 생성되며 코드 어디서든 참조할 수 있다. 함수를 호출하면 `arguments` 객체와 `this`가 암묵적으로 함수 내부에 전달된다.

이때 `this`가 가리키는 값, 즉 `this` 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.

클래스 기반 언어에서 `this`는 언제나 클래스가 생성하는 인스턴스를 가리킨다. 하지만 자바스크립트의 `this`는 함수가 호출되는 방식에 따라 `this`에 바인딩될 값, 즉 `this` 바인딩이 동적으로 결정된다.

```js
console.log(this); // window

function square(number) {
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: "Park",
  getName() {
    console.log(this); // {name: 'Park', getName: f}
    return this.name;
  },
};
console.log(person.getName()); // Park

function Person(name) {
  this.name = name;
  console.log(this); // Person {name: 'Park'}
}

const me = new Person("Park");
```

이처럼 `this`는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수이므로 일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다.

## 22.2 함수 호출 방식과 this 바인딩

다시 한 번 말하면 `this`는 함수 호출 방식에 따라 동적으로 결정된다.
주의할 것은 동일한 함수도 여러 방식으로 호출할 수 있다는 것이다.

### 22.2.1 일반 함수 호출

기본적으로 `this`에는 전역 객체가 바인딩된다.

```js
function foo() {
  console.log("foo's this: " + this); // window
  function bar() {
    console.log("bar's this: " + this); // window
  }
  bar();

  function baz() {
    "use strict";
    console.log("baz's this: " + this); // undefined, 일반 함수에서 this는 의미가 없기 때문에 strict mode에서는 undefined가 바인딩된다.
  }
}
foo();
```

전역 함수는 물론이고 중첩 함수를 일반 함수로 호출하면 함수 내부의 `this`에는 전역 객체가 바인딩된다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(`${this}, ${this.value}`); // {value: 100, foo: f}, 100

    function bar() {
      console.log(`${this}, ${this.value}`); // window, 1
    }

    bar();
  },
};

obj.foo();
```

객체의 메서드내 중첩 함수, 콜백 함수 상관없이 일반 함수로 호출되면 `this`에 전역 객체가 바인딩된다.

하지만 외부 함수인 메서드와 중첩 함수 또는 콜백 함수의 `this`가 일치하지 않는다는 것은 중첩 함수 또는 콜백 함수를 헬퍼 함수로 동작하기 어렵게 만든다.

이를 해결하기 위한 방법으로는

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    const that = this;

    function bar() {
      console.log(that.value); // 100
    }

    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);

    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => {
      console.log(that.value); // 100
    }, 100);

    bar();
  },
};

obj.foo();
```

이밖에도 `Function.prototype.apply`, `Function.prototype.call`, `Function.prototype.bind` 메서드를 제공한다.

### 22.2.2 메서드 호출

메서드 내부의 `this`에서는 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩된다. 주의할 점은 내부의 `this`는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩 된다는 것이다.

```js
const person = {
  name: "Park",
  getName() {
    return this.name;
  },
};

console.log(person.getName()); // Park, person 객체가 호출함

const anotherPerson = {
  name: "Kim",
};

anotherPerson.getName = person.getName;
console.log(anotherPerson.getName()); // Kim, anotherPerson 객체가 호출함

const getName = person.getName;
console.log(getName()); // '', 일반 함수로 호출됐기 때문에 브라우저 환경의 window.name과 같음(브라우저 창의 이름을 나타내는 빌트인 프로퍼티다. Node.js에선 undefined)
```

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Park");

console.log(me.getName()); // Park, me 객체가 호출함

Person.prototype.name = "Lee";

console.log(Person.prototype.getName()); // Lee, Person.prototype이 호출함
```

### 22.2.3 생성자 함수 호출

생성자 함수 내부의 `this`에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩된다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

여기서도 `new` 연산자를 제외하면 일반 함수 호출로 동작하기 때문에 `window`를 가리키게 된다.

### 22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출

`Function.prototype.apply`, `Function.prototype.call` 메서드는 `this`로 사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출한다.

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding()); // window

console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}
```

`apply`와 `call` 메서드의 본질적인 기능은 함수를 호출하는 것이다.  
둘의 차이는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다.

```js
function getThisBinding() {
  console.log(arguments);

  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.iterator): f]
// {a: 1}
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.iterator): f]
// {a: 1}
```

`apply` 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.  
`call` 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.

둘의 대표적 용도는 `arguments` 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우다. `arguments` 객체는 배열이 아니기 때문에 배열 메서드를 사용할 수 없으나 두 메서드를 이용하면 가능하다.

```js
function convertArgsToArray() {
  console.log(arguments);

  const arr = Array.prototype.slice.apply(arguments);
  // const arr = Array.prototype.slice.call(arguments);

  console.log(arr);

  return arr;
}

convertArgsToArray([1, 2, 3]); // [1, 2, 3]
```

`Function.prototype.bind` 메서드는 `apply`, `call` 메서드와 달리 함수를 호출하지 않고 첫 번째 전달한 값으로 `this` 바인딩이 교체된 함수를 새롭게 생성해 반환한다.

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.bind(thisArg)); // getThisBinding
console.log(getThisBinding.bind(thisArg)()); // {a: 1}, 명시적으로 호출해야함
```

```js
const person = {
  name: "Park",
  foo(callback) {
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  // bind를 사용하지 않았다면 콜백 함수인 일반 함수가 호출되는 시점의 this는 window이므로 this.name은 ''였을 것이다.
  console.log(`Hi! My name is ${this.name}`); // Hi!, My name is Park
});
```
