# UseMemo

### Memorization

- 기존에 수행한 연산의 결과값을 어딘가에 저장해두고 동일한 입력이 들어오면 재활용하는 프로그래밍 기법

### Rendering마다 호출되는 컴포넌트 함수

- React의 함수형 Component는 React 앱에서 rendering이 일어날 때 마다 호출 됨

- rendering은 수시로 계속 일어날 수 있음

### 함수형 Component에 memorization 적용

- 2개의 인자
  
  - 결과 값을 생성해주는 팩토리 함수
  
  - 기존 결과값 재활용 여부의 기준이 되는 입력값 배열

```js
import React, { useMemo } from "react";

function SortedWords({ words }) {
  const sortWords = () => {
    console.log("sortWords");
    delay(500);
    return words.sort();
  };

  const sortedWords = useMemo(sortWords, [words]); // FAST

  return; /* 생략 */
}
```

#### 출처

- daleseo.com/react-hooks-use-memo/
