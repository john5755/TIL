## Generics

#### 제네릭의 Hello World

- identity 함수: 인수 그대로 반환하는 함수 (`echo`)

- 제네릭 없다면 identity함수에 특정 타입을 주어야 한다. (또는 any)

```ts
function identity(arg: number): number {
    return arg;
}
```

- any
  
  - arg가 어떤 타입이어도 받을 수 있다는 점에서 제네릭
  
  - 실제로 반환될 때 어떤 타입인지에 대한 정보를 잃게 됨
    
    - 무엇이 반환되는지 표시하기 위해 인수의 타입을 캡쳐할 방법이 필요
    
    - 값이 아닌 타입에 적용되는 타입 변수를 사용

```ts
function identity<T>(arg: T): T {
    return arg;
}
```

- T -> 타입변수
  
  - 유저가 준 인수의 타입을 캡쳐하고 나중에 사용할 수 있게 함
    
    - 여기서는 반환타입으로 다시 사용
  
  - any와 달리 어떤 정보도 잃지 않는다.

- identity 함수의 호출
  
  - 함수에 타입 인수를 포함한 모든 인수를 전달하는 방법
  
  ```ts
  let output = identity<string>("myString"); // 출력 타입은 'string'입니다.
  ```
  
  - 타입 인수 추론(컴파일러가 T의 값을 자동으로 정함)
  
  ```ts
  let output = identity("myString"); //출력 타입은 'string'입니다.
  ```
  
  - 컴파일러가 "myString"을 보고 T를 정함
  
  - 컴파일러가 유추할 수 없는 경우 명시적인 타입 인수 전달이 필요

##### 제네릭 타입 변수 작업(Working with Generic Type Variables)

- 제네릭을 사용하기 시작하면 컴파일러가 함수 본문에 제네릭 타입화된 매개변수를 쓰도록 강요

- identity 함수 호출 시 마다 arg길이를 log에 찍으려면...?

```ts
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length); // 오류: T에는 .length 가 없습니다.
  return arg;
```

- .length 없는 type이 사용될 수도 있음 (number)

- T가 아닌 T의 배열에서 동작하도록 의도했다고 가정

```ts
function loggingIdentity<T>(arg: T[]): T[] {
  console.log(arg.length); // 배열은 .length를 가지고 있습니다. 따라서 오류는 없습니다.
  return arg;
}
```

```ts
function loggingIdentity<T>(arg: Array<T>): Array<T> {
  console.log(arg.length); // 배열은 .length를 가지고 있습니다. 따라서 오류는 없습니다.
  return arg;
}
```

##### Generic Types

- 제네릭 함수의 타입
  
  - 함수 선언과 유사하게 타입 매개변수가 먼저 나열되는, 비-제네릭 함수의 타입과 비슷

```ts
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

-     타입 변수의 수와 타입 변수가 사용되는 방식에 따라 타입의 제네릭 타입의 매개 변수에 다른 이름을 사용할 수도 있음 

```ts
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
```

- 제네릭 타입을 객체 리터럴 타입의 함수 호출 시그니처로 작성할 수도 있음

```ts
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: { <T>(arg: T): T } = identity;
```

- 제네릭 인터페이스 작성할 수 있음

```ts
interface GenericIdentityFn {
  <T>(arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

- 제네릭 매개변수를 전체 인터페이스의 매개변수로 옮길 수 있음
  
  - 제네릭 타입을 확인할 수 있음
  
  - 인터페이스의 다른 모든 멤버가 타입 매개변수를 볼 수 있음

```ts
interface GenericIdentityFn<T> {
  (arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

- 제네릭 함수 작성 대신 비-제네릭 함수 시그니처

- 시그니처가 사용할 것을 효과적으로 제한할 특정한 타입 인수 필요 (number)

##### Generic Classes

- 인터페이스와 형태 비슷 `<>` 안쪽에 제네릭 타입 매개변수 목록을 가짐

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

- GenericNumber 클래스의 문자 그대로 사용하지만 number 타입만 쓰도록 제한 하는 것은 없음

- string이나 훨씬 복잡한 객체를 사용할 수 있음

```ts
let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

- 클래스는 정적 측면이 아닌 인스턴스 측면에서만 제네릭
  
  - 정적 멤버는 클래스의 타입 매개변수를 쓸 수 없다.

##### 제네릭 제약조건

- 특정 타입들로만 동작하는 제네릭 함수 만들기

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // 오류: T에는 .length가 없습니다.
    return arg;
}
```

- 모든 타입에서 .length를 가지는 것이 아니라서 error

- 제약 조건이 명시하는 인터페이스를 만들고, extends 키워드로 표현한 인터페이스를 이용해서 명시

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // 이제 .length 프로퍼티가 있는 것을 알기 때문에 더 이상 오류가 발생하지 않습니다.
    return arg;
}
```

- 함수는 모든 타입에 대해서 동작하지는 않으며, 필요한 프로퍼티들이 있는 타입의 값을 전달해야 함.

```ts
loggingIdentity(3);  // 오류, number는 .length 프로퍼티가 없습니다
loggingIdentity({length: 10, value: 3});
```

##### 제네릭 제약조건에서 타입 매개변수 사용하기

- 다른 타입 매개변수로 제한된 타입 매개변수를 선언 할 수 있음

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // 성공
getProperty(x, "m"); // 오류: 인수의 타입 'm' 은 'a' | 'b' | 'c' | 'd'에 해당되지 않음.
```

- 실수로 obj에 존재하지 않는 프로퍼티 가져오지 않도록 하기 위해 두 가지 타입에 제약조건

##### 제네릭에서 클래스 타입 사용 (Using Class Types in Generics)

- 제네릭을 사용하는 TS에서 팩토리를 생성할 때 생성자 함수로 클래스 타입을 참조해야 한다.

```ts
function create<T>(c: {new(): T; }): T {
    return new c();
}
```

- 고급 예제에서는 prototype 프로퍼티를 사용하여 생성자 함수와 클래스 타입의 인스턴스 사이의 관계를 유추하고 제한한다.

```ts

```
