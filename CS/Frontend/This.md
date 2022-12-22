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

- this의 값을 한 문맥에서 다른 문맥으로 넘기려면 call()이나 apply()사용

```js
// call 또는 apply의 첫 번째 인자로 객체가 전달 될 수 있으며 this가 그 객체에 묶이
let obj = {a: 'Custom'}

// 변수를 선언하고 변수에 프로퍼티로 전역 window 할당
let a = 'Global'

function whatsThis(){
    return this.a
}

whatsThis(); // this는 'Global'
whatsThis.call(obj); // this는 'Custom'
whatsThis.apply(obj); // this는 'Custom'
```

```js
function add(c,d){
    return this.a + this.b + c + d
}

let o = {a:1, b:3};

// 첫 번째 인자는 'this'로 사용할 객체
// 이어지는 인자들은 함수 호출에서 인자로 전달
add.call(o, 5, 7); // 16

// 두 번째 인자는 함수 호출에서 인수로 사용될 멤버들이 위치한 배열
add.apply(o, [10,20]); // 34
```

- 비엄격모드에서 `this`로 전달된 값이 객체가 아닌 경우 call이나 apply는 이를 변환하기 위한 시도를 함.
  
  - null이나 undefined 값은 전역 객체가 됨
  
  - 7이나 'foo'같은 원시값은 관련된 생성자를 사용해 객체로 변환
    
    - 7은 new Number(7)에 의해 객체로 변환
    
    - 문자열 'foo'는 new String('foo')에 의해 객체로 변환
  
  ```js
  function bar(){
      console.log(Object.prototype.toString(call(this));
  }
  
  bar.call(7); // [object Number]
  bar.call('foo'); // [object String]
  bar.call(undefined) // [object Global]
  ```
