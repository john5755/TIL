## Class

- 간단한 예

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

- Property, 생성자, greet 메서드

- Class 안에서 Class의 멤버 참조 할 때 this를 앞에 붙임

- new를 사용해서 Greeter Class의 Instance를 생성

### 상속(Inheritance)

```ts
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}

class Dog extends Animal {
    bark() {
        console.log('Woof! Woof!');
    }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

- Dogs는 `extends`사용하여 Animal로부터 파생된 파생 Class

```ts
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino"); // Override

sam.move();
tom.move(34);
```

- 파생된 Class의 생성자 함수는 기초 Class의 생성자를 실행할 `super()`를 호출해야 한다.
  
  - `super()`는 부모 객체의 함수를 호출하는 데 사용

- 생성자 내에서 this에 있는 Property에 접근하기 전에 `super()`를 먼저 호출해야 한다.

### Public, Private, Protected modifiers(지정자)

##### 기본적으로 공개(Public by default)

- 기본적으로 공개지만 명시적으로 표시할 수 있음

```ts
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

##### ECMAScript Private Fields

```ts
class Animal {
    #name: string;
    constructor(theName: string) { this.#name = theName; }
}

new Animal("Cat").#name
```

- Property '#name'은 비공개 식별자이기 때문에 'Animal' 클래스 외부에선 접근할 수 없습니다.

##### TypeScript's `Private`

- Class 외부에서 멤버에 접근하지 못하도록 멤버를 `private`으로 표시할 수 있음

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // 오류: 'name'은 비공개로 선언되어 있습니다;
```

- 호환된다고 판단되는 두 개의 타입 중 한 쪽이 `private` 나 `protected` 멤버 가지고 있다면, 다른 한 쪽도 동일한 선언에 `private` 멤버 가지고 있어야 한다.

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 오류: 'Animal'과 'Employee'은 호환될 수 없음.
```

- Animal과 Rhino는 `private name : string`이라는 동일한 선언으로 부터 private 부분을 공유하기 때문에 호환이 가능

- Employee를 Animal에 할당할 때, 타입이 호환되지 않는 오류가 발생
  
  - Employee의 name은 Animal에서 선언한 것이 아니기 때문

##### TypeScript's `Protected`

- `protected`로 선언된 멤버를 파생된 class 내에서 접근할 수 있다는 점 제외하면 매우 유사하게 동작

```ts
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // 오류
```

- Person 외부에서는 name을 사용할 수 없지만 Employee는 Person에서 파생
  
  - Employee의 Instance 메서드 내에서 여전히 사용할 수 있음

```ts
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}

// Employee는 Person을 확장할 수 있습니다.
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // 오류: 'Person'의 생성자는 protected 입니다
```

- 생성자 또한 `protected`로 표시될 수 있음



#### 읽기 전용 지정자(Readonly modifier)

```ts
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 오류! name은 읽기전용 입니다.
```



#### 매개변수 프로퍼티 (Parameter properties)

- 매개변수 프로퍼티 사용하면 한 곳에서 멤버를 만들고 초기화 할 수 있다.

```ts
class Octopus {
    readonly numberOfLegs: number = 8;
    constructor(readonly name: string) {
    }
}
```

- 매개변수 프로퍼티는 접근 지정자나 `readonly` 또는 둘 모두를 생성자 매개변수에 접두어로 붙여서 선언한다.
  
  - private 사용 시 비공개 멤버를 선언하고 초기화



#### 접근자 (Accessors)

- 객체의 멤버에 대한 접근을 가로채는 방식으로 getters/setters 지원

- 예제

```ts
class Employee {
    fullName: string;
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    console.log(employee.fullName);
}
```

- setter -> newName의 길이 확인

- getter -> 기존 기능 유지 위함

```ts
const fullNameMaxLength = 10;

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (newName && newName.length > fullNameMaxLength) {
            throw new Error("fullName has a max length of " + fullNameMaxLength);
        }

        this._fullName = newName;
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    console.log(employee.fullName);
```

- get, set이 없는 접근자는 자동으로 readonly로 유추
  
  - property 내의 사용자들이 변경할 수 없음



#### 전역 프로퍼티(Static Properties)

- Instance가 아닌 Class 자체에서 보이는 전역 멤버를 생성할 수 있다.

- 전역 접근 앞에 클래스 이름(여기선 Grid.)을 붙인다.

```ts
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

#### 추상 클래스(Abstract Classes)

- 다른 클래스들이 파생될 수 있는 기초 클래스

- 직접 instance화 할 수 없다.

- Interface와 달리 멤버에 대한 구현 세부 정보를 포함할 수 있다.

- `abstract`키워드는 추상 클래스 뿐만 아니라 추상 메서드를 정의하는 데 사용된다.

```ts
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log("roaming the earth...");
    }
}
```

- 반드시 파생된 클래스에서 구현되어야 한다.

```ts
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log("Department name: " + this.name);
    }

    abstract printMeeting(): void; // 반드시 파생된 클래스에서 구현되어야 합니다.
}

class AccountingDepartment extends Department {

    constructor() {
        super("Accounting and Auditing"); // 파생된 클래스의 생성자는 반드시 super()를 호출해야 합니다.
    }

    printMeeting(): void {
        console.log("The Accounting Department meets each Monday at 10am.");
    }

    generateReports(): void {
        console.log("Generating accounting reports...");
    }
}

let department: Department; // 추상 타입의 레퍼런스를 생성합니다
department = new Department(); // 오류: 추상 클래스는 인스턴스화 할 수 없습니다
department = new AccountingDepartment(); // 추상이 아닌 하위 클래스를 생성하고 할당합니다
department.printName();
department.printMeeting();
department.generateReports(); // 오류: 선언된 추상 타입에 메서드가 존재하지 않습니다
```



### 고급 기법(Advanced Techniques)

#### 생성자 함수(Constructor functions)

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world""
```

-  `let greeter : Gretter;` Gretter class의 인스턴스 타입으로 Gretter 사용

- `new` 사용 할 때 생성자 함수가 호출 됨

#### 인터페이스로써 클래스 사용하기

```ts
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```

- class 선언은 class의 인스턴스를 나타내는 타입과 생성자 함수 두 가지를 생성
  
  - 타입을 생성할 수 있기 때문에 인터페이스를 사용할 수 있는 동일한 위치에서 사용 가능
