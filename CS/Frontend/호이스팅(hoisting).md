# 호이스팅(hoisting)



## 의미

- 인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것.
  
  - var로 선언한 변수는 호이스팅시 `undefined`로 변수를 초기화
  
  - let, const로 선언한 변수는 호이스팅시 변수를 초기화 하지 않음

### 기술 예제

```js
function catName(){
    console.log("제 고양이의 이름은" + name + "입니다.");
}
catName("호랑이")

/*
결과: "제 고양이의 이름은 호랑이입니다"
*/
```

```js
catName("클로이");

function catName(name) {
  console.log("제 고양이의 이름은 " + name + "입니다");
}

/*
결과: "제 고양이의 이름은 클로이입니다"
*/
```



### 선언만 호이스팅 대상

- JavaScript는 초기화를 제외한 선언만 호이스팅

- 변수를 먼저 사용하고 그 후에 선언 및 초기화가 나타나면, 사용하는 시점의 변수는 기본 초기화 상태(var 선언 시 undefined, 그 외에는 초기화 하지 않음)

```js
console.log(num) // undefined 출력
var num; // 선언
num = 6; // 초기화
```

- 선언 없이 초기화만 존재하면 `ReferenceError`

```js
console.log(num)
num = 6
```

```js
// 예제 1
// y만 호이스팅 대상

x = 1; // x 초기화. x를 선언하지 않은 경우 선언. 그러나 명령문에 var가 없으므로 호이스팅이 발생하지 않음
console.log(x + " " + y); // '1 undefined'
// JavaScript는 선언만 호이스팅하므로, 윗줄의 y는 undefined
var y = 2; // y를 선언하고 초기화

// 예제 2
// 호이스팅은 없지만, 변수 초기화는 (아직 하지 않은 경우) 변수 선언까지 병행하므로 변수를 사용할 수 있음

a = '크랜'; // a 초기화
b = '베리'; // b 초기화

console.log(a + "" + b); // '크랜베리'
```

### let, const

- 호이스팅 대상이지만 `undefined`로 변수를 초기화 하지는 않는다.

- 변수를 초기화 하기 전에 읽는 코드가 먼저 나타나면 예외가 발생



#### 출처

- [호이스팅 - MDN Web Docs 용어 사전: 웹 용어 정의 | MDN](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)


