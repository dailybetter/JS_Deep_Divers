# 24 클로저

클로저는 자바스크립트 고유의 개념이 아니다. 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어에서 사용되는 중요한 특성이다.

"클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다."

여기서 가장 중요한 키워드는 함수가 선언된 렉시컬 환경이다.

```js
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

function notInnerFunc() {
  console.log(x); // 1
}

outerFunc();
```

`outerFunc` 함수 내부에서 중첩 함수 `innerFunc`가 정의되고 호출되었다. 이때 중첩 함수 `innerFunc`의 상위 스코프는 외부 함수의 스코프다. 따라서 중첩 함수 내부에서 자신을 포함하고 있는 외부 함수의 `x` 변수에 접근할 수 있다.

## 24.1 렉시컬 스코프

자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라 한다.

```js
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo();
bar();
```

`foo`와 `bar` 함수 모두 전역에서 정의된 전역 함수다. 함수의 상위 스코프는 함수를 어디서 정의했느냐에 따라 결정되므로 둘의 상위 스코프는 전역이다.  
따라서 어디서 호출하더라도 상위 스코프는 전역이고 이것에 어떠한 영향도 주지 못한다.

앞서 살펴보았듯이 스코프의 실체는 실행 컨텍스트의 렉시컬 환경이다. 이 렉시컬 환경은 자신의 "외부 렉시컬 환경에 대한 참조"를 통해 상위 렉시컬 환경과 연결된다. 이것이 바로 스코프 체인이다.

따라서 함수의 상위 스코프를 결정한다는 것은 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조 값을 결정한다는 것과 같다. 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값이 바로 상위 렉시컬 환경에 대한 참조이며 이것이 상위 스코프이기 때문이다.

렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 의해 결정된다. 이것이 바로 렉시컬 스코프다.

## 24.2 함수 객체의 내부 슬롯 [[Environment]]

렉시컬 스코프가 가능하려면 함수는 자신이 호출되는 환경과는 상관없이 자신이 정의된 환경, 즉 상위 스코프를 기억해야 한다.

이를 위해 함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.  
이때 자신의 내부 슬롯 [[Envirionment]]에 저장된 상위 스코프의 참조는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킨다. 또한 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장될 참조 값이다.

함수 객체는 내부 슬롯에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 자신이 존재하는 한 기억한다.

함수가 호출돼 코드를 평가하는 순서에서 외부 렉시컬 환경에 대한 참조에는 함수 객체의 내부 슬롯 [[Environment]]에 저장된 렉시컬 환경의 참조가 할당된다. 즉, 함수 객체의 내부 슬롯에 저장된 렉시컬 환경의 참조는 바로 함수의 상위 스코프를 의미한다.

## 24.3 클로저와 렉시컬 환경

```js
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };

  return inner;
}

const innerFunc = outer();
innerFunc();
```

`outer`를 호출하면 이것은 `inner`를 반환하고 생명 주기를 마감한다. 즉, 실행이 종료되면 `outer` 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거되고 변수 `x` 또한 생명 주기를 마감한다.  
따라서 `outer` 함수의 지역 변수 `x`는 더는 유효하지 않게 되어 변수에 접근할 수 있는 방법은 달리 없어 보인다.

그러나 `innerFunc`의 실행 결과는 10이다.

이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 **중첩 함수를 클로저**<sup>closure</sup>라고 부른다.

"클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다."라는 정의에서 그 함수가 선언된 렉시컬 환경이란 함수가 정의된 위치의 스코프, 즉 상위 스코프를 의미하는 실행 컨텍스트의 렉시컬 환경을 말한다.

예제에서 `outer` 함수가 평가되어 함수 객체를 생성할 때 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 전역 렉시컬 환경을 함수 객체의 [[Environment]] 내부 슬롯에 상위 스코프로서 저장한다.

그리고 `outer` 함수를 호출하면 함수의 렉시컬 환경이 생성되고 앞서 해당 슬롯에 저장된 전역 렉시컬 환경을 `outer` 함수의 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 할당한다.

그리고 `inner`가 평가된다.(함수 표현식으로 정의했기 때문에 런타임에 평가된다.) 이때 중첩 함수 `inner`는 자신의 [[Environment]] 내부 슬롯에 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 `outer` 함수의 렉시컬 환경을 상위 스코프로서 저장한다.

`outer` 함수의 실행 컨텍스트가 스택에서 제거되더라도 렉시컬 환경까지 소멸하는 것은 아닌 것이다. `inner` 함수의 [[Environment]] 내부 슬롯에 의해 참조되고 있고 `inner`는 전역 변수 `innerFunc`에 의해 참조되고 있기 때문에 가비지 컬렉션의 대상이 되지 않기 때문이다.

중첩 함수 `inner`는 외부 함수 `outer`보다 더 오래 생존했다. 이때 외부 함수보다 더 오래 생존한 중첩 함수는 외부 함수의 생존 여부(실행 컨텍스트의 생존 여부)와 상관없이 자신이 정의된 위치에 의해 결정된 상위 스코프를 기억한다.  
따라서 상위 스코프의 식별자를 참조할 수 있고 식별자의 값을 변경할 수도 있다.

자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다. 하지만 일반적으로 모든 함수를 클로저라고 하지는 않는다.  
상위 스코프의 어떤 식별자도 참조하지 않는 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프를 기억하지 않는다.(debugger를 사용해보면 Closure 항목이 없음)

또 다른 케이스로 상위 스코프의 식별자를 참조하고 있어도 외부 함수보다 중첩 함수의 생명 주기가 짧다면 외부 함수의 생명 주기가 종료된 이후 그것의 식별자를 참조할 수 없는 것이 있다. 따라서 이 경우도 클로저라고 하지 않는다.

따라서 클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.  
다만 외부 함수의 특정 식별자만 참조하고 있는 경우 해당 식별자만을 기억한다.(이것 또한 브라우저의 최적화)

이때 클로저에 의해 참조되는 상위 스코프의 변수를 자유 변수라고 부른다. 클로저란 "함수가 자유 변수에 대해 닫혀있다."라는 의미다.

## 24.4 클로저의 활용

클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다. 의도치 않게 변경되지 않도록 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용한다.

```js
let num = 0;

const increase = function () {
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 의도한 대로 동작하려면 다음의 전제조건이 지켜져야 한다.

1. 카운트 상태는 increase 함수가 호출되기 전까지 변경되지 않고 유지되어야 한다.
2. 이를 위해 카운트 상태는 increase 함수만이 변경할 수 있어야 한다.

하지만 카운트 상태는 전역 변수를 통해 관리되고 있기 때문에 언제든지 누구나 접근할 수 있고 변경할 수 있다.(암묵적 결함) 이는 의도치 않게 상태가 변경될 수 있다는 것을 의미한다.

따라서 카운트 상태를 안전하게 변경하고 유지하기 위해서는 `increase` 함수만이 `num` 변수를 참조하고 변경할 수 있게 하는 것이 바람직하다.

```js
const increase = (function () {
  let num = 0;

  return function () {
    return ++num;
  };
})();

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 실행되면 즉시 실행 함수가 호출되고 즉시 실행 함수가 반환한 함수가 `increase` 변수에 할당된다.  
`increase` 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 즉시 실행 함수의 렉시컬 환경을 기억하는 클로저다.

즉시 실행 함수는 호출된 이후 소멸되지만 즉시 실생 함수가 반환한 클로저는 `increase` 변수에 할당되어 호출된다.  
그리고 즉시 실행 함수는 한 번만 실행되므로 `increase`가 호출될 때마다 `num` 변수가 재차 초기화될 일은 없을 것이고 외부에서 직접 접근할 수 없는 변수이므로 의도되지 않은 변경을 걱정할 필요도 없다.

이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지하기 위해 사용한다.

```js
// 클로저의 생성자 함수 버전
const Counter = (function () {
  let num = 0;

  function Counter() {}

  Counter.prototype.increase = function () {
    return ++num;
  };
  Counter.prototype.decrease = function () {
    return --num;
  };

  return Counter;
})();

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2
console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

만약 `num`이 생성자 함수 `Counter`가 생성할 인스턴스의 프로퍼티라면 인스턴스를 통해 외부에서 접근이 자유로운 `public` 프로퍼티가 된다. 하지만 즉시 실행 함수 내에서 선언된 `num` 변수는 인스턴스를 통해 접근할 수 없으며, 즉시 실행 함수 외부에서도 접근할 수 없는 은닉된 변수다.

`increase`, `decrease` 메서드는 모두 사진의 함수 정의가 평가되어 함수 객체가 될 때 실행 중인 실행 컨텍스트인 즉시 실행 함수 실행 컨텍스트의 렉시컬 환경을 기억하는 클로저다. 따라서 프로토타입을 통해 상속되는 프로토타입 메서드일지라도 즉시 실행 함수의 자유 변수 `num`을 참조할 수 있다.

외부 상태 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에서 부수 효과를 최대한 억제하여 오류를 피하고 프로그램의 안전성을 높이기 위해 클로저는 적극적으로 사용된다.

```js
function makeCounter(aux) {
  let counter = 0;

  return function () {
    counter = aux(counter);
    return counter;
  };
}

function increase(n) {
  return ++n;
}

function increase2(n) {
  return n + 2;
}

const increaser1 = makeCounter(increase);

console.log(increaser()); // 1
console.log(increaser()); // 2

const increaser2 = makeCounter(increase2);

console.log(increaser2()); // 2
console.log(increaser2()); // 4
```

`makeCounter` 함수는 인자로 전달받은 보조 함수를 합성하여 자신이 반환하는 함수의 동작을 변경할 수 있다. 이때 주의해아 할 것은 `makeCounter` 함수를 호출해 함수를 반환할 때 반환된 함수는 자신만의 독립된 렉시컬 환경을 갖는다는 것이다.

만약 `counter`를 공유하려면 함수를 두 번 호출하지 말아야한다. 이는 렉시컬 환경을 공유하는 클로저를 만들기 위함이다.

```js
const counter = (function () {
  let counter = 0;

  return function (aux) {
    counter = aux(counter);
    return counter;
  };
})();

function increase(n) {
  return ++n;
}

function increase2(n) {
  return n + 2;
}

console.log(counter(increaser)); // 1
console.log(counter(increaser)); // 2

console.log(counter(increaser2)); // 4
console.log(counter(increaser2)); // 6
```

## 24.5 캡슐화와 정보 은닉

캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다.  
캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라 한다.

정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개하지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호하고, 객체 간의 상호 의존성, 즉 결합도를 낮추는 효과가 있다.

다른 객체지향 프로그래밍 언어는 클래스를 정의하고 그 클래스를 구성하는 멤버에 대하여 접근 제한자를 선언해 공개 범위를 한정할 수 있지만 자바스크립트는 이것을 제공하지 않는다.

```js
const Person = (function () {
  let _age = 0; // private

  function Person(name, age) {
    this.name = name; // public
    _age = age;
  }

  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}, I am ${_age}.`);
  };

  return Person;
})();

const me = new Person("Park", 27);
me.sayHi(); // Hi! My name is Park, I am 27.
console.log(me.name); // Park
console.log(me._age); // undefined
```

하지만 위 코드는 문제가 있다. `Person` 생성자 함수가 여러 개의 인스턴스를 생성할 경우 다음과 같이 `_age` 변수의 상태가 유지되지 않는다는 것이다.

```js
const me = new Person("Park", 27);
me.sayHi(); // Hi! My name is Park, I am 27.

const you = new Person("Lee", 30);
you.sayHi(); // Hi! My name is Lee, I am 30.

me.sayHi(); // Hi! My name is Park, I am 30.
```

이는 `Person.prototype.sayHi` 메서드가 단 한 번 생성되는 클로저이기 때문에 발생하는 현상이다. 해당 메서드는 즉시 실행 함수가 호출될 때 생성된다. 이때 메서드는 자신의 상위 스코프인 즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경의 참조를 [[Environment]]에 저장하여 기억한다. 따라서 `Person` 생성자 함수의 모든 인스턴스가 상속을 통해 호출할 수 있는 `Person.prototype.sayHi` 메서드의 상위 스코프는 어떤 인스턴스로 호출하더라도 하나의 동일한 상위 스코프를 사용하게 된다.

이처럼 자바스크립트는 정보 은닉을 완전하게 지원하지 않는다. 인스턴스 메서드를 사용한다면 자유 변수를 통해 `private`를 흉내 낼 수는 있지만 프로토타입 메서드를 사용한다면 이마저도 불가능해 진다.

## 24.6 자주 발생하는 실수

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // 3 3 3
}
```

`for` 문의 변수 선언문에서 `var` 키워드로 선언한 `i` 변수는 블록 레벨 스코프가 아닌 함수 레벨 스코프를 갖기 때문에 전역 변수다. 따라서 해당 함수를 호출하면 전역 변수 `i`를 참조하여 3이 출력된다.

위 예시를 클로저를 사용해 수정한다면

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // 3 3 3
}
```

즉시 실행 함수는 전역 변수 `i`에 현재 할당되어 있는 값을 인수로 전달받아 매개변수 `id`에 할당한 후 중첩 함수를 반환하고 종료된다.

이때 즉시 실행 함수의 매개변수 `id`는 즉시 실행 함수가 반환한 중첩 함수의 상위 스코프에 존재한다. 즉시 실행 함수가 반환한 중첩 함수는 자신의 상위 스코프(즉시 실행 함수의 렉시컬 환경)를 기억하는 클로저이고, 매개변수 `id`는 즉시 실행 함수가 반환한 중첩 함수에 묶여 있는 자유 변수가 되어 그 값이 유지 된다.

위 예제는 자바스크립트의 함수 레벨 스코프 특성으로 인해 `for` 문의 변수 선언문에서 `var` 키워드로 선언한 변수가 전역 변수가 되기 때문에 발생하는 현상이다.(let쓰면 됌)

이때 함수의 상위 스코프는 `for` 문의 코드 블록이 반복 실행될 때마다 식별자(`for` 문의 변수 선언문에서 선언한 초기화 변수 및 `for` 문의 코드 블록 내에서 선언한 지역 변수 등)의 값을 유지해아 한다. 이를 위해 `for` 문이 반복될 때마다 독립적인 렉시컬 환경을 생성하여 식별자의 값을 유지한다.

`for`문이 평가되면 새로운 렉시컬 환경<sup>LOOP Lexical Environment</sup>을 생성하고 초기화 변수 식별자와 값을 등록한다. 그리고 새롭게 생성된 렉시컬 환경을 현재 실행 중인 실행 컨텍스트의 렉시컬 환경으로 교체한다.

코드 블록이 반복 실행되기 시작하면 새로운 렉시컬 환경<sup>PER-ITERATION Lexical Environment</sup>을 생성하고 `for` 문 코드 블록 내의 식별자와 값을 등록한다.

이처럼 `let`이나 `const` 키워드를 사용하는 반복문은 코드 블록을 반복 실행할 때마다 새로운 렉시컬 환경을 생성하여 반복할 당시의 상태를 마치 스냅숏을 찍는 것처럼 저장한다. 단, 이는 반복문의 코드 블록 내부에서 함수를 정의할 때 의미가 있다.
