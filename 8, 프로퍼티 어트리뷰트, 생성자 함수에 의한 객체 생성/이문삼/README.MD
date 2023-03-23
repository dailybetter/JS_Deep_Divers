# 16 프로퍼티 어트리뷰트

## 16.1 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티와 의사 메서드다. ECMAScript 사양에 등장하는 이중 대괄호([\[\...]])로 감싼 이름들이 내부 슬롯과 내부 메서드다.

이대로 엔진이 구현되어 실제로 동작하지만 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아니다.  
단 `[[prototype]]`같은 경우처럼 일부 에 한에서 접근할 수 있는 수단을 제공하기는 한다.

```js
const o = {}

o.[[prototype]] // Uncaught SyntaxError: Unexpected token '['
o.__proto__ // Object.prototype
```

## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다.  
프로퍼티의 상태란 프로퍼티의 값, 값의 갱신 가능 여부, 열거 가능 여부, 재정의 가능 여부를 말한다.

프로퍼티 어트리뷰트는 자바스크립트 엔진이 관리하는 내부 상태 값인 내부 슬롯 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`이다. 따라서 프로퍼티 어트리뷰트에 직접 접근할 수 없지만 `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있다.

```js
const person = {
  name: "Park",
  age: 26,
};

console.log(Object.getOwnPropertyDescriptor(person, "name"));
// {value: 'Park', writable: true, enumerable: true, configurable: true}
console.log(Object.getOwnPropertyDescriptors(person, "name"));
// {value: 'Park', writable: true, enumerable: true, configurable: true}
// {value: 26, writable: true, enumerable: true, configurable: true}
```

이때 `Object.getOwnPropertyDescriptor`메서드는 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.  
만약 존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 `undefined`가 반환된다.

ES8에서 도입된 `Object.getOwnPropertyDescriptors`는 모든 프로퍼티의 디스크립터 객체를 반환한다.

## 16.3 데이터 프로퍼티와 접근자 프로퍼티

- 데이터 프로퍼티: 키와 값으로 구성된 일반적인 프로퍼티. 지금까지 살펴본 모든 프로퍼티는 데이터 프로퍼티다.
- 접근자 프로퍼티: 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티다.

### 16.3.1 데이터 프로퍼티

<table>
  <tr>
    <th>프로퍼티 어트리뷰트</th>
    <th>
      프로퍼티 디스크립터<br/>
      객체의 프로퍼티
    </th>
    <th>설명</th>
  </tr>
  <tr>
    <td>[[Value]]</td>
    <td>value</td>
    <td>
      <li>프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값이다.</li>
      <li>프로퍼티 키를 통해 프로퍼티 값을 변경하면 <code>[[Value]]</code>에 값을 재할당한다. 이때 프로퍼티가 없으면 프로퍼티를 동적 생성하고 생성된 프로퍼티의 <code>[[Value]]</code>에 값을 저장한다.</li>
    </td>
  </tr>
  <tr>
    <td>[[Writable]]</td>
    <td>writable</td>
    <td>
      <li>프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖는다.</li>
      <li><code>[[Writable]]</code>의 값이 false인 경우 해당 프로퍼티의 <code>[[Value]]</code>의 값을 변경할 수 없는 읽기 전용 프로퍼티가 된다.</li>
    </td>
  </tr>
  <tr>
    <td>[[Enumerable]]</td>
    <td>enumerable</td>
    <td>
      <li>프로퍼티의 열거 가능 여부를 나타내며 불리언 값을 갖는다.</li>
      <li><code>[[Enumerable]]</code>의 값이 false인 경우 해당 프로퍼티는 for ... in문이나 Object.keys 메서드 등으로 열거할 수 없다.</li>
    </td>
  </tr>
  <tr>
    <td>[[Configurable]]</td>
    <td>configurable</td>
    <td>
      <li>프로퍼티의 재정이 가능 여부를 나타내며 불리언 값을 갖는다.</li>
      <li><code>[[Configurable]]</code>의 값이 false인 경우 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지된다. 단, <code>[[Writable]]</code>이 true인 경우 <code>[[Value]]</code>의 변경과 <code>[[Writable]]</code>을 false로 변경하는 것은 허용된다.</li>
    </td>
  </tr>
</table>

보통 프로퍼티가 생성될 때 `[[Value]]`의 값은 프로퍼티 값으로 초기화되며(동적 생성도 동일) `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`의 값은 `true`로 초기화된다.

### 16.3.2 접근자 프로퍼티

접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

<table>
  <tr>
    <th>프로퍼티 어트리뷰트</th>
    <th>
      프로퍼티 디스크립터<br/>
      객체의 프로퍼티
    </th>
    <th>설명</th>
  </tr>
  <tr>
    <td>[[Get]]</td>
    <td>get</td>
    <td>접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수다. 즉, 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰트 <code>[[Get]]</code>의 값, 즉 getter 함수가 호출되고 그 결과가 프로퍼티 값으로 반환된다.</td>
  </tr>
  <tr>
    <td>[[Set]]</td>
    <td>set</td>
    <td>접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수다. 즉, 접근자 프로퍼티 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트 <code>[[Set]]</code>의 값, 즉 setter 함수가 호출되고 그 결과가 프로퍼티 값으로 저장된다.</td>
  </tr>
  <tr>
    <td>[[Enumerable]]</td>
    <td>enumerable</td>
    <td>데이터 프로퍼티의 <code>[[Enumerable]]</code>과 같다.</td>
  </tr>
  <tr>
    <td>[[Configurable]]</td>
    <td>configurable</td>
    <td>데이터 프로퍼티의 <code>[[Configurable]]</code>과 같다.</td>
  </tr>
</table>

```js
const person = {
  firstName: "Nojun",
  lastName: "Park",

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 접근자 프로퍼티를 통한 값의 수정 및 참조
person.fullName = "Gam Gyul"; // call setter function
console.log(person); // Gam Gyul, call getter function
```

그리고 `Object.getOwnPropertyDescriptor`메서드가 반환한 프로퍼티 어트리뷰트를 객체로 표현한 프로퍼티 디스커립터 객체를 살펴보면 접근자 프로퍼티와 데이터 프로퍼티를 구분할 수 있다.

## 16.4 프로퍼티 정의

```js
const person = {};

Object.defineProperty(person, 'firstName', {
  value: 'Nojun',
  writable: true,
  enumerable: true,
  configurable: true,
});

const person2 = {};
Object.defineProperties(person, {
  firstName: {
    value: 'Nojun',
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    ...
  },
  fullName: {
    get() {
      return this.firstName + ' ' + this.lastName;
    },
    set(name) {
      [this.firstName, this.lastName] = name.split(' ')
    },
    enumerable: true,
    configurable: true,
  }
});
```

`Object.defineProperty`(한 개), `Object.defineProperties`(여러 개)를 사용하면 프로퍼티를 정의할 수 있다.

## 16.5 객체 변경 방지

### 16.5.1 객체 확장 금지

`Object.preventExtentions` 메서드는 객체의 확장을 금지한다. 여기서 확장을 금지한다는 것은 프로퍼티 추가만 금지된다는 것이다.  
즉, 동적 추가와 `Object.defineProperty`메서드로 추가할 수 없다.

확장이 가능한 객체인지는 `Object.isExtensible`메서드로 확인할 수 있다.

```js
const person = { firstName: "Nojun" };

Object.preventExtensions(person);

person.age = 26; // 기본 동작은 무시, strict 모드에서는 에러
console.log(person); // {firstName: 'Nojun'}

delete person.firstName;
console.log(person); // {}
```

### 16.5.2 객체 밀봉

`Object.seal`메서드는 객체를 밀봉한다. 여기서 밀봉이란 읽기, 쓰기만 가능한 것을 말한다.

밀봉된 객체인지 여부는 `Object.isSealed`메서드로 확인할 수 있다.

```js
const person = { name: "Park" };

Object.seal(person);
console.log(Object.getOwnPropertyDescriptors(person));
// 밀봉된 객체는 configurable이 false다.
// name: {value: 'Park', writable: true, enumerable: true, configurable: false}

person.age = 26; // 기본 동작은 무시, strict 모드에서는 에러
console.log(person); // {name: 'Park'}

person.name = "Lee";
console.log(person); // {name: 'Lee'}, 갱신은 가능

Object.defineProperty(person, "name", { configurable: true }); // TypeError: Cannot redefine property: name
```

### 16.5.3 객체 동결

`Object.freeze`메서드는 객체를 동결한다. 여기서 동결이란 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지, 프로퍼티 값 갱신 금지를 의미한다.  
즉, 읽기만 가능하다.

동결된 객체 여부는 `Object.isFrozen`메서드로 확인할 수 있다.

```js
const person = { name: "Park" };

Object.freeze(person);

console.log(Object.getOwnPropertyDescriptors(person));
// 밀봉된 객체는 writable과 configurable이 false다.
// name: {value: 'Park', writable: false, enumerable: true, configurable: false}
```

### 16.5.4 불변 객체

지금까지 살펴본 변경 방지 메서드들은 얕은 변경 방지로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지 못한다.

```js
const person = {
  name: "Park",
  address: {
    city: "Seoul",
  },
};

Object.freeze(person);

console.log(Object.isFrozen(person.address)); // false

person.address.city = "Busan";
console.log(person); // {name: 'Park', address: {city: 'Busan'}}
```

만약 읽기 전용의 불변 객체를 구현하려면 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 `Object.freeze`메서드를 호출해야 한다.

```js
function deepFreeze(target) {
  if (target && typeof target === "object" && !Object.isFrozen(target)) {
    Object.freeze(target);
    Object.keys(target).forEach((key) => deepFreeze(target[key]));
  }

  return target;
}

const person = {
  name: "Park",
  address: {
    city: "Seoul",
  },
};

deepFreeze(person);

console.log(Object.isFrozen(person.address)); // true

person.address.city = "Busan";
console.log(person); // {name: 'Park', address: {city: 'Seoul'}}
```

---

# 17 생성자 함수에 의한 객체 생성

## 17.1 Object 생성자 함수

`new`연산자와 함께 `Object` 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다.

```js
const person = new Object();

person.name = "Park";
person.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

person.sayHello(); // Hi! My name is Park
```

자바스크립트는 `Object` 생성자 함수 이외에도 `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`, `RegEcp`, `Promise` 등의 빌트인 생성자 함수를 제공한다.

## 17.2 생성자 함수

### 17.2.1 객체 리터럴에 의한 객체 생성 방식의 문제점

객체 리터럴에 의한 생성은 직관적이고 간편하지만 한 번에 한 개만 생성한다.  
그리고 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메소드를 기술해야한다.

따라서 동일한 프로퍼티 및 메소드를 가지는 객체를 여러 개 생성해야 한다면 불편하다.

### 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.

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

> ### this
>
> this는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수다. this가 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
>
> 1. 일반 함수로서 호출 - 전역 객체
> 2. 메서드로서 호출 - 메서드를 호출한 객체(마침표 앞의 객체)
> 3. 생성자 함수로서 호출 - 생성자 함수가 (미래에) 생성할 인스턴스

자바와 같은 클래스 기반 객체지향 언어의 생성자와는 달리 `new` 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작한다. 그렇지 않다면 일반 함수로 동작한다.

```js
const circle3 = Circle(15);

console.log(circle3); // undefined, 일반 함수로 호출된 Circle은 반환문이 없기 때문
console.log(radius); // 15, Circle 내의 this가 전역 객체를 가리키기 때문
```

### 17.2.3 생성자 함수의 인스턴스 생성 과정

생성자 함수는 프로퍼티 구조가 동일한 인스턴스를 생성하기 위한 템플릿으로 동작해야하기 때문에 인스턴스를 생성하는 것과 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)해야한다.  
여기서 생성은 필수고 초기화는 옵션이다.

이때 인스턴스를 생성하고 반환하는 코드가 없음에도 가능한 이유는 엔진이 암묵적인 처리를 통해 인스턴스를 생성하고 반환하기 때문이다. (`new` 키워드의 존재)

1. 인스턴스 생성과 this 바인딩

암묵적으로 빈 객체가 생성된다. 이 빈 객체가 바로 생성자 함수가 생성한 인스턴스고 `this`에 바인딩 된다.  
이 처리는 런타임 이전에 실행된다.

```js
function Circle(radius) {
  console.log(this) // Circle {}
  ...
}
```

2. 인스턴스 초기화

생성자 함수에 기술되어 있는 코드가 한 줄씩 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화 한다. 즉, `this`에 바인딩되어 있는 인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당한다.

```js
function Circle(radius) {
  // 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

3. 인스턴스 반환

생성자 함수 내부의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.

만약 `this`가 아닌 다른 객체를 명시적으로 반환하면 `this`가 반환되지 못하고 `return` 문에 명시한 객체가 반환된다.

```js
function Circle(radius) {
  // 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  return {};
  // 만약 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환된다.
  // return 100;
}

const circle = new Circle(1);
console.log(circle); // {}
```

> ```js
> function Circle(radius) {
>   // 초기화
>   const circle = {};
>
>   circle.radius = radius;
>   circle.getDiameter = function () {
>     return 2 * circle.radius;
>   };
>
>   return circle;
> }
>
> const circle = new Circle(5);
> console.log(circle); // {}
> console.log(circle.getDiameter()); // 10
> ```
>
> 이런 경우도 생성자 함수로 사용할 수 있다. 다만 명시적으로 this가 아닌 다른 값을 반환하는 것은 생성자 함수의 기본 동작을 훼손하므로 반드시 `return`문을 생략해야 한다.

### 17.2.4 내부 메서드 [[Call]]과 [[Construct]]

함수 선언문, 표현식으로 정의한 함수는 일반적인 함수로 호출 + 생성자 함수로서 호출할 수 있다.

함수는 객체이므로 일반 객체와 동일하게 동작할 수 있다. 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가지고 있기 때문이다.

```js
function foo() {}

foo.prop = 10;
foo.method = function () {
  console.log(this.prop);
};

foo.method(); // 10
```

여기서 일반 객체와 다른 점은 호출이 가능하다는 것이다.  
이는 일반 객체의 내부 슬롯과 메소드는 물론 함수 객체만을 위한 [[Environment]], [[FormalParameters]]등의 내부 슬롯과 [[Call]], [[Construct]] 같은 내부 메서드를 추가로 갖고 있기 때문이다.

일반 함수로서 호출되면 [[Call]], `new` 연산자와 함께 생성자 함수로 호출되면 내부 메서드 [[Construct]]가 호출된다.

여기서 모든 함수는 [[Call]] 메서드를 갖고 있기 때문에 callable하지만, 모든 함수가 [[Construct]]를 가지고 있지는 않다.  
따라서 함수 객체는 constructor일 수도 있고 non-constructor일 수도 있다.

### 17.2.5 constructor와 non-constructor의 구분

엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 구분한다.

- constructor: 함수 선언문, 함수 표현식, 클래스(클래스도 함수다)
- non-constructor: 메서드(ES6 메서드 축약 표현), 화살표 함수

```js
function foo() {}
const bar = function () {};
const baz = {
  // x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 이는 메서드로 인정하지 않는다.
  x: function () {},
};

new foo(); // foo {}
new bar(); // bar {}
new baz.x(); // x {}

const arrow = () => {};
new arrow(); // TypeError: arrow is not a constructor

const obj = {
  x() {},
};
new obj.x(); // TypeError: obj.x is not a constructor
```

non-constructor인 함수 객체는 [[Construct]]를 갖지 않기 때문에 에러가 발생하게 된다.

### 17.2.6 new 연산자

`new` 연산자와 함께 함수를 호출하면 생성자 함수로 동작한다.  
다만 constructor([[Consturct]]를 가진 함수)여야만 한다.

```js
function add(x, y) {
  return x + y;
}

let inst = new add();
console.log(inst); // {}

function createUser(name, role) {
  return { name, role };
}

inst = new createUser("Park", "admin");
console.log(inst); // { name: 'Park', role: 'admin' }
```

일반 함수와 생성자 함수에 특별한 형식적 차이는 없지만 생성자 함수는 일반적으로 첫 문자를 대문자로 기술하는 파스칼 케이스로 명명하여 일반 함수와 구별할 수 있도록 노력한다.

### 17.2.7 new.target

생성자 함수가 `new` 연산자 없이 호출되는 것을 방지하기 위해 파스칼 케이스 컨벤션을 사용한다 하더라도 실수는 발생할 수 있다. 이러한 위험성을 회피하기 위해 ES6에서는 `new.target`을 지원한다.

`new.target`은 `this`와 유사하게 모든 함수 내부에서 암묵적인 지역 변수와 같이 사용되며 메타 프로퍼티라고 부른다. (참고로 IE는 지원하지 않음)

`new` 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 `new.target`은 함수 자신을 가리킨다. 반대로 없이 호출된 함수 내부의 `new.target`은 `undefined`다.

```js
function Circle(radius) {
  if (!new.target) {
    return new Circle(radius); // 재귀 호출을 통해 생성자 함수로서 호출
  }

  // IE에서도 사용 가능한 방법(스코프 세이프 생성자 패턴)
  // new 없이 호출되면 this는 window를 가리키기 때문에(브라우저 기준) 이러한 방법이 가능하다.
  if (!(this instanceof Circle)) {
    return new Circle(radius); // 재귀 호출을 통해 생성자 함수로서 호출
  }
  ...
}

const circle = Circle(5); // new 없이도 인스턴스 생성
console.log(circle.getDiameter()) // 10
```

위에서 설명한 대부분의 빌트인 생성자 함수는 `new` 연산자와 함께 호출되었는지를 확인한 후 적절한 값을 반환한다.

```js
const obj = Object(); // {}
```

하지만 `String`, `Number`, `Boolean` 생성자 함수는 `new` 연산자와 함께 호출 했을 때 각 객체를 생성하여 반환하지만 없다면 문자열, 숫자, 불리언 값을 반환한다.  
이를 통해 데이터 타입을 변환하기도 한다.
