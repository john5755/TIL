## 유니언과 교차타입

### 유니언(Union)

```typescript
/**
 * 문자열을 받고 왼쪽에 "padding"을 추가합니다.
 * 만약 'padding'이 문자열이라면, 'padding'은 왼쪽에 더해질 것입니다.
 * 만약 'padding'이 숫자라면, 그 숫자만큼의 공백이 왼쪽에 더해질 것입니다.
 */
function padLeft(value: string, padding: any) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // "Hello world"를 반환합니다.
```

- padding이 'any' type이기 때문에 number나 string으로도 함수 호출 가능

```typescript
declare function padLeft(value: string, padding: any): string;
// ---생략---
let indentedString = padLeft("Hello world", true);
```

- 컴파일은 통과하지만 런타임 에러 발생

```ts
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft("Hello world", true);
```

- 유니언은 여러 타입 중 하나가 될 수 있는 값을 의미



### 공통필드를 갖는 유니언 (Unions with Common Fields)

- 유니언 타입인 값이 있으면, 유니언에 있는 모든 타입에 공통인 멤버들에만 접근가능

```ts
interface Bird {
  fly(): void;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

declare function getSmallPet(): Fish | Bird;

let pet = getSmallPet();
pet.layEggs();

// 두 개의 잠재적인 타입 중 하나에서만 사용할 수 있습니다.
pet.swim();
```

- pet이 Bird인지 Fish인지 모르기 때문에 Error



### 유니언 구별하기 (Discriminating Unions)

- 가능한 타입 추론의 범위를 좁혀나가게 해줄수 있는 리터럴 타입을 갖는 단일 필드를 사용

```ts
type NetworkLoadingState = {
  state: "loading";
};

type NetworkFailedState = {
  state: "failed";
  code: number;
};

type NetworkSuccessState = {
  state: "success";
  response: {
    title: string;
    duration: number;
    summary: string;
  };
};

// 위 타입들 중 단 하나를 대표하는 타입을 만들었지만,
// 그것이 무엇에 해당하는지 아직 확실하지 않습니다.
type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState;
```

- state 필드는 NetworkState안의 모든 type에 공통으로 존재 -> 접근 가능
  
  - state통해서 어느 타입인 줄 알 수 있음

- switch 문 사용

```ts
function networkStatus(state: NetworkState): string {
  // 현재 TypeScript는 셋 중 어떤 것이
  // state가 될 수 있는 잠재적인 타입인지 알 수 없습니다.

  // 모든 타입에 공유되지 않는 프로퍼티에 접근하려는 시도는
  // 오류를 발생시킵니다.
  state.code;

  // state에 swtich문을 사용하여, TypeScript는 코드 흐름을 분석하면서
  // 유니언 타입을 좁혀나갈 수 있습니다.
  switch (state.state) {
    case "loading":
      return "Downloading...";
    case "failed":
      // 여기서 타입은 NetworkFailedState일 것이며,
      // 따라서 `code` 필드에 접근할 수 있습니다.
      return `Error ${state.code} downloading`;
    case "success":
      return `Downloaded ${state.response.title} - ${state.response.summary}`;
  }
}
```



### 교차 타입(Intersection Types)

- 여러 타입을 하나로 결합
  
  - 필요한 기능을 가진 하나의 타입을 얻을 수 있다.

```ts
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

interface ArtistsData {
  artists: { name: string }[];
}

// 이 인터페이스들은
// 하나의 에러 핸들링과 자체 데이터로 구성됩니다.

type ArtworksResponse = ArtworksData & ErrorHandling;
type ArtistsResponse = ArtistsData & ErrorHandling;

const handleArtistsResponse = (response: ArtistsResponse) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.artists);
};
```



### 교차를 통한 믹스인 (Mixins via Intersections)

- 믹스인 패턴을 실행하기 위해 교차가 사용됨.

```ts

```
