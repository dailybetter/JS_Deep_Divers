## 19.5 프로토타입의 생성 시점

객체는 리터럴 표기법 또는 생성자 함수에 의해 생성되므로 결국 모든 객체는 생성자 함수와 연결되어 있다.

여기서 프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.  
이는 방금 살펴본 것처럼 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.

### 19.5.1 사용자 정의 생성자 함수와 프로토타입 생성 시점

생성자 함수로서 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.

```js
console.log(Person.prototype); // {constructor: f}

function Person(name) {
  this.name = name;
}

// non-constructor는 프로토타입이 생성되지 않는다.
const ArrowPerson = (name) => {
  this.name = name;
};

console.log(ArrowPerson.prototype); // undefined
```

이전에 살펴봤던 것처럼 함수 선언문은 런타임 이전에 엔진에 의해 먼저 실행되기 때문에 `Person` 생성자 함수는 어떤 코드보다 먼저 평가되어 함수 객체가 된다. 그리고 이때 프로토타입도 더불어 생성된다.  
생성된 프로토타입은 `Person` 생성자 함수의 `prototype` 프로퍼티에 바인딩된다.

여기서 생성된 프로토타입은 오직 `constructor` 프로퍼티만을 갖는 객체다. 그리고 프로토타입도 객체고 모든 객체는 프로토타입을 가지므로 프로토타입도 자신의 프로토타입을 갖는다.  
이 프로토타입은 `Object.prototype`이다.

### 19.5.2 빌트인 생성자 함수와 프로토타입 생성 시점

`Object`, `String`, `Number`등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다. 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다. 생성된 프로토타입은 빌트인 생성자 함수의 `prototype` 프로퍼티에 바인딩된다.

이처럼 객체가 생성되기 전에 생성자 함수와 프로토타입은 이미 객체화되어 존재한다.  
이후 객체를 생성하면 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당된다. 이렇게 상속을 받는 것이다.

## 19.6 객체 생성 방식과 프로토타입의 결정

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스

이렇게 다양한 방식으로 생성된 객체는 세부적인 방식의 차이는 있으나 추상 연산 `OrdinaryObjectCreate`에 의해 생성된다는 공통점이 있다.  
추상 연산 `OrdinaryObjectCreate`는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달 받는다. 그리고 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달할 수 있다.

즉, 프로토타입은 추상 연산에 전달되는 인수에 의해 결정된다. 이 인수가 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

### 19.6.1 객체 리터럴에 의해 생성된 객체의 프로토타입

엔진이 객체 리터럴은 평가해 객체를 생성할 때 전달되는 프로토타입은 `Object.prototype`이다.

```js
const obj = { x: 1 };

console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

객체 리터럴에 의해 생성된 `obj` 객체는 `Object.prototype`을 프로토타입으로 갖게 되며, 이로써 `Object.prototype`을 상속받는다.  
따라서 `constructor` 프로퍼티와 `hasOwnProperty` 메서드 등을 소유하지 않지만 자신의 프로토타입인 `Object.prototype`의 `constructor` 프로퍼티와 `hasOwnProperty` 메서드를 자신의 자신인 것처럼 자유롭게 사용할 수 있다.

### 19.6.2 Object 생성자 함수에 의해 생성된 객체의 프로토타입

`Object` 생성자 함수로 생성되는 객체에 전달되는 프로토타입은 `Object.prototype`이다.  
다만 리터럴과의 차이점은 생성자 함수는 일단 빈 객체를 생성한 후 프로퍼티를 추가해야한다는 점.

```js
const obj = new Object(); // 빈 객체 생성
obj.x = 1;

console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

### 16.6.3 생성자 함수에 의해 생성된 객체의 프로토타입

`new` 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성할 때 추상 연산에 전달되는 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Park");
```

위 코드가 실행되면 추상 연산 `OrdinaryObjectCreate`에 의해 생성자 함수와 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체와 생성된 객체 사이에 연결이 만들어진다.

이때 `Person`과 `Person.prototype`의 프로퍼티는 `constructor`뿐이다.  
여기서 원하는 프로퍼티를 추가하면 하위(자식) 객체가 상속받을 수 있다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Me");
const you = new Person("You");

me.sayHello(); // Hi! My name is Me
you.sayHello(); // Hi! My name is You
```

## 19.7 프로토타입 체인

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Me");

console.log(me.hasOwnProperty("name")); // true
```

`Person` 생성자 함수에 의해 생성된 `me` 객체는 `Object.prototype`의 메서드인 `hasOwnProperty`를 호출할 수 있다. 이는 `me` 객체가 `Person.prototype`뿐만 아니라 `Object.prototype`도 상속받았다는 것을 의미한다.

여기서 두 개의 프로토타입을 동시에 상속받았다는 뜻이 아니라 `me` 객체는 `Person.prototype`을 상속받았고, `Person.prototype`은 `Object.prototype`이 프로토타입이다.

```js
Object.getPrototypeOf(me) === Person.prototype; // true
Object.getPrototypeOf(Person) === Object.prototype; // true
```

![그림 19-18](https://velog.velcdn.com/images%2Fnoahshin__11%2Fpost%2Fc0a94928-969d-4388-9978-69358a2c27de%2Fimage.png)

자바스크립트는 객체의 프로퍼티(메소드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.  
**이를 프로토타입 체인이라 한다.**

프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이다.

```js
me.hasOwnProperty("name"); // true
```

위 예시의 메서드 검색 과정은 아래와 같다.

1. 먼저 `hasOwnProperty` 메서드를 호출한 `me` 객체에서 검색한다. `me` 객체에는 해당 메서드가 없으므로 프로토타입 체인, 즉 [[Prototype]] 내부 슬롯에 바인딩되어 있는 프로토타입(`Person.prototype`)으로 이동하여 `hasOwnProperty` 메서드를 검색한다.
2. `Person.prototype`에도 `hasOwnProperty` 메서드가 없으므로 `Object.prototype`으로 이동하여 검색한다.
3. `Object.prototype`에는 `hasOwnProperty` 메서드가 존재한다. 따라서 엔진은 `Object.prototype.hasOwnProperty`를 호출한다. 이때 해당 메서드의 `this`에는 `me` 객체가 바인딩된다.

```js
// call 메서드는 this로 사용할 객체를 전달하면서 함수를 호출한다.
Object.prototype.hasOwnProperty.call(me, "name");
```

프로토타입 체인의 최상위에 위치하는 객체는 언제나 `Object.prototype`이다. 따라서 모든 객체는 `Object.prototype`을 상속받으며 이것을 프로토타입 체인의 종점이라한다.  
체인의 종점인 `Object.prototype`에서도 프로퍼티를 검색할 수 없는 경우 `undefined`를 반환한다.(에러가 아님에 주의)

```js
console.log(me.foo); // undefined
```

따라서 프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘이라고 할 수 있다.

앞에서 공부한 스코프 체인(스코프의 계층적 구조)은 식별자를 검색하기 위한 것이다. 따라서 스코프 체인은 식별자 검색을 위한 메커니즘이라고 할 수 있다.

```js
me.hasOwnProperty("name");
```

예제에서는 먼저 스코프 체인에서 `me` 식별자를 검색한다. 전역에서 선언되었으므로 전역 스코프에서 검색된다.  
그 다음 프로토타입 체인에서 `hasOwnProperty` 메서드를 검색한다.

이처럼 스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는 데 사용된다.

## 19.8 오버라이딩과 프로퍼티 섀도잉

```js
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };

  return Person;
})();

const me = new Person("Park");

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Park
```

프로토타입이 소유한 프로퍼티를 프로토타입 프로퍼티, 인스턴스가 소유한 프로퍼티를 인스턴스 프로퍼티라고 부른다.

프로토타입 프로퍼티와 같은 이름의 프로퍼티를 인스턴스에 추가하면 프로토타입 체인을 따라 프로토타입 프로퍼티를 검색하여 덮어쓰는 것이 아니라 인스턴스 프로퍼티로 추가한다.

이때 인스턴스 메서드 `sayHello`는 프로토타입 메서드 `sayHello`를 오버라이딩 했고, 프로토타입 메서드는 가려진다. 이를 프로퍼티 섀도잉이라 한다.

```js
delete me.sayHello;
me.sayHello(); // Hi! My name is Park

delete me.sayHello;
me.sayHello(); // Hi! My name is Park

delete Person.prototype.sayHello;
me.sayHellow(); // TypeError: me.sayHello is not a function
```

이와 같이 하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능하다.(두 번 `delete`를 수행해도 마찬가지)  
따라서 프로토타입 프로퍼티를 변경 또는 삭제하려면 프로토타입에 직접 접근해야 한다.

## 19.9 프로토타입의 교체

프로토타입은 임의의 다른 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다. 이러한 특징을 활용하여 객체 간의 상속 관계를 동적으로 변경할 수 있다.

### 19.9.1 생성자 함수에 의한 프로토타입의 교체

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Park");

console.log(me.prototype === Person); // false
console.log(me.prototype === Object); // true
```

프로토타입으로 교체한 객체 리터럴에는 `constructor` 프로퍼티가 없다.(엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티)  
따라서 `me` 객체의 생성자 함수를 검색하면 `Person`이 아닌 `Object`가 나온다.

물론 파괴된 연결을 되살릴수도 있다.

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    constructor: Person,
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Park");

console.log(me.prototype === Person); // true
console.log(me.prototype === Object); // false
```

### 19.9.2 인스턴스에 의한 프로토타입의 교체

프로토타입은 생성자 함수의 `prototype` 프로퍼티뿐만 아니라 인스턴스의 `__proto__` 접근자 프로퍼티(또는 `Object.getPrototypeOf` 메서드)를 통해 접근할 수 있다. 이를 통해 교체가 가능하다.

다만 생성자 함수를 통한 방법과 다른 점은 생성자 함수는 미래에 생성할 인스턴스의 프로토타입을 교체하는 것이라면 인스턴스는 이미 생성된 객체의 프로토타입을 교체하는 것이다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Park");

const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  },
};

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);

// 이 코드는 위 코드와 동작이 같다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Park
```

생성자 함수에 의한 교체와 마찬가지로 `parent` 객체에는 `constructor` 프로퍼티가 없으므로 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴된다.  
따라서 프로토타입의 `constructor` 프로퍼티로 `me` 객체의 생성자 함수를 검색하면 `Person`이 아닌 `Object`가 나온다.

여기서 생성자 함수에 의한 교체와 가장 큰 차이점은 생성자 함수에 의한 교체는 `Person` 생성자 함수의 `prototype` 프로퍼티가 교체된 프로토타입(`constructor`만 존재하지 않는)을 가리키지만 인스턴스에 의한 방식은 가리키지 않는다는 것이다.(기존의 `Parent.prototype`을 가리키고 있음)

마찬가지로 되살리는 것또한 가능하지만 꽤나 번거롭다.(상속은 class를 추천)

## 19.10 instanceof 연산자

`instanceof` 연산자는 이항 연산자로서 좌변에는 객체를 가리키는 식별자, 우변에는 생성자 함수를 가리키는 식별자를 피연산자로 받는다. 만약 우변의 피연산자가 함수가 아닌 경우 `TypeError`가 발생한다.

이 연산자는 우변의 생성자 함수의 `prototype`에 바인딩된 객체가 좌변의 객체의 \*/**프로토타입 체인** 상에 존재하면 `true`로 평가되고 아니면 `false`로 평가된다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Park");

console.log(me instanceof Person); // true
console.log(me instanceof Object); // true

const parent = {};

// 인스턴스에 의한 교체
Object.setPrototypeOf(me, parent);

console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false
console.log(me instanceof Person); // false
console.log(me instanceof Object); // true, parent의 프로토타입은 Object.prototype
```

이처럼 `instanceof` 연산자는 프로토타입의 `constructor` 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라 생성자 함수의 `prototype`에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인한다.

```js
function isInstanceOf(instance, constructor) {
  const prototype = Object.getPrototypeOf(instance);

  // 재귀 탈출 조건, prototype이 null이면 프로토타입 체인 종점에 다다른 것이다.
  if (prototype === null) return false;

  // 프로토타입이 생성자 함수의 prototype 프로퍼티에 바인딩된 객체라면 true를 반환한다.(단축 평가)
  // 그렇지 않다면 재귀 호출을 통해 프로토타입 체인 상의 상위 프로토타입으로 이동
  return (
    prototype === constructor.prototype || isInstanceOf(prototype, constructor)
  );
}

console.log(isInstanceOf(me, Person)); // true
console.log(isInstanceOf(me, Object)); // true
console.log(isInstanceOf(me, Array)); // false
```

따라서 생성자 함수에 의해 프로토타입이 교체되어 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 생성자 함수의 `prototype` 프로퍼티와 프로토타입 간의 연결은 파괴되지 않으므로 `instanceof`는 아무런 영향을 받지 않는다.

## 19.11 직접 상속

### 19.11.1 Object.create에 의한 직접 상속

`Object.create` 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다. 이 방식도 다른 객체 생성 방식과 마찬가지로 추상 연산 `OrdinaryObjectCreate`를 호출한다.

`Object.create` 메서드는 첫 번째 매개변수에 전달한 객체의 프로토타입 체인에 속하는 객체를 생성한다. 즉, 객체를 생성하면서 직접적으로 상속을 구현하는 것이다.  
이 방식의 장점은 다음과 같다.

- `new` 연산자가 없이도 객체를 생성할 수 있다.
- 프로토타입을 지정하면서 객체를 생성할 수 있다.
- 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

그럼에도 이 방식이 권장되지 않는 이유는 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다. 따라서 `Object.prototype`의 빌트인 메서드를 사용할 수 없다.

### 19.11.2 객체 리터럴 내부에서 **proto**에 의한 직접 상속

`Object.create` 메서드의 두 번째 인자로 프로퍼티를 정의하는 것은 매우 번거롭다. 일단 생성 후 추가하는 방법도 있으나 이 또한 깔끔한 방법은 아니다.

ES6에서는 객체 리터럴 내부에서 \_\_proto\_\_ 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.

```js
const myProto = { x: 10 };

const obj = {
  y: 20,
  __proto__: myProto,
};

/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 19.12 정적 프로퍼티/메서드

정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

Person.staticProp = "static Prop";

Person.staticMethod = function () {
  console.log("static Method");
};

const me = new Person("Park");

Person.staticMethod(); // static Method

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

마지막 `TypeError`에서 알 수 있는 것은 정적 프로퍼티/메서드는 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니라는 것이다. 그래서 인스턴스에서 접근할 수 없는 것이다.  
(EX. `Object.entries`, `Object.values` 등)

만약 인스턴스/프로토타입 메서드 내에서 `this`를 사용하지 않는다면 그 메서드는 정적 메서드로 변경할 수 있다.  
그리고 프로토타입 메서드를 호출하라면 인스턴스를 생성해야 하지만 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.

## 19.13 프로퍼티 존재 확인

### 19.13.1 in 연산자

`in` 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다. `in` 연산자의 사용법은 다음과 같다.

```js
const person = {
  name: "Park",
  address: "Seoul",
};

console.log("name" in person); // true
console.log("age" in person); // false
```

`in` 연산자는 확인 대상 객체(우항 피연산자)의 프로퍼티뿐만 아니라 확인 대상 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인하므로 주의가 필요하다.

```js
console.log("toString" in person); // true, Object.prototype.toString

// Reflect.has를 사용할 수도 있음. ES6에서 추가
console.log(Reflect.has(person, "name")); // true
```

### 19.13.2 Object.prototype.hasOwnProperty 메서드

`Object.prototype.hasOwnProperty` 메서드는 이름에서 알 수 있듯이 인수로 전달받은 프로퍼티가 객체 고유의 프로퍼티 키인 경우에만 `true`를 반환하고 상속받은 프로토타입의 프로퍼티 키인 경우 `false`를 반환한다.

```js
console.log(person.hasOwnProperty("name")); // true
console.log(person.hasOwnProperty("toString")); // false
```

## 19.14 프로퍼티 열거

### 19.14.1 for ... in 문

객체의 모든 프로퍼티를 순회하며 열거하려면 `for ... in`문을 사용한다.

```js
const person = {
  name: "Park",
  address: "Seoul",
};

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
// name: Park
// address: Seoul
```

`for ... in` 문은 객체의 프로퍼티 개수만큼 순회하며 상속받은 프로토타입의 프로퍼티까지 열거한다. 하지만 `toString`과 같은 `Object.prototype`의 프로퍼티가 열거되지 않는데 그 이유는 `toString` 메서드가 열거할 수 없도록 정의되어 있는 프로퍼티이기 때문이다.

`Object.prototype.toString` 프로퍼티의 프로퍼티 어트리뷰트 [[Enumerable]]의 값은 `false`이다.

```js
// {value: f, writable: true, enumerable: false, configurable: true}
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "toString"));
```

다시 한 번 정리해 보자면,  
`for ... in` 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 `true`인 프로퍼티를 순회하며 열거한다.

```js
const person = {
  name: "Park",
  address: "Seoul",
  __proto__: { age: 20 },
};

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
// name: Park
// address: Seoul
// age: 20

// 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않음
const sym = new Symbol();
const obj = {
  a: 1,
  [sym]: 10,
};

for (const key in obj) {
  console.log(`${key}: ${obj[key]}`);
}
// a: 1
```

따라서 상속받은 프로퍼티를 제외한 객체 자신의 프로퍼티만 열거하려면 `Object.prototype.hasOwnProperty` 메서드를 사용해 객체 자신의 프로퍼티인지 확인해야 한다.

```js
const person = {
  name: "Park",
  address: "Seoul",
  __proto__: { age: 20 },
};

for (const key in person) {
  if (!person.hasOwnProperty(key)) continue;
  console.log(`${key}: ${person[key]}`);
}

// name: Park
// address: Seoul
```

배열에는 `for ... in` 문을 사용하지 말고 일반적인 `for` 문이나 `for ... of`, `Array.prototype.forEach` 메서드를 사용하기를 권장한다.(배열도 객체이므로 상속받은 프로퍼티가 포함될 수 있다.)

### 19.14.2 Object.keys/values/entries 메서드

객체 자신의 고유 프로퍼티만 열거하기 위해서는 `for ... in` 문을 사용하는 것보다 `Object.keys/values/entries` 메서드를 사용하는 것을 권장한다.

```js
const person = {
  name: "Park",
  address: "Seoul",
  __proto__: { age: 20 },
};

console.log(Object.keys(person)); // ['name', 'address']
console.log(Object.values(person)); // ['Park', 'Seoul']
console.log(Object.entries(person)); // [['name', 'Park'], ['address', 'Seoul']]
```

- `Object.keys`: 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환
- `Object.values`: 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환
- `Object.entires`: 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환
