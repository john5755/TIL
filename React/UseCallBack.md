# UseCallback

- 함수를 Memoization 하기 위해서 사용되는 hook 함수

- 첫 번째 인자로 넘어온 함수, 두 번째 인자로 넘어온 배열 내의 값이 변경될 때 까지 저장해놓고 재사용

### 자바스크립트 함수 동등성
<<<<<<< HEAD

- 동일한 코드의 js 함수를 `===`으로 검사하면 false가 나옴
  
  - 함수도 객체 취급을 하기 때문에 메모리 주소에 의한 참조 비교가 일어나기 때문
  
  - React Component 함수 내에서 어떤 함수를 다른 함수의 인자로 넘기거나 자식 Component의 prop으로 넘길 때 예상치 못한 성능 문제로 이어질 수 있음

### 의존 배열로 함수를 넘길 때

```js
import React, { useState, useEffect } from "react";

function Profile({ userId }) {
  const [user, setUser] = useState(null);

  const fetchUser = () =>
    fetch(`https://your-api.com/users/${userId}`)
      .then((response) => response.json())
      .then(({ user }) => user);

  useEffect(() => {
    fetchUser().then((user) => setUser(user));
  }, [fetchUser]);

  // ...
}
```

- js의 함수 동등성 판단 방식 때문에 무한 루프 발생

- fetchUser는 함수 이기 때문에 userId 변경 여부 상관 없이 컴포넌트 랜더링 될 때 마다 새로운 참조값으로 변경됨

- useEffect()가 호출되어 user 상태 값이 바뀌고 다시 useEffect()가 호출

- useCallback()이용하면 fetchUser()의 참조값을 동일하게 유지할 수 있음

```js
import React, { useState, useEffect } from "react";

function Profile({ userId }) {
  const [user, setUser] = useState(null);

  const fetchUser = useCallback(
    () =>
      fetch(`https://your-api.com/users/${userId}`)
        .then((response) => response.json())
        .then(({ user }) => user),
    [userId]
  );

  useEffect(() => {
    fetchUser().then((user) => setUser(user));
  }, [fetchUser]);

  // ...
}
```

- useEffect()에 넘어온 함수는 userId값이 변경되지 않는 한 재호출 되지 않음

### React.memo와 함께 사용하기

- useCallback() hook 함수는 자식컴포넌트의 불필요한 랜더링을 줄이기 위해서 React.memo()와 함께 사용 가능

- 방이름(room), 조명 켜짐 여부(on), 조명 제어 함수(toggle)

```js
import React from "react";

function Light({ room, on, toggle }) {
  console.log({ room, on });
  return (
    <button onClick={toggle}>
      {room} {on ? "💡" : "⬛"}
    </button>
  );
}
```

```js
Light = React.memo(Light);
```

```js
import React, { useState, useCallback } from "react";

export default function SmartHome() {
  const [masterOn, setMasterOn] = useState(false);
  const [kitchenOn, setKitchenOn] = useState(false);
  const [bathOn, setBathOn] = useState(false);

  const toggleMaster = useCallback(() => setMasterOn(!masterOn), [masterOn]);
  const toggleKitchen = useCallback(
    () => setKitchenOn(!kitchenOn),
    [kitchenOn]
  );
  const toggleBath = useCallback(() => setBathOn(!bathOn), [bathOn]);

  return (
    <>
      <Light room="침실" on={masterOn} toggle={toggleMaster} />
      <Light room="주방" on={kitchenOn} toggle={toggleKitchen} />
      <Light room="욕실" on={bathOn} toggle={toggleBath} />
    </>
  );
}
```

- React Component 함수를 React.memo()로 감싸면 props가 변경 되지 않는 한 다시 호출 되지 않음

- 조명 제어 함수를 useCallback() hook 함수로 감싸지 않으면 SmartHome 컴포넌트가 랜더링 될 때 마다 모두 바뀜



#### 출처

- daleseo.com/react-hook-use-callback/
=======
>>>>>>> 3c04a99 (usecallback main)
