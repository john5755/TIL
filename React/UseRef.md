# UseRef

## UseRef가 필요한 이유

- React Component는 내부 상태(state)가 변할 때 마다 다시 랜더링
  
  - 함수 내부의 변수들이 모두 초기화되고 함수의 모든 로직이 다시 실행된 다는 것을 의미

- 다시 Rendering되어도 동일한 참조값을 유지하려면
  
  - useEffect 사용 예시
  
  ```js
  import React, { useState, useEffect } from "react";
  
  function ManualCounter() {
    const [count, setCount] = useState(0);
  
    let intervalId;
  
    const startCounter = () => {
      // 💥 매번 새로운 값 할당
      intervalId = setInterval(() => setCount((count) => count + 1), 1000);
    };
    const stopCounter = () => {
      clearInterval(intervalId);
    };
  
    return (
      <>
        <p>자동 카운트: {count}</p>
        <button onClick={startCounter}>시작</button>
        <button onClick={stopCounter}>정지</button>
      </>
    );unt}</p>;
  }
  ```
  
  - intervalId 변수를 startCounter와 stopCounter 함수가 공유해야 함
    
    - intervalId를 두 함수 밖에서 선언 해야 함
    
    - count 상태 값이 바뀔 때마다 intervalId도 새로운 값으로 바뀜
    
    - 브라우저 메모리에 정리되지 못한 intervalId들이 1초에 하나씩 쌓인다

## UseRef 사용방법

- useRef는 `current`속성을 가지고 있는 객체를 반환
  
  - 인자로 넘어온 초기값을 current 속성에 할당
  
  - 값을 변경해도 상태를 변경할 때 처럼 React Component가 다시 랜더링 되지 않음
  
  - 다시 랜더링 될 때도 유실되지 않음

```js
import React, { useState, useRef } from "react";

function ManualCounter() {
  const [count, setCount] = useState(0);
  const intervalId = useRef(null);
  console.log(`랜더링... count: ${count}`);

  const startCounter = () => {
    intervalId.current = setInterval(
      () => setCount((count) => count + 1),
      1000
    );
    console.log(`시작... intervalId: ${intervalId.current}`);
  };

  const stopCounter = () => {
    clearInterval(intervalId.current);
    console.log(`정지... intervalId: ${intervalId.current}`);
  };

  return (
    <>
      <p>자동 카운트: {count}</p>
      <button onClick={startCounter}>시작</button>
      <button onClick={stopCounter}>정지</button>
    </>
  );
}
```

#### 출처

- https://www.daleseo.com/react-hooks-use-ref/
