# This

- 대부분의 경우 함수를 호출한 방법에 의해 결정

- 실행 중에는 할당으로 설정할 수 없고, 함수를 호출할 때 마다 다를 수 있음

- `bind`: 함수를 어떻게 호출했는지 상관하지 않고 this 값을 설정

- `->`: 스스로의 this binding을 제공하지 않음

```js
const test = {
  prop: 42,
  func: function() {
    return this.prop;
  },
};

console.log(test.func());
// expected output: 42
```

## 전역 문맥

- this는 strict mode 관계 없이 전역 객체 참조

```js
// 웹 브라우저에서는 window 객체가 전역 객체
console.log(this === window); // true

a = 37;
console.log(window.a); // 37

this.b = "MDN";
console.log(window.b)  // "MDN"
console.log(b)         // "MDN"
```

## 함수 문맥

- this를 호출한 방법에 따라 좌우

### 단순 호출

- strict mode아닌 경우

```js
function f1() {
  return this;
}

// 브라우저
f1() === window; // true

// Node.js
f1() === global; // true
```

- strict mode인 경우

```js
function f2(){
  "use strict"; // 엄격 모드 참고
  return this;
}

f2() === undefined; // true
```


