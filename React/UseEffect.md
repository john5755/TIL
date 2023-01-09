# UseEffect

- React Component가 랜더링 될 때 마다 특정 작업을 실행할 수 있도록 하는 hook
  
  - component가 mount, unmount, update 됐을 때 특정 작업을 처리

### 사용법

- 기본형태
  
  - useEffect(function, deps)

- 불러오기
  
  ```js
  import React, {useEffect} from 'react';
  ```

- Component가 Mount 됐을 때
  
  ```js
  useEffect(()=>{
      console.log('mount 될 때만 실행')
  },[])
  ```

- Component가 Update 됐을 때(특정 props, state 바뀔 때)
  
  ```js
  useEffect(()=>{
      console.log(name)
  },[name])
  ```
  
  - mount될 때 도 실행됨
  
  ```js
  const mounted = useRef(false);
  
  useEffect(()=>{
      if(!mounted.current){
      mounted.current = true
  } else {
      // 원하는 코드
  }
  },[name])
  ```

- Component가 unmount(사라질 때) & update되기 직전에
  
  ```js
  useEffect(()=>{
      console.log('effect')
      console.log(name)
      return () => {
      console.log('cleanup');
      console.log(name)
  }
  },[])
  ```
  
  - cleanup 함수
    
    - return 뒤에 나오는 함수
    
    - unmount시 -> 빈 배열
    
    - update 직전 -> deps값 작성



#### 출처

- [React Hooks : useEffect() 함수 :: 자라는 것을 잘하는 개발자](https://xiubindev.tistory.com/100)
