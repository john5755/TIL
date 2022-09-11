## Type

### Array

```typescript
let list: number[] = [1,2,3];
let list: Array<number> = [1,2,3];
```

### Tuple

```typescript
// tuple 타입으로 선언
let x: [string, number];

// 초기화
x = ["Hello", 10] // 성공

// 잘못된 초기화
x = [10, "Hello"] // 오류
x[3] = ''world"; // 오류 property 3이 없음.
```

### Enum

- 데이터가 한정된 값만 갖는 경우
  
  - ex) 요일, 계절

```typescript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;
```

```typescript
enum Color {Red=1, Green, Blue}
let c: Color = Color.Green;
```

```typescript
enum Color {Red=1, Green=2, Blue=4}
let c: Color = Color.Green;
let colorName : string = Color[2] // Green
```

### Any

```typescript
let notSure: any = 4;
notSure = "may be a string instead"
notSure = false // 성공
```

```typescript
// object와 비교
let notSure: any = 4;
notSure.ifItExists() // 성공
notSure.toFixed() // 성공

let prettySure: Object = 4; //할당은 가능
prettySure.toFixed(); // 오류
```

```typescript
// type의 일부만 알 때 유용
let list: any[] = [1, true, "free"]
list[1] = 100;
```

### Void

- any의 반대

- 함수에서 반환값이 없을 때 반환

```typescript
function warnUser(): void{
    console.log("This")
}
```

- 변수 선언에는 유용하지 않음. null과 undefined만 가능.

### Null and Undefined

- 모든 type의 하위 타입, number같은 type에 할당할 수 있다는 것 의미

- `--strictNullChecks` 사용 시 any와 각각 자신에게만 할당 가능
  
  - undefined는 void에도 할당 가능

### Never

- 절대 발생할 수 없는 타입

- 함수에서 항상 오류를 발생시키거나 절대 반환하지 않는 반환 타입으로 쓰임

```typescript
function error(message: string): never{
    throw new Error(message);
}
```

- 모든 type에 할당 가능
  
  - 어떤 type도 never에 할당할 수 있거나 하위 type이 아니다.

### Object

- 원시 타입이 아닌 타입

- object.create같은 API가 더 잘 나타난다.

```typescript
declare function create(o: object | null): void;


create({ prop: 0 }) // 성공
create(null) // 성공
```

### Type Assertion

```typescript
let someValue: any = "This is a String";
let strLength: number = (<string>someValue).length;
let strLength: number = (soeValue as string).length;
```
