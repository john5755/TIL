# Decorator

## 소개

- 클래스 선언과 멤버에 어노테이션과 메타-프로그래밍 구문을 추가할 수 있는 방법을 제공

- command line 또는 tsconfing.json에서 --experimentDecorators 컴파일러 옵션 활성화 해야 함.

```ts
tsc --target ES5 --experimentalDecorators
```

```ts
// tsconfig.json

{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}
```

## Decorators

- 클래스 선언, 메서드, 접근자, 프로퍼티 또는 매개변수에 첨부할 수 있는 특수한 종류의 선언

- `@expression`형식을 사용
  
  - expression은 데코레이팅된 선언에 대한 정보와 함께 런타임에 호출된 함수여야

```ts
function sealed(target) {
    // 'target' 변수와 함께 무언가를 수행합니다.
}
```

## 데코레이터 팩토리(Decorator Factories)

- 데코레이터가 선언에 적용되는 방식을 원하는 대로 바꾸고 싶다면 데코레이터 팩토리를 작성할 수 있다.

- 데코레이터가 런타임에 호출할 표현식을 반환하는 함수

```ts
function color(value: string) { // 데코레이터 팩토리
    return function (target) { // 데코레이터
        // 'target'과 'value' 변수를 가지고 무언가를 수행합니다.
  };
}
```

## 데코레이터 합성(Decorator Composition)

- 단일행

```ts
@f @g x
```

- 여러행

```ts
@f
@g
x
```

- 단일 선언에 적용되는 경우는 수학의 합성함수와 유사
  
  - 1. 각 데코레이터의 표현은 위에서 아래로 평가
    
    2. 다음 결과는 아래에서 위로 함수로 호출

- 데코레이터 팩토리 사용 예제

```ts
function f() {
    console.log("f(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("f(): called");
    }
}

function g() {
    console.log("g(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("g(): called");
    }
}

class C {
    @f()
    @g()
    method() {}
}
```

```ts
f(): evaluated
g(): evaluated
g(): called
f(): called
```

## 데코레이터 평가(Decorator Evaluation)

- 클래스에서 다양한 선언에 데코레이터를 적용하는 방법
  
  - 메서드, 접근자 또는 프로퍼티 데코레이터가 다음에 오는 매개변수 데코레이터는 각 인스턴스 멤버에 적용
  
  - 메서드, 접근자 또는 프로퍼티 데코레이터가 다음에 오는 매개변수 데코레이터는 각 정적 멤버에 적용
  
  - 매개변수 데코레이터는 생성자에 적용
  
  - 클래스 데코레이터는 클래스에 적용 

### 클래스 데코레이터

- 클래스 선언 직전에 선언

- 클래스 생성자에 적용되며 클래스 정의를 관찰, 수정 또는 교체하는 데 사용할 수 있다.

- 선언 파일이나 다른 주변 컨텍스트(예: 선언 클래스)에서 사용 할 수 없다.

- 클래스 데코레이터의 표현식은 데코레이팅 클래스의 생성자를 유일한 인수로 런타임 함수로 호출된다.

- 클래스 데코레이터가 값을 반환하면 클래스가 선언을 제공하는 생성자 함수로 바꿉니다.

- 런타임에 데코레이터를 적용하는 로직은 이 기능을 대신해주지 않습니다.

- BugReport Class에 적용된 클래스 데코레이터

```ts
@sealed
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}
```

- 다음 함수 선언을 사용하여 `@sealed` 데코레이터를 정의 할 수 있다.

```ts
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}
```

- `@sealed`가 실행되면 생성자와 프로토타입을 모두 감싼다.

- 생성자 재정의 예제

```ts
@ClassDecorator
class A {
  b: string = "Hello"

  get c(): string {
    return `${this.b} World!`
  }

  d(e: string): void {
    console.log(e)
  }
}

function ClassDecorator(constructor: typeof A) {
  console.log(constructor)
  console.log(constructor.prototype)
}
```

```ts
function ClassDecorator(constructor: typeof A) {
  const method = constructor.prototype.d // 기존의 method
  // 기존의 method를 재정의 한다.
  constructor.prototype.d = function (e: string) {
    method(e) // 기존의 method를 호출하고, 아래를 추가한다.
    console.log("d()를 호출하면 이것도 호출된다!")
  }
}
```

```ts
new A().d("안녕!")
```

```console
안녕!
d()를 호출하면 이것도 호출된다!
```

```ts
function reportableClassDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    reportingURL = "http://www...";
  };
}

@reportableClassDecorator
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}

const bug = new BugReport("Needs dark mode");
console.log(bug.title); // Prints "Needs dark mode"
console.log(bug.type); // Prints "report"

// Note that the decorator _does not_ change the TypeScript type
// and so the new property `reportingURL` is not known
// to the type system:
bug.reportingURL;
Property 'reportingURL' does not exist on type 'BugReport'.
```

### 메서드 데코레이터(Method Decorators)

- 메서드 선언 직전에 선언

- 메서드의 프로퍼티 설명자에 적용

- 메서드 정의를 관찰, 수정 또는 대체하는데 사용

- 선언 파일, 오버로드 또는 기타 주변 컨텍스트에 사용할 수 없다.

- 런타임에 다음 세 개의 인수와 함께 함수로 호출된다.
  
  - 정적 멤버에 대한 클래스의 생성자 함수 또는인스턴스 멤버에 대한 클래스의 프로토타입
  
  - 멤버의 이름
  
  - 멤버의 프로퍼티 설명자
    
    - 스크립트 대상이 'ES5'보다 낮은 경우 프로퍼티 설명자는 'undefined' (접근자도)

- Greeter 클래스 메서드에 적용된 메서드 데코레이터의 예

```ts
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }

  @enumerable(false)
  greet() {
    return "Hello, " + this.greeting;
  }
}
```

- 다음 함수 선언을 사용하여 `@enumerable` 데코레이터를 정의할 수 있다.

```ts
function enumerable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.enumerable = value;
  };
}
```

- `@enumerable(false)`데코레이터는 데코레이터 팩토리

- 데코레이터가 호출되면 프로퍼티 설명자의 enumberable 프로퍼티를 수정

### 접근자 데코레이터(Accessor Decorators)

- 접근자 선언 바로 전에 선언

- 접근자의 프로퍼티 설명자에 적용되며 접근자의 정의를 관찰, 수정, 교체하는데 사용

- 선언 파일이나 다른 주변 컨텍스트에서 사용할 수 없다.
  
  - ts는 단일 멤버에 대해 get 및 set 접근자를 데코레이팅 할 수 없다.

- 멤버의 모든 데코레이터를 문서 순서대로 지정된 첫번째 접근자에 적용해야 한다.
  
  - 데코레이터는 각각의 선언이 아닌 get과 set 접근자를 결합한 프로퍼티 설명자에 적용되기 때문

- 다음 세 가지 인수와 함께 함수로 호출된다.
  
  - 정적 멤버에 대한 클래스의 생성자 함수 또는 인스턴스멤버에 대한 클래스의 프로토타입
  
  - 멤버의 이름
  
  - 멤버의 프로퍼티 설명자

- 접근자 데코레이터가 값을 반환하면 멤버의 프로퍼티 설명자로 사용된다.
  
  - 스크립트 대상이 ES5보다 낮은경우 반환값은 무시 된다.

- Point 클래스의 멤버에 적용되는 접근자 데코레이터의 예

```ts
class Point {
  private _x: number;
  private _y: number;
  constructor(x: number, y: number) {
    this._x = x;
    this._y = y;
  }

  @configurable(false)
  get x() {
    return this._x;
  }

  @configurable(false)
  get y() {
    return this._y;
  }
}
```

```ts
function configurable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.configurable = value;
  };
}
```

### 프로퍼티 데코레이터(Property Decorators)

- 프로퍼티 선언 바로 전에 선언

- 선언 파일이나 다른 주변 컨텍스트에서 사용할 수 없다.

- 런타임에 다음 두 개의 인수와 함께 함수로 호출된다.
  
  - 정적 멤버에 대한 클래스의 생성자 함수 또는 인스턴스 멤버에 대한 클래스의 프로토타입
  
  - 멤버의 이름

```ts
class Greeter {
  @format("Hello, %s")
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }
  greet() {
    let formatString = getFormat(this, "greeting");
    return formatString.replace("%s", this.greeting);
  }
}
```

- 다음 함수 선언을 사용하여 `@format` 데코레이터와 `getFormat`함수를 정의할 수 있습니다.

```ts
import "reflect-metadata";
const formatMetadataKey = Symbol("format");
function format(formatString: string) {
  return Reflect.metadata(formatMetadataKey, formatString);
}
function getFormat(target: any, propertyKey: string) {
  return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}
```
