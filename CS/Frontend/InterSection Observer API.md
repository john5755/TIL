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
