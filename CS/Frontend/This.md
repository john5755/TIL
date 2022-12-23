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

### Bind

- `f.bind(someObject)`를 호출하면 f와 같은 코드와 같은 범위를 가졌지만 this는 원본 함수를 가진 새로운 함수를 생성
  
  - 새 함수의 this는 호출방식과 상관 없이 `bind()`의 첫 번째 매개변수로 고정

```js
function f(){
    return this.a
}

var g = f.bind({a:'azerty'})
console.log(g()); // azerty

var h = g.bind({a: 'yoo'}); // bind는 한번만 동작
console.log(h()); // azerty

var o = {a: 37, f: f, g: g, h: h};
console.log(o.a, o.f(), o.g(), o.h()); // 37, 37, azerty, azerty
```

### 화살표 함수

- 화살표 함수에서 `this`는 자신을 감싸는 정적 범위

- 전역 코드에서는 전역 객체

```js
var globalObject = this;
var foo = (() => this);
console.log(foo() === globalObject); // true
```

- 화살표 함수를 `call()`, `apply()`, `bind()`를 사용해서 호출할 때 this 값을 정해주더라도 무시한다.

- 사용자가 매개 변수를 정해주는 건 문제 없지만, 첫 번째 매개변수는 `null`을 지정해야 한다.

```js
// 객체로 메서드 호출
var obj = {func: 'foo'};
console.log(obj.func() === globalObject); // true

// call
console.log(foo.call(obj) === globalObject); // true

// bind
foo = foo.bind(obj);
console.log(foo() === globalObject); // true
```

- 어떤 방법을 써도 foo의 this는 생성 시점의 것으로 설정

```js
var obj = {
    bar: function(){
    var x = (() => this);
    return x;
    }
};

var fn = obj.bar();
console.log(fn() === obj); // true

// 호출 없이 obj의 메서드 참조하는 경우
var fn2 = obj.bar;
// 화살표 함수의 this를 bar 메서드 내에서 호출하면
// fn2의 this를 따르므로 window를 반환
console.log(fn2()() == window); // true
```

### 객체의 메서드로서

- 함수를 어떤 객체의 메서드로 호출하면 `this`의 값은 그 객체를 사용한다.

```js
var o = {
    prop: 37,
    f: function(){
    return this.prop
    }
};

console.log(o.f()); // 37
```

- 함수가 정의된 방법이나 위치에 전혀 영향을 받지 않는다.

```js
var o = {prop: 37}

function independent(){
    return this.prop
}


o.f = independent;

console.log(o.f()) // 37


o.b = {g: independent, prop: 42};
console.log(o.b.g()); // 42
```

### 객체의 프로토 타입 체인에서의 this

```js
var o = {
    f: function(){return this.a + this.b;}
};

var p = Object.create(o)
p.a = 1;
p.b = 4;

console.log(p.f()); // 5
```

### 접근자와 설정자의 this

```js
function sum(){
    return this.a + this.b + this.c
}

var o = {
    a:1, b:2, c:3,
    get average(){
        return (this.a + this.b + this.c) / 3;
    }
};

Object.defineProperty(o, 'sum', {
    get: sum, enumberable: true, configurable: true
});

console.log(o.average, o.sum); // 2, 6
```

### 생성자

```js
function C(){
    this.a = 37;
}

var o = new C();
console.log(o.a); // 37

function C2(){
    this.a = 37;
    return {a:38};
}

o = new C2();
console.log(O.a); // 38
```

### DOM 이벤트 처리기

- 함수를 이벤트 처리기로 설정하면 this는 이벤트를 발사한 요소로 설정

- 일부 브라우저는 addEventListner()외의 다른 방법으로 추가한 처리기에 대해서는 X

```js
function bluify(e){
    // 항상 true
    console.log(this === e.currentTarget)
    // curretTarget과 target이 같은 객체면 true
    console.log(this === e.target);
    this.style.backgroundColor = '#A5D9F3';
}

var elements = document.getElementByTagName('*');

for (var i = 0; i < elements.length; i ++){
    elements[i].addEventListner('click', bluify, false);
}
```

### 인라인 이벤트 핸들러

```js
<button onclick="alert(this.tagName.toLowerCase());">
  this 표시
</button>
```

- button을 보여준다.

```js
<button onclick="alert((function() { return this; })());">
  내부 this 표시
</button>
```

- window 객체 반환 (this가 정해지지 않음)
