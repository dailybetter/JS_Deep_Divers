# TDZ

TDZ( Temporal Dead Zone) 은 '일시적 사각지대'다.

변수가 스코프 내에서 선언되기 전에 접근하려고 할 때 발생하는 에러를 예로들어보면 아래와 같다.

```javascript
console.log(x); // ReferenceError: x is not defined

let x = 10;
```

## TDZ의 영향을 받지 않는 구문

- var
- function
- import

```javascript
hello("word");
// function 구문은 tdz에 영향을 받지 않고 호이스팅 되어 에러가 나지 않는다.
function hello(text) {
  console.lgo(text);
}
```

## 화살표 함수

함수 표현식인 화살표 함수의 경우 TDZ의 영향을 받는다.

```javascript
console.log(add(1, 2)); // ReferenceError: Cannot access 'add' before initialization

const add = (a, b) => {
  return a + b;
};
```
