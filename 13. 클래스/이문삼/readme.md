# 25 클래스

## 25.1 클래스는 프로토타입의 문법적 설탕인가?

ES5에서는 클래스 없이도 다음과 같이 생성자 함수와 프로토타입을 통해 객체지향 언어의 상속을 구현할 수 있다.

```js
var Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype.sayHi = function () {
    console.log("Hi! My name is " + this.name);
  };

  return Person;
})();

var me = new Person("Park");
me.sayHi(); // Hi! My name is Park
```

ES6에서 도입된 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 자바나 C#같은 클래스 기반 객체지향 프로그래밍에 익숙한 프로그래머가 더욱 빠르게 학습할 수 있도록 클래스 기반 객체지향 프로그래밍 언어와 매우 흡사한 새로운 객체 생성 메커니즘을 제시한다.

사실 클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕이라고 볼 수도 있다.

단, 클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다.

### 25.1.1 둘의 차이

1. 클래스를 `new` 연산자 없이 호출하면 에러가 발생한다. 하지만 생성자 함수를 `new` 연산자 없이 호출하면 일반 함수로서 호출된다.
2. 클래스는 상속을 지원하는 `extends`와 `super` 키워드를 제공한다. 생성자 함수는 지원하지 않는다.
3. 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다. 하지만 함수 선언문으로 정의된 생성자 함수는 함수 호이스팅이, 함수 표현식으로 정의한 생성자 함수는 변수 호이스팅이 발생한다.
4. 클래스 내의 모든 코드에는 암묵적으로 strict mode가 지정되어 실행되며 이를 해제할 수 없다.
5. 클래스의 `constructor`, 프로토타임 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 `false`다. 다시 말해, 열거되지 않는다.

둘은 프로토타입 기반의 객체지향을 구현했다는 점에서 매우 유사하지만 클래스는 생성자 함수 기반의 객체 생성 방식보다 견고하고 명료하다.

따라서 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕이라고 보기보다는 새로운 객체 생성 메커니즘으로 보는 것이 좀 더 합당하다.

## 25.2 클래스 정의

클래스는 `class` 키워드를 사용하여 정의한다. 클래스 이름은 생성자 함수와 마찬가지로 파스칼 케이스를 사용하는 것이 일반적이다.(에러는 아님.)

또한 클래스는 표현식으로 정의할 수 있는 데 이는 클래스가 값으로 사용할 수 있는 일급 객체라는 것을 의미한다.([일급 객체의 특징](../18_함수와%20일급%20객체/박노준.md#181-일급-객체))  
좀 더 자세히 말하면 클래스는 함수다.

클래스 몸체에서 정의할 수 있는 메서드는 `constructor`(생성자), 프로토타입 메서드, 정적 메서드의 세 가지가 있다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 정적 메서드
  static sayHello() {
    console.log("Hello!");
  }
}

const me = new Person("Park");

console.log(me.name); // Park
me.sayHi(); // Hi! My name is Park
Person.sayHello(); // Hello!
```

## 25.3 클래스 호이스팅

클래스는 함수로 평가된다.

```js
console.log(typeof Person); // function
```

클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정, 즉 런타임 이전에 먼저 평가되어 함수 객체를 생성한다.  
이때 클래스가 평가되어 생성된 함수 객체는 생성자 함수로서 호출할 수 있는 함수, 즉 `constructor`다. 생성자 함수로서 호출할 수 있는 함수는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.  
단, 클래스는 클래스 정의 이전에 참조할 수 없다. 그리고 마치 호이스팅이 발생하지 않는 것 처럼 보이나 그렇지 않다.

```js
const Person = "";

{
  // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
  console.log(Person); // ReferenceError: Cannot access 'Person' before initialization

  class Person {}
}
```

이는 클래스도 `let`, `const` 키워드로 선언한 변수처럼 호이스팅 되지만 TDZ에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작하기 때문이다.

## 25.4 인스턴스 생성

클래스는 생성자 함수이며 `new` 연산자와 함께 호출되어 인스턴스를 생성한다.  
그리고 클래스는 인스턴스를 생성하는 것이 유일한 존재 이유이므로 반드시 `new` 연산자와 함께 호출해야 한다.

## 25.5 메서드

> 책이 만들어진 년도에는 클래스 몸체에 프로퍼티를 직접 정의할 수 있는 사양이 나오기 전이었지만 지금은 프로퍼티 앞에 private 접근자까지도 선언할 수 있으니 알아두자.

### 25.5.1 constructor

`constructor`는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다. 이것은 이름을 변경할 수 없다.

```js
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

클래스는 평가되어 함수 객체가 된다. 따라서 함수 객체 고유의 프로퍼티를 모두 갖고 있으며 프로토타입과 연결되어 있고 자신의 스코프 체인을 구성한다.

모든 함수 객체가 가지고 있는 `prototype` 프로퍼티가 가리키는 프로토타입 객체의 `constructor` 프로퍼티는 클래스 자신을 가리키고 있으며 이는 클래스가 인스턴스를 생성하는 생성자 함수라는 것을 의미한다.

클래스가 생성한 인스턴스의 내부를 확인해보면 `this`에 추가한 `name` 프로퍼티가 인스턴스의 프로퍼티로 추가된 것을 확인할 수 있다. `constructor` 내부의 `this`는 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스를 가리킨다.

여기서 `constructor`는 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다. 다시 말해, 클래스 정의가 평가되면 `constructor`에 기술된 동작을 하는 함수 객체가 생성된다.

`constructor`는 생성자 함수와 유사하지만 몇 가지 차이가 있다.

1. 클래스 내에 최대 한 개만 존재할 수 있다. 2개 이상의 `constructor`를 포함하면 문법 에러가 발생한다.
2. `constructor`는 생략할 수 있다.  
   생략했다면 다음과 같이 빈 `constructor`가 암묵적으로 정의된다. 이것을 생략한 클래스는 빈 객체를 생성한다.

인스턴스를 생성할 때 클래스 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 `constructor`에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달하면 된다. 이때 초기값은 `constructor`의 매개변수에게 전달된다.

```js
class Person {
  constructor(name, address) {
    this.name = name;
    this.address = address;
  }
}

const me = new Person("Park", "Seoul");
```

이처럼 `constructor` 내에서는 인스턴스의 생성과 동시에 인스턴스 프로퍼티 추가를 통해 인스턴스의 초기화를 실행하므로 인스턴스를 초기화하려면 이것을 생략해서는 안 된다.

그리고 별도의 반환문을 갖지 않아야한다. `new` 연산자와 함께 클래스가 호출되면 생성자 함수와 동일하게 암묵적으로 `this`, 즉 인스턴스를 반환하기 때문이다.  
다른 객체를 명시적으로 반환하면 인스턴스가 반환되지 못하고 `return` 문에 명시한 객체가 반환된다.(단, 원시값 반환은 무시됌)

### 25.5.2 프로토타입 메서드

생성자 함수에 의한 객체 생성 방식과는 달리 클래스 몸체에서 정의한 메서드는 클래스의 `prototype` 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.

그리고 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

```js
Object.getPrototypeOf(me) === Person.prototype; // true
me instanceof Person; // true

Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
me instanceof Object; // true

me.constructor === Person; // true
```

이처럼 클래스 몸체에서 정의한 메서드는 인스턴스의 프로토타입에 존재하는 프로토타입 메서드가 된다. 인스턴스는 프로토타입 메서드를 상속받아 사용할 수 있다.

결국 클래스는 생성자 함수와 같이 인스턴스를 생성하는 생성자 함수라고 볼 수 있다. 다시 말해, 클래스는 생성자 함수와 마찬가지로 프로토타입 기반의 객체 생성 메커니즘이다.

### 25.5.3 정적 메서드

정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.  
클래스에서는 메서드에 `static` 키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.

```js
class Person {
  static sayHi() {
    console.log("Hi!");
  }
}

Person.sayHi(); // Hi!
```

이처럼 정적 메서드는 클래스에 바인딩된 메서드가 된다. 클래스는 함수 객체로 평가되므로 자신의 프로퍼티/메서드를 소유할 수 있다.  
그리고 앞서 봤던 것처럼 클래스는 클래스 정의가 평가되는 시점에 함수 객체가 되므로 인스턴스와 달리 별다른 생성 과정이 필요 없다.

반대로 정적 메서드는 인스턴스로 호출할 수 없다. 정적 메서드가 바인딩된 클래스는 인스턴스의 프로토타입 체인 상에 존재하지 않기 때문이다.

```js
const me = new Person("Park");
me.sayHi(); // TypeError: me.sayHi is not a function
```

### 25.5.4 정적 메서드와 프로토타입 메서드의 차이

1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 가능하다.(`this`를 사용하는 것을 말함)

메서드 내부의 `this`는 메서드를 소유한 객체가 아니라 메서드를 호출한 객체, 즉 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체에 바인딩된다.

프로토타입 메서드는 인스턴스로 호출해야 하므로 프로토타입 메서드 내부의 `this`는 프로토타입 메서드를 호출한 인스턴스를 가리킨다.  
반대로 정적 메서드는 클래스로 호출해야 하므로 정적 메서드 내부의 `this`는 인스턴스가 아닌 클래스를 가리킨다.

> `this`를 사용할 경우엔 무조건 프로토타입 메서드  
> 아닌 경우엔 둘 다 가능하지만 인스턴스를 생성하지 않아도 되는 정적 메서드가 좋음

### 25.5.5 클래스에서 정의한 메서드의 특징

1. `function` 키워드를 생략한 메서드 축약 표현을 사용한다.
2. 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
3. 암묵적으로 strict mode로 실행된다.
4. `for ... in` 문이나 `Object.keys` 메서드 등으로 열거할 수 없다. ([[Enumerable]]의 값이 `false`임.)
5. 내부 메서드 [[Construct]]를 갖지 않는 non-constructor다. 따라서 `new` 연산자와 함께 호출할 수 없다.

## 25.6 클래스의 인스턴스 생성 과정

`new` 연산자와 함께 클래스를 호출하면 생성자 함수와 마찬가지로 클래스의 내부 메서드 [[Construct]]가 호출된다. 클래스는 `new` 연산자 없이 호출할 수 없다.

1. 인스턴스 생성과 this 바인딩  
   `new` 연산자와 함께 클래스를 호출하면 `constructor`의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체가 생성된다. 이 객체가 인스턴스다. 클래스가 생성한 인스턴스의 프로토타입으로 클래스의 `prototype` 프로퍼티가 가리키는 객체가 설정된다.  
   암묵적으로 생성된 빈 객체, 즉 인스턴스는 `this`에 바인딩된다. 따라서 `constructor` 내부의 `this`는 클래스가 생성한 인스턴스를 가리킨다.
2. 인스턴스 초기화  
   `constructor`의 내부 코드가 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다. `this`에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 인수로 전달받은 초기값으로 프로퍼티의 값을 초기화한다.  
   만약 `constructor`가 생략되었다면 이 과정도 생략된다.
3. 인스턴스 반환  
   클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.

   ```js
   class Person {
     constructor(name) {
       // 1. 인스턴스 생성 후 this 바인딩
       console.log(this); // Person {}
       console.log(Object.getPrototypeOf(this) === Person.prototype); // true

       // 2. 인스턴스 초기화
       this.name = name;

       // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환됨
     }
   }
   ```

## 25.7 프로퍼티

### 25.7.1 인스턴스 프로퍼티

인스턴스 프로퍼티는 `constructor` 내부에서 정의해야 한다.

> 지금은 아니다. 이미 표준으로 채택되어 클래스 내부에 프로퍼티를 선언할 수 있다.

`constructor`에서 클래스가 암묵적으로 생성한 빈 객체, 즉 인스턴스에 프로퍼티가 추가되어 인스턴스가 초기화된다.

### 25.7.2 접근자 프로퍼티

접근자 프로퍼티는 자체적으로는 값([[Value]] 내부 슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

```js
// Object
const person = {
  firstName: 'Nojun',
  lastName: 'Park',

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ')
  }
}

console.log(person.fullName) // Nojun Park
person.fullName = `Something Wrong`;
console.log(person.fullName) // Something Wrong

// Class
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ')
  }
}

const me = new Person('Nojun', 'Park')

console.log(me.fullName) // Nojun Park
me.fullName = `Something Wrong`;
console.log(me.fullName) // Something Wrong
```

접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수, 즉 getter 함수와 setter 함수로 구성되어 있다.  
이때 getter와 setter 이름은 인스턴스 프로퍼티처럼 사용된다. 다시 말해 호출하는 것이 아니라 프로퍼티처럼 참조(또는 할당)하는 형식으로 사용한다.  
getter는 반드시 무언가를 반환해야 하고 setter는 반드시 매개변수가 있어야 한다.(1개만 가능)

클래스의 접근자 프로퍼티또한 인스턴스가 아닌 프로토타입의 프로퍼티가 된다.

### 25.8 상속에 의한 클래스 확장

### 25.8.1 클래스 상속과 생성자 함수 상속

상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것이다.  
생성자 함수는 의사 클래스 상속 패턴을 사용하는 방법 밖에는 없다.

```js
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() {
    return "eat";
  }

  move() {
    return "move";
  }
}

class Bird extends Animal {
  fly() {
    return "fly";
  }
}

const bird = new Bird(1, 5);

console.log(bird); // Bird {age: 1, weight: 5}
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); // fly
```

### 25.8.2 extends 키워드

상속을 통해 클래스를 확장하려면 `extends` 키워드를 사용하여 상속받을 클래스를 정의한다.  
상속을 통해 확장된 클래스를 서브클래스라 부르고, 서브클래스에게 상속된 클래스를 수퍼클래스라 부른다. 마찬가지로 클래스도 프로토타입을 통해 상속 관계를 구현한다.  
수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다. 이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

### 25.8.3 동적 상속

`extends` 키워드는 클래스 뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다. 단, `extends` 키워드 앞에는 반드시 클래스가 와야 한다.

```js
function Base(a) {
  this.a = a;
}

class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived {a: 1}
```

`extends` 키워드 다음에는 클래스뿐만이 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. 이를 통해 동적으로 상속받을 대상을 결정할 수 있다.

```js
function base1() {}

class Base2 {}

let condition = true;

class Derived extneds (condition? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1) // true
console.log(derived instanceof Base2) // true
```

### 25.8.4 서브클래스의 constructor

클래스에서 `constructor`를 생략하면 클래스에 다음과 같이 비어있는 `constructor`가 암묵적으로 정의된다.

서브클래스에서 생략하면 다음과 같은 `constructor`가 정의된다.

```js
constructor( ...args ) { super(...args)}
```

`super()`는 수퍼클래스의 `constructor`를 호출하여 인스턴스를 생성한다.

### 25.8.5 super 키워드

`super` 키워드는 함수처럼 호출할 수도 있고 `this`와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.

- `super`를 호출하면 수퍼클래스의 `constructor`(super-constructor)를 호출한다.
- `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

#### super 호출

`super`를 호출하면 수퍼클래스의 `constructor`를 호출한다.

다음 예제와 같이 수퍼클래스의 constructor 내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생선한다면 서브클래스의 constructor를 생략할 수 있다. 이때 `new` 연산자와 함께 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암묵적으로 정의된 `constructor`의 `super` 호출을 통해 수퍼클래스의 `constructor`에 전달된다.

```js
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

class Derived extends Base {
  // construtor(...args) { super(...args); }
  constructor(a, b, c) {
    super(a, b);
    this.c = c;
  }
}

const derived = new Derived(1, 2, 3);
console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

만약 서브클래스에서 추가한 프로퍼티를 갖는 인스넡스를 생성한다면 서브클래스의 `constructor`를 생략할 수 없다.  
이때는 수퍼클래스의 `constructor`에게 전달할 인수를 `super`를 통해 전달하면 된다.

`super`를 호출할 때 주의할 사항은 다음과 같다.

1. 서브클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.

   ```js
   class Base() {}

   class Derived extends Base {
    constructor() {
      // ReferenceError
      console.log('constructor call')
    }
   }
   ```

2. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.

   ```js
   class Base {}

   class Derived extends Base {
     constructor() {
       // ReferenceError
       this.a = 1;
       super();
     }
   }
   ```

3. super는 반드시 서브클래스의 constructor에서만 호출한다.

#### super 참조

메서드 내에서 `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

1. 서브클래스의 프로토타입 메서드 내에서 `super.sayHi`는 수퍼클래스의 프로토타입 메서드 `sayHi`를 가리킨다.

   ```js
   class Base {
     constructor(name) {
       this.name = name;
     }

     sayHi() {
       return `Hi! ${this.name}`;
     }
   }

   class Derived extends Base {
     sayHi() {
       // __super는 Base.prototype을 가리킨다.
       const __super = Object.getPrototypeOf(Derived.prototype);
       return `${__super.sayHi.call(this)} how are you doing?`;
       // return `${super.sayHi(this)} how are you doing?`;
     }
   }
   ```

   super는 자신을 참조하고 있는 메서드(`Derived`의 `sayHi`)가 바인딩되어 있는 객체(`Derived.prototype`)의 프로토타입(`Base.prototype`)을 가리킨다.  
   따라서 `super.sayHi`는 `Base.prototype.sayHi`를 가리킨다. 단, `call` 메서드를 사용해 `this`를 전달해야 한다.  
   이유는 `Base.prototype.sayHi` 메서드가 프로토타입 메서드이기 때문에 내부의 `this`는 `Base.prototype`이 아닌 인스턴스를 가리켜야하기 때문이다.

   이처럼 `super` 참조가 동작하기 위해서는 `super`를 참조하고 있는 메서드(`Derived`의 `sayHi`)가 바인딩되어 있는 객체(`Derived.prototype`)의 프로토타입 (`Base.prototype`)을 찾을 수 있어야 한다. 이를 위해 메서드는 내부 슬롯 [[HomeObject]]를 가지며, 자신을 바인딩하고 있는 객체를 가리킨다.

   ```js
   /*
   [[HomeObject]]는 메서드 자신을 바인딩하고 있는 객체를 가리킨다.
   [[HomeObject]]를 통해 메서드 자신을 바인딩하고 있는 객체의 프로토타입을 찾을 수 있다.
   예를 들어, Derived 클래스의 sayHi 메서드는 Derived.prototype에 바인딩되어 있다.
   따라서 Derived 클래스의 sayHi 메서드의 [[HomeObject]]는 Derived.prototype이고
   이를 통해 Derived 클래스의 sayHi 메서드 내부의 super 참조가 Base.prototype으로 결정된다.
   따라서 super.sayHi는 Base.prototype.sayHi를 가리키게 된다.
   */
   super = Object.getPrototypeOf([[HomeObject]])
   ```

   주의할 것은 ES6의 메서드 축약 표현으로 정의된 함수만이 [[HomeObject]]를 갖는다는 것이다.  
   그리고 `super` 참조는 클래스의 전유물은 아니다. 객체 리터럴에서도 `super` 참조를 사용할 수 있다.

2. 서브클래스의 정적 메서드 내에서 `super.sayHi`는 수퍼클래스의 정적 메서드 `sayHi`를 가리킨다.

   ```js
   class Base {
     static sayHi() {
       return "Hi!";
     }
   }

   class Derived extends Base {
     static sayHi() {
       return `${super.sayHi()} how are you doing?`;
     }
   }

   console.log(Derived.sayHi());
   ```

### 25.8.6 상속 클래스의 인스턴스 생성 과정

```js
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }

  toString() {
    return `width = ${this.width}, height = ${this.height}`;
  }
}

class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);
    this.color = color;
  }

  // overriding
  toString() {
    return super.toString() + `, color = ${this.color}`;
  }
}

const colorRectangle = new ColorRectangle(2, 4, "red");
console.log(colorRectangle); // ColorRectangle {width: 2, height: 4. color: 'red'}

console.log(colorRectangle.getArea()); // 8
console.log(colorRectangle.toString()); // width = 2, height = 4, color = red
```

#### 1. 서브클래스의 super 호출

엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 "base" 또는 "derived"를 값으로 갖는 내부 슬롯 [[ConstructorKind]]를 갖는다. 상속 받지 않는 클래스(생성자 함수)는 내부 슬롯의 값이 "base"로 설정되만 다른 클래스를 상속받는 서브클래스는 "derived"로 설정된다.  
이를 통해 수퍼클래스와 서브클래스는 `new` 연산자와 함께 호출되었을 때의 동작이 구분된다.

서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임한다. 이것이 바로 서브클래스의 `constructor`에서 반드시 `super`를 호출해야 하는 이유다.
좀 더 정확히 말하자면 수퍼클래스가 평가되어 생성된 함수 객체의 코드가 실행되기 시작한다.

#### 2. 수퍼클래스의 인스턴스 생성과 this 바인딩

수퍼클래스의 `constructor` 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다. 그리고 암묵적으로 생성된 빈 객체, 즉 인스턴스는 `this`에 바인딩된다. 따라서 수퍼클래스의 `constructor` 내부에 `this`는 생성된 인스턴스를 가리킨다.

```js
class Rectangle {
  constructor(width, height) {
    console.log(this); // ColorRectangle {}
    console.log(new.target); // ColorRectangle

    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
    console.log(this instanceof ColorRectangle); // true
    console.log(this instanceof Rectangle); // true
  }
}
```

`new` 연산자와 함께 호출된 함수를 가리키는 `new.target`은 서브클래스를 가리킨다. 따라서 인스턴스는 `new.target`이 가리키는 서브클래스가 생성한 것으로 처리된다.

#### 3. 수퍼클래스의 인스턴스 초기화

수퍼클래스의 `constructor`가 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다.  
인스턴스에 프로퍼티를 추가하고 `constructor`가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

```js
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;

    console.log(this); // ColorRectangle {width: 2, height: 4}
  }
}
```

#### 4. 서브클래스 constructor로의 복귀와 this 바인딩

`super`의 호출이 종료되고 제어 흐름이 서브클래스 `constructor`로 돌아온다. 이때 `super`가 반환한 인스턴스가 `this`에 바인딩된다.

```js
class ColorRectangle {
  constructor(width, height, color) {
    super(width, height);

    console.log(this); // ColorRectangle {width: 2, height: 4}
  }
}
```

앞서 살펴본 서브클래스의 `constructor`에서 `super`를 호출하기 전에 `this`를 참조할 수 없는 이유가 바로 이 때문이다.

#### 5. 서브클래스의 인스턴스 초기화

`super` 호출 이후, 서브클래스의 `constructor`에 기술되어 있는 인스턴스 초기화가 실행된다. 즉, `this`에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 `constructor`가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

#### 6. 인스턴스 반환

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.

### 25.8.7 표준 빌트인 생성자 함수 확장

`extends` 키워드 다음에는 클래스뿐만이 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. `String`, `Number`, `Array` 같은 표준 빌트인 객체도 해당된다.

```js
class MyArray extends Array {
  static get [Symbol.species]() {
    return Array;
  }

  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);

console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
console.log(myArray.average()); // 1.75
```

이때 주의할 것은 `Array.prototype`의 메서드 중에서 `map`, `filter`와 같이 새로운 배열을 반환하는 메서드가 `MyArray` 클래스의 인스턴스를 반환한다는 것이다.(예제에선 `uniq`가 Array.prototype.filter를 사용한다.)  
만약 새로운 배열을 반환하는 메서드가 `MyArray` 클래스의 인스턴스를 반환하지 않고 `Array`의 인스턴스를 반환하면 `MyArray` 클래스의 메서드와 메서드 체이닝이 불가능하다.

만약 `Array`가 생성한 인스턴스를 반환하게 하려면 맨 윗줄 `get` 선언을 사용해 정적 접근자 프로퍼티를 추가해야한다.

```js
console.log(myArray.uniq().average()); // TypeError
```
