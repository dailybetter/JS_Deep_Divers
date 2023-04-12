# 26 ES6 함수의 추가 기능

## 26.1 함수의 구분

자바스크립트의 함수는 일반적인 함수로서 호출할 수도 있고, `new` 연산자와 함께 호출하여 인스턴스를 생성할 수 있는 생성자 함수로서 호출할 수도 있으며 객체에 바인딩되어 메서드로서 호출할 수도 있다. 이는 언뜻 보면 편리한 것 같지만 실수를 유발시킬 수 있으며 성능 면에서도 손해다.

ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다. (callable이면서 constructor다.)

따라서 메서드라고 부르던 객체에 바인딩된 함수도 callable이며 constructor이기 때문에 생성자 함수로서도 호출할 수 있다는 것이다.

```js
var obj = {
  x: 10,
  f: function () {
    return this.x;
  },
};

console.log(obj.f()); // 10
console.log(new obj.f()); // f {}
```

객체에 바인딩된 함수가 `constructor`라는 것은 객체에 바인딩된 함수가 `prototype` 프로퍼티를 가지며, 프로토타입 객체도 생성한다는 것을 의미하기 때문이다.

따라서 ES6에서는 함수를 사용 목적에 따라 세가지 종류로 명확히 구분했다.

| ES6 함수의 구분 | constructor | prototype | super | arguments |
| --------------- | :---------: | :-------: | :---: | :-------: |
| 일반 함수       |      O      |     O     |   X   |     O     |
| 메서드          |      X      |     X     |   O   |     O     |
| 화살표 함수     |      X      |     X     |   X   |     X     |

일반 함수는 함수 선언문이나 함수 표현식으로 정의한 함수를 말하며 ES6 이전의 함수와 차이가 없다.  
하지만 메서드와 화살표 함수는 non-constructor다.

## 26.2 메서드

ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.

```js
const obj = {
  x: 1,
  foo() {
    return this.x;
  },
  bar: function () {
    return this.x;
  },
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1

new obj.foo(); // TypeError: obj.foo is not a constructor
new obj.bar(); // bar {}
```

ES6 메서드는 인스턴스를 생성할 수 없으므로 `prototype` 프로퍼티가 없고 프로토타입도 생성하지 않는다.

```js
obj.foo.hasOwnProperty("prototype"); // false
obj.bar.hasOwnProperty("prototype"); // true
```

참고로 표준 빌트인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 non-constructor다.

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다. `super` 참조는 해당 내부 슬롯을 사용하여 수퍼클래스의 메서드를 참조하므로 ES6 메서드는 `super` 키워드를 사용할 수 있다.

```js
const base = {
  name: "Park",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  sayHi() {
    return `${super.sayHi()}. How are you doing?`;
  },
};

console.log(derived.sayHi()); // Hi! Park. How are you doing?
```

이처럼 ES6 메서드는 본연의 기능(`super`)을 추가하고 의미적으로 맞지 않는 기능(`constructor`)은 제거했다. 따라서 메서드를 정의할 때 프로퍼티 값으로 익명 함수 표현식을 할당하는 ES6 이전의 방식은 사용하지 않는 것이 좋다.

## 26.3 화살표 함수

화살표 함수는 `function` 키워드 대신 화살표를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다. 특히 화살표 함수는 콜백 함수 내부에서 `this`가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용하다.

### 26.3.1 화살표 함수 정의

- 함수 정의  
   화살표 함수는 함수 선언문으로 정의할 수 없고 함수 표현식으로 정의해야 한다.
- 매개변수 선언  
   매개변수가 여러 개인 경우 소괄호 () 안에 매개변수를 선언하며 매개변수가 한 개인 경우 소괄호를 생략 할수 있다.  
   하지만 매개변수가 없는 경우엔 소괄호를 생략할 수 없다.
- 함수 몸체 정의  
   함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 {}를 생략할 수 있다. 이때 함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환된다.  
   여러 개의 문으로 구성된다면 중괄호를 생략할 수 없고 반환값이 있다면 명시적으로 반환해야 한다.  
   따라서 함수 몸체가 하나의 문으로 구성된다 해도 함수 몸체의 문이 표현식이 아닌 문이라면 중괄호를 생략할 수 없다.  
   객체 리처럴을 반환하는 경우 객체 리터럴을 소괄호로 감싸 주어야 한다. 감싸지 않으면 함수 몸체를 감싸는 중괄호로 잘못 해석해 값이 반환되지 않는다.

화살표 함수도 일급 객체이므로 표준 빌트인 객체의 콜백 함수를 인자로 받는 고차 함수에 인수로 전달할 수 있다. 이 경우 일반적인 함수 표현식보다 표현이 간결하고 가독성이 좋다.

### 26.3.2 화살표 함수와 일반 함수의 차이

1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.
   화살표 함수는 인스턴스를 생성할 수 없으므로 `prototype` 프로퍼티가 없고 프로토타입도 생성하지 않는다.
2. 중복된 매개변수 이름을 선언할 수 없다.
3. 화살표 함수는 함수 자체의 `this`, `arguments`, `super`, `new.target` 바인딩을 갖지 않는다.
   따라서 화살표 함수 내부에서 해당 식별자를 참조하면 스코프 체인을 통해 상위 스코프의 식별자를 참조한다.  
   만약 화살표 함수와 화살표 함수가 중첩되어 있다면 상위 화살표 함수에도 해당 식별자 바인딩이 없으므로 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 식별자를 참조한다.

### 26.3.3 this

화살표 함수의 `this`는 일반 함수의 `this`와 다르게 동작한다. 이는 콜박 함수 내부의 `this`가 외부 함수의 `this`와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것이다.

`this` 바인딩은 이전에 살펴본 것처럼 어떻게 호출되었는지에 따라 동적으로 결정된다.(정의할 때 정적으로 결정되는 것이 아님.)  
이때 주의할 것은 일반 함수로서 호출되는 콜백 함수의 경우다. 고차 함수의 인수로 전달되어 고차 함수 내부에서 호출되는 콜백 함수도 중첩 함수라고 할 수 있다.

```js
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map(function (item) {
      return this.prefix + item; // TypeError: Cannot read property 'prefix' of undefined
    });
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

에러가 발생하는 이유는 다음과 같다.  
프로토타입 메서드 내부인 반환문에서 `this`는 메서드를 호출한 객체(`prefixer`)를 가리킨다. 그런데 `Array.prototype.map`의 인수로 전달한 콜백 함수의 내부인 다른 반환문에서 `this`는 `undefined`를 가리킨다.  
이는 해당 메서드가 콜백 함수를 일반 함수로서 호출하기 때문이다.

일반 함수로서 호출되는 모든 함수 내부의 `this`는 전역 객체를 가리킨다.  
그런데 클래스 내부의 모든 코드에는 strict mode가 암묵적으로 적용되므로 해당 메서드의 콜백 함수에도 적용되기 때문에 `undefined`가 바인딩되는 것이다.

이 문제를 해결하기 위해 ES6 이전에는 다음과 같은 방법을 사용했다.

1. `add` 메서드를 호출한 `prefixer` 객체를 가리키는 `this`를 일단 회피시킨 후에 콜백 함수 내부에서 사용한다.

   ```js
   add(arr) {
     const that = this;
     return arr.map(function (item) {
       return this.prefix + item;
     });
   }
   ```

2. `Array.prototype.map`의 두 번째 인수로 `add` 메서드를 호출한 `prefixer` 객체를 가리키는 `this`를 전달한다.

   ```js
   add(arr) {
     return arr.map(function (item) {
       return this.prefix + item;
     }, this);
   }
   ```

3. `Function.prototype.bind` 메서드를 사용하여 `add` 메서드를 호출한 `prefixer` 객체를 가리키는 `this`를 바인딩한다.

   ```js
   add(arr) {
     return arr.map(function(item) {
       return this.prefix + item;
     }.bind(this))
   }
   ```

화살표 함수에서는 이 문제를 해결할 수 있다.

```js
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map((item) => this.prefix + item);
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 `this`를 참조하면 상위 스코프의 `this`를 그대로 참조한다. 이를 lexical this라 한다.  
이는 마치 렉시컬 스코프와 같이 화살표 함수의 `this`가 함수가 정의된 위치에 의해 결정된다는 것을 의미한다.

```js
() => this.x;

(function () {
  return this.x;
}.bind(this));
```

따라서 화살표 함수가 전역 함수라면 화살표 함수의 `this`는 전역 객체를 가리킨다.  
그리고 프로퍼티에 할당한 화살표 함수도 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 `this`를 참조한다.

```js
const counter = {
  num: 1,
  increase: () => ++this.num,
};

console.log(counter.increase()); // NaN
```

화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않기 때문에 `Function.prototype.call`, `Function.prototype.apply`, `Function.prototype.bind` 메서드를 사용해도 화살표 함수 내부의 `this`를 교체할 수 없다.  
이는 해당 메서드를 호출할 수 없다는 의미는 아니고 `this` 바인딩을 갖지 않기 때문에 이것을 교체할 수 없다는 뜻이다.

화살표 함수는 언제나 상위 스코프의 `this` 바인딩을 참조한다.

메서드도 메서드 축약 표현(ES6 메서드)를 사용하지 않으면 객체 내부의 프로퍼티를 `this`로 참조할 수 없기 때문에 화살표 함수를 사용하는 것은 바람직하지 않다.

이는 프로토타입 객체의 프로퍼티에 화살표 함수를 할당하는 경우도 동일한 문제가 발생한다.  
만약 프로토타입 객체에 프로퍼티를 동적 추가하고 싶으면 일반 함수를 할당하거나 객체 리터럴을 바인딩하고 프로토타입의 `constructor` 프로퍼티와 생성자 함수 간의 연결을 재설정한다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype = {
  constructor: Person,
  sayHi() {
    console.log(`Hi! ${this.name}`);
  },
};

const person = new Person("Park");
person.sayHi(); // Hi! Park
```

또한 클래스 필드 정의 제안을 사용하여 클래스 필드에 화살표 함수를 할당할 수도 있다.

```js
class PersonBad {
  name = "Park";
  sayHi = () => console.log(`Hi ${this.name}`);
}

class PersonBad {
  constructor() {
    this.name = "Park";
    this.sayHi = () => console.log(`Hi ${this.name}`);
  }
}
```

위쪽의 코드가 아래처럼 동작하게된다. 즉 프로토타입의 프로퍼티가 아니라 클래스의 인스턴스 프로퍼티인 것이다.  
`sayHi` 클래스 필드에 할당한 화살표 함수 내부에서 참조한 `this`는 `construdtor` 내부의 `this` 바인딩과 같다. 이것은 클래스가 생성한 인스턴스를 가리키므로 화살표 함수의 `this`도 인스턴스를 가리키는 것이다.

따라서 메서드를 정의할 때는 메서드 축약 표현으로 정의하는 것이 좋다.

```js
class Person {
  name = "Park";
  sayHi() {
    console.log(`Hi ${this.name}`);
  }
}
```

### 26.3.4 super

화살표 함수는 함수 자체의 `super` 바인딩을 갖지 않는다. 따라서 `this`와 마찬가지로 상위 스코프의 `super`를 참조한다.

```js
class Base {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

class Derived extends Base {
  sayHi = () => `${super.sayHi()}. How are you doing?`;
};

const derived = new Derived('Park')
console.log(derived.sayHi()) // Hi! Park how are you doing?
```

`super`는 내부 슬롯 [[HomeObject]]를 갖는 ES6 메서드 내에서만 사용할 수 있는 키워드다.  
화살표 함수는 ES6 메서드는 아니지만 `super`를 참조해도 에러가 발생하지 않고 `constructor`의 `super` 바인딩을 참조한다.

### 26.3.5 arguments

화살표 함수는 함수 자체의 `arguments` 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 `arguments`를 참조하면 `this`와 마찬가지로 상위 스코프의 `arguments`를 참조한다.

`arguments` 객체는 함수를 정의할 때 매개변수의 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용한 데 화살표 함수에서는 이를 사용할 수 없다.  
따라서 화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용해야 한다.

## 26.4 Rest 파라미터

### 26.4.1 기본 문법

Rest 파라미터는 매개변수 이름 앞에 세개의 점 ...을 붙여서 정의한 매개변수를 의미한다.  
이것은 함수에 전달된 인수들의 목록을 배열로 전달받는다.

```js
function foo(...rest) {
  console.log(rest); // [ 1, 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

function bar(param, ...rest) {
  console.log(param); // 1
  console.log(rest); // [ 2, 3, 4, 5 ]
}

bar(1, 2, 3, 4, 5);
```

일반 매개변수와 함께 사용할 수 있으며 함수에 전달된 인수들은 매개변수와 Rest 파라미터에 순차적으로 할당된다.

다만 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이기 때문에 반드시 마지막 파라미터이어야 한다.  
그리고 단 하나만 선언할 수 있으며, 함수 정의 시 매개변수 개수를 나타내는 함수 객체의 `length` 프로퍼티에 영향을 주지 않는다.

### 26.4.2 Rest 파라미터와 arguments 객체

`arguments` 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용할 수 있다.

하지만 이것은 유사 배열 객체이므로 배열 메서드를 사용하려면 `call`, `apply` 메서드를 사용해 `arguments` 객체를 배열로 변환해야 한다.

rest 파라미터를 사용하면 배열이기 때문에 배열 메서드를 사용할 수 있기 때문에 배열로 변환하는 번거로움을 피할 수 있다.

## 26.5 매개변수 기본값

함수를 호출할 때 매개변수의 개수만큼 인수를 전달하는 것이 바람직하지만 그렇지 않은 경우에도 에러가 발생하지 않는다. 이는 자바스크립트 엔진이 매개변수의 개수와 인수의 개수를 체크하지 않기 때문이다.

인수가 전달되지 않는 매개변수의 값은 `undefined`이기 때문에 의도치 않은 결과를 피하기 위해서 매개변수에 기본 값을 할당하는 방어코드가 필요하다.

ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다.  
이것은 매개변수에 인수를 전달하지 않은 경우와 `undefined`를 전달한 경우에만 유효하다.

```js
function sum(x, y) {
  x = x || 0;
  y = y || 0;

  return x + y;
}

function sumWithDefaultParameter(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1)); // 1
console.log(sumWithDefaultParameter(1)); // 1
```
