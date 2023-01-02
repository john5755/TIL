# InterSection Observer API

- 타겟 요소와 상위 요소 또는 최상위 document의 viewport 사이의 intersection 내의 변화를 비동기적으로 관찰하는 방법

- 필요성
  
  - 페이지가 스크롤 되는 도중에 발생하는 이미지나 다른 컨텐츠의 지연 로딩
  
  - `infinite-scroll`구현
  
  - 광고 수익을 계산하기 위한 광고의 가시성 보도
  
  - 사용자에게 결과가 표시되는 여부에 따라 작업이나 애니메이션을 수행할 지 여부를 결정

### Intersection Observer의 컨셉과 사용

- 다음 상황에서 호출되는 콜백을 생성하는 기능을 제공
  
  - 대상(target)으로 칭하는 요소가 기기 Viewport나 특정 요소(root)와 교차
  
  - Observer가 최초로 Target을 관측하도록 요청받을 때

- document의 루트 요소를 기준으로 해당 요소를 관측하기 위해서는 null을 설정

- root와 대상 요소가 교차하는 정도를 `intersection ratio`라고 함
  
  - 대상 요소의 가시성 퍼센티지를 0.0~1.0의 숫자로 표현

### InterSection Observer 생성하기

- 생성자 호출 시 콜백 함수를 제공해야 한다.

- 콜백함수는 threshold가 한 방향 혹은 다른 방향으로 교차할 때 실행된다.

```js
let options = {
    root: document.querySelector('#scrollArea'),
    rootMargin: '0px',
    threshold: 1.0,
}

let observer = new IntersectionObserver(callback, options)
```

- threshold: 1.0은 대상 요소가 root 내 지정 요소 내에서 100% 보여질 때 콜백이 호출 될 것을 의미



### InterSection Observer 설정하기

- options 객체가 observer 콜백이 호출되는 상황을 조작할 수 있다.

- `root`:
  
  - 대상 객체의 가시성을 확인할 때 사용되는 `viewport`요소
  
  - 대상 객체의 조상 요소여야 한다.
  
  - 기본 값은 브라우저 viewport, root 값이 null이거나 지정되지 않을 때 설정

- `rootMargin`:
  
  - root가 가지는 여백. css의 margin 속성과 유사
  
  - root 요소의 각 측면의 bounding box를 수축시키거나 증가시킴.
  
  - 교차성을 계산하기 전에 적용
  
  - 기본 값은 0

- `threshold`:
  
  - observer의 콜백이 실행될 대상 요소의 가시성 퍼센티지를 나타내는 단일 숫자 혹은 숫자 배열
  
  - 50%만큼 요소가 보여졌을 때 탐지하고 싶다면 0.5로 설정
  
  - 25% 단위로 요소의 가시성이 변경될 때마다 콜백이 실행되게 하고 싶다면
    
    - [0, 0.25, 0.5, 0.75, 1]과 같은 배열을 설정
  
  - 기본값은 0
    
    - 요소가 1픽셀이라도 보이자 마자 콜백이 실행
  
  - 1.0은 요소의 모든 픽셀이 화면에 노출되기 전에는 콜백을 실행시키지 않음을 의미
