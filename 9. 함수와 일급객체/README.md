# 함수와 일급객체

## 일급 객체

일급 객체란

1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
2. 변수나 자료구조에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

js 함수는 일급객체다.

## 함수 객체의 프로퍼티

**arguments 프로퍼티**

자바스크립트는 함수의 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.

caller 프로퍼티 : arguments 객체를 생성한 함수 (자기 자신)을 가리킨다. length 프로퍼티 : 인수의 개수를 가리킨다. Symbol(Symbol.iterator) 프로퍼티 : arguments 객체를 순회 가능한 자료구조인 이터러블로 만들기 위한 프로퍼티

****proto**프로퍼티**

모든 객체는 프로토타입이라는 내부 슬롯을 가지며, 이것은 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체를 가리킨다.

**proto** 프로퍼티는 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입 객체에 간접적으로 접근하기 위해 사용하는 접근자 프로퍼티다.

```javascript
const obj = { a: 1 };

// 객체 리터럴 방식으로 생성한 객체의 프로토타입 객체는 Object.prototype이다.
console.log(obj.__proto__ === Object.prototype); // true

// 객체 리터럴 방식으로 생성한 객체는 프로토타입 객체 Object.prototype 프로퍼티를 상속 받는다.
// hasOwnProperty 메서드는 Object.prototype의 메서드다.
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false
```

**prototype 프로퍼티**

생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor 만이 소유하는 프로퍼티

```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty('prototype'); // true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty('property'); / false
```
