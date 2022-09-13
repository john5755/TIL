## 열거형 (Enums)

##### 숫자 열거형(Numeric)

- enum 키워드 통해서 정의

```ts
enum Direction {
    Up = 1,
    Down,
    Left,
    Right,
}
```

- Up이 1로 초기화

- 그 지점부터 뒤따르는 멤버들은 자동으로 증가된 값을 가짐

- 초기화 하지 않으면 Up=0

```ts
enum Response {
    No = 0,
    Yes = 1,
}

function respond(recipient: string, message: Response): void {
    // ...
}

respond("Princess Caroline", Response.Yes)
```

- 열거형 자체에서 Property로 모든 멤버에 접근

- 열거형의 이름을 사용해 타입을 선언

```ts
enum E {
    A = getSomeValue(),
    B, // 오류! 앞에 나온 A가 계산된 멤버이므로 초기화가 필요합니다.
}
```

- 계산된 멤버와 상수 멤버를 섞어서 사용할 수 있음

- 초기화 되지 않은 열거형이 먼저 나오거나
  
  - 숫자 상수 혹은 다른 상수 열거형 멤버와 함께 초기화된 숫자 열거형 이후에 와야 함



##### 문자열 열거형(String enums)

- 각 멤버들은 문자열 리터럴 또는 다른 문자열 열거형의 멤버로 상수 초기화 되어야 함.

- 유의미하고 읽기 좋은 값을 이용하여 실행 가능 (직렬화)

```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

##### 이종 열거형

- 사용하지 않는  것 권장

##### 계산된 멤버와 상수 멤버(computed and constant)

- 상수로 간주되는 경우
  
  - 열거형의 첫 번째 데이터이며 초기화 값이 없는 경우 (0으로 할당)
  
  - 초기화 값이 없으며 숫자 상수로 초기화된 열거형 멤버뒤에 따라 나오는 경우
  
  ```ts
  // 'E1' 과 'E2' 의 모든 열거형 멤버는 상수입니다.
  
  enum E1 { X, Y, Z }
  
  enum E2 {
      A = 1, B, C
  }
  ```
  
  - 열거형 멤버는 상수 열거형 표현식으로 초기화 됨.
    
    - 리터럴 열거형 표현식
    
    - 이전에 정의된 다른 상수 열거형 멤버에 대한 참조
    
    - 괄호로 묶인 상수 열거형 표현식
    
    - 상수 열거형 표현식에 단항 연산자 (`+`, `-`, `~`) 사용한 경우
    
    - 상수 열거형 표현식에 이중 연산자 (`+`, `-`, `*`, `/`, `%`, `<<`,`>>`, `>>>`, `&`, `|`, `^`)의 피연산자로 사용할 경우
      
      - NaN이거나 Infinity이면 Error
  
  - 이 외의 경우 계산된 것으로 간주
  
  ```ts
  enum FileAccess {
      // 상수 멤버
      None,
      Read    = 1 << 1,
      Write   = 1 << 2,
      ReadWrite  = Read | Write,
      // 계산된 멤버
      G = "123".length
  }
  ```

##### 유니언 열거형과 열거형 멤버 타입

- 계산되지 않는 상수 열거 멤버의 특수한 부분집합인 리터럴 열거형 멤버
  
  - 초기화 값이  존재하지 않거나 아래의 값들로 초기화 되는 멤버
    
    - 문자 리터럴
    
    - 숫자 리터럴
    
    - 숫자 리터럴에 단항 연산자 `-` (-100)

- 열거형의 모든 멤버가 리터럴 열거형 값을 가지면
  
  - 열거형 멤버를 타입 처럼 사용할 수 있다. (특정 멤버는 오직 열거형 멤버의 값만 가지게 할 수 있음)
  
  ```ts
  enum ShapeKind {
      Circle,
      Square,
  }
  
  interface Circle {
      kind: ShapeKind.Circle;
      radius: number;
  }
  
  interface Square {
      kind: ShapeKind.Square;
      sideLength: number;
  }
  
  let c: Circle = {
      kind: ShapeKind.Square, // 오류! 'ShapeKind.Circle' 타입에 'ShapeKind.Square' 타입을 할당할 수 없습니다.
      radius: 100,
  }
  ```
  
  - 열거형 타입 자체가 효율적으로 각각의 열거형 멤버의 유니언이 된다.
    
    - 타입 시스템이 열거형 자체에 존재하는 정확한 값의 집합을 알고 있다는 점을 활용할 수 있다.
  
  ```ts
  enum E {
      Foo,
      Bar,
  }
  
  function f(x: E) {
      if (x !== E.Foo || x !== E.Bar) {
          //             ~~~~~~~~~~~
          // 에러! E 타입은 Foo, Bar 둘 중 하나이기 때문에 이 조건은 항상 true를 반환합니다.
      }
  }
  ```
  
  -  x !== E.foo -> true 이므로 통과
  
  - 조건에 통과하지 않는다면 x == E.foo, 뒤에 조건은 적절하지 않음



##### 런타임에서 열거형 (Enums at runtime)

- 열거형은 runtime에 존재하는 실제 객체

```ts
enum E {
    X, Y, Z
}
```

```ts
function f(obj: { X: number }) {
    return obj.X;
}

// E가 X라는 숫자 프로퍼티를 가지고 있기 때문에 동작하는 코드입니다.
f(E);
```

##### 컴파일 시점에서 열거형

- 열거형이 런타임에 실제 존재하는 객체라고 하더라도 `keyof`키워드는 일반적인 객체와 다르게 동작

- `keyof` `typeof`를 사용하면 모든 열거형의 키를 문자열로 나타내는 타입을 가져옴

```ts
enum LogLevel {
    ERROR, WARN, INFO, DEBUG
}

/**
 * 이것은 아래와 동일합니다. :
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;

function printImportant(key: LogLevelStrings, message: string) {
    const num = LogLevel[key];
    if (num <= LogLevel.WARN) {
       console.log('Log level key is: ', key);
       console.log('Log level value is: ', num);
       console.log('Log level message is: ', message);
    }
}
printImportant('ERROR', 'This is a message');
```



##### 역 매핑

```ts
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[a]; // "A"
```

```ts
var Enum;
(function (Enum) {
    Enum[Enum["A"] = 0] = "A";
})(Enum || (Enum = {}));
var a = Enum.A;
var nameOfA = Enum[a]; // "A"
```

- 열거형은 정방향(name -> value) 매핑과 역방향(value -> name)매핑 두 정보를 모두 저장하는 객체로 컴파일 된다.

- 문자 열거형은 역매핑 생성하지 않는다.



##### Const 열거형

- 추가로 생성된 코드 및 추가적인 간접 참조에 대한 비용을 피하기 위해 사용

```ts
const enum Enum {
    A = 1,
    B = A * 2
}
```

- 상수 열거형 표현식만 사용

- 컴파일 과정에서 완전히 제거됨

- 사용하는 과정에 인라인됨

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
```

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```



##### Ambient enums

- 이미 존재하는 열거형 타입의 모습을 묘사하기 위해 사용

```ts
declare enum Enum{
    A = 1,
    B,
    C = 2
}
```

- 일반적 열거형과 달리 초기화 되지 않은 멤버는 항상 계산된 멤버로 간주함.
