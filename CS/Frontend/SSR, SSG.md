# SSR, SSG



## Next Js

- SPA -> 기본적으로 CSR 방식을 사용하여 동작
  
  - CSR이 가진 단점 극복하기 위해 SSR 부분 적용하기 위해 개발된 프레임워크
  
  - React -> Next, Vue -> Nuxt



## Next Js 에서의 SSR 및 SSG

- Next는 브라우저에 렌더링할 때 기본적으로 pre-rendering을 함
  
  - 각 페이지를 미리 HTML 문서로 생성하여 가지고 있는 것.
  
  - CSR -> 번들링된 js가 클라이언트 단에서 추가 렌더링을 담당
  
  - Next -> 페이지 별 HTML 문서 미리 생성 후 요청 시 반환

- 이 기능이 사실 SSG에 속함 (Static-Site-Generation)
  
  - HTML을 빌드 타임에 각 페이지별로 생성하고 해당 페이지로 요청이 올 경우 이미 생성된 HTML 문서를 반환 -> Next의 Default
  
  - SSR: 요청이 올 때 마다 HTML 문서를 그 때 생성하여 반환

- Next에서 SSG의 사용을 권고하는 경우
  
  - 퍼포먼스에 집중(CDN을 통해 더 빠른 응답 가능)
  
  - 정적 생성하여 각 요청에 동일한 문서 반환할 수 있는 경우 (제품 목록 등)

- SSR 사용을 권고하는 경우
  
  - 항상 최신 상태를 유지해야 하는 경우 (요청에 따라 응답할 내용이 시시각각 변함)
  
  - 제품 상세 페이지 / 분석 차트 등 요청 마다 다른 내용 또는 형식의 HTML 문서가 반환되는 경우

- CSR을 사용하는 경우
  
  - 데이터의 변동이 빈번하게 일어나는 경우 data-fetching을 통해 CSR할 것을 권고
  
  - 유저 대시보드
    
    - 개인적인 영역, 특정 유저에 귀속, 외부노출이 필요하거나 SEO 적용이 필요 없음
    
    - 유저의 수정을 통해 즉각적으로 화면이 변동



## SSG 및 SSR 적용 방안

- getStaticProps
  
  - 문서에 출력되는 리스트가 항상 고정된 형식/내용인 경우
  
  - 첫 요청에 하나의 HTML 문서를 생성 후 그 이후 요청엔 동일한 문서를 반환
  
  - 데이터를 서버에서 처리하므로 SEO 적용이 용이
  
  - 주의: 개발 모드에서는 SSG로 작성하더라도 요청마다 페이지 재생성
  
  ```javascript
  const About = ({list}) => {
      return
  }
  
  export default About;
  
  export const getStaticProps = aysnc () => {
      const res = await axios.get();
      const data = res.data;
      
      return {
      props : {
      list: data,
  }
  }
  }
  ```

- getServerSideProps
  
  - 링크 이동 후 보여지는 페이지가 매 요청 시 마다 다른 내용 렌더링하는 경우
    
    - SSR로 매 요청시 각기 다른 HTML 생성하여 반환하는 방식이 적절
  
  - dynamic routing 방식
    
    - page에 detail이라는 directory 생성 후 [id].js에 해당 내용 기입
  
  ```js
  const Detail = ({item}) =>{
      return()
  }
  
  export default Detail;
  
  export default getServerSideProps = async (ctx) =>{
      const id = ctx.params.id;
      const res = await axios.get(`url/{id}`)
      const data = res.data
  
      return {
      props:{
      item: data
  }
  }
  }
  ```

- getStaticPaths
  
  - dynamic routing에서 ssg를 적용하고 싶은 경우
  
  - fallback false인 경우 1,2,3이 아닌 경우 404에러
  
  ```js
  export const getStaticPaths = aysnc () => {
      return {
      paths:[
      {params: {id: 1}},
      {params: {id: 2}},
      {params: {id: 3}},
      ],
      fallback: true,
      };
  };
  
  export const getStaticProps = async (ctx) =>{
      const id = ctx.parmas.id;
      const res = await axios.get(`url/${id}`);
      const data = res.data;
  
      return {
      props: {
      item: data,
  }
  }
  }
  ```



### 출처

- https://velog.io/@longroadhome/FE-SSRServer-Side-Rendering-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSGStatic-Site-Generation-feat.-NEXT%EB%A5%BC-%EC%A4%91%EC%8B%AC%EC%9C%BC%EB%A1%9C


