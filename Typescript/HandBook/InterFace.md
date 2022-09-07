## InterFace

### Our First InterFace

```typescript
function printLabel(labelObj: {label: string}){
    console.log(labelObj.label);
}


let myObj = {size:10, label:"size to 10 object"}
printLabel(myObj)
```

```typescript
interface LabeledValue{
    label: string;
}

function printLabel(labeledObJ: labeledValue){
    console.log(labeledObj.label);
}

let myObj = {size:10, label: "size to 10 object"};
printLabel(myObj)
```

- Property가 존재하는 지와 요구하는 type을 가졌는지만 검사한다.

### Optional Properties

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}


function createSquare(config: SquareConfig):{color: string; area:number;}{
    let newSquare = {color: "white", ,area:100};
    if (config.color){
    newSquare.color = config.color
    }
    if (config.width){
    newSquare.area = config.width * config.width;
    }
    return newSquare;
}


let mySquare = createSquare({color:"black"})
```

- interface에 속하지 않는 property의 사용을 방지, 사용가능한 속성을 기술

### Readonly Properties

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}
```

- Array

```typescript
let a: number[] = [1,2,3,4];
let ro: ReadonlyArray<number> = a;

a = ro; // 오류
a = ro as number[]; // type assertion으로 override 가능
```

- 변수는 const 사용 property는 readonly 사용



### Excess Property Checks(초과 프로퍼티 검사)

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 });
```

- 대상 type이 갖고 있지 않은 property를 가지고 있어서 오류 발생

```typescript
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

```typescript
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

- 공통 객체 property 없으면 error 발생

- "피하는" 방법은 가급적 사용하지 않는 것이 바람직.



### Function Types

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}
```

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;

let mySearch: SearchFunc;

// error: Type '(src: string, sub: string) => string' is not assignable to type 'SearchFunc'.
// Type 'string' is not assignable to type 'boolean'.
mySearch = function(src, sub) {
  let result = src.search(sub);
  return "string";
};
```

### Indexable Types

```typescript
interface StringArray {
    [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

- stringArray가 number로 indexed 되면 string을 반환할 것.

- 숫자 인덱서에서 반환된 타입은 반드시 문자열 인덱서에서 반환된 타입의 하위 타입이어야 한다.
  
  - JS가 객체를 indexing하기 전에 string으로 변환하기 때문

- 인덱스의 할당을 막기 위해 인덱스 시그니처를 `읽기 전용`으로 만들 수 있음.

```typescript
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // 오류!
```



### Class

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date): void;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
