## React Server Components: 속도, 상호작용성, 그리고 사용자 경험 향상
웹 개발 컨설턴트인 Aurora Scharff는 **React Server Components**가 서버 렌더링, 실시간 스트리밍, 상호작용 최적화를 통해 어떻게 웹 앱 성능을 개선하는지에 대해 보여줍니다.

### 3줄 요약
- React Server Components를 사용하여 웹 성능과 사용자 경험 향상시키기
- 서버 컴포넌트를 사용하여 데이터를 비동기적으로 가져오기 / 서스펜스를 활용하여 로딩 상태 처리하기
- React 19의 새 기능 (캐싱, 낙관적 업데이트)

### Intro
- React 서버 컴포넌트란?: 서버에서만 실행되는 컴포넌트로, 클라이언트에 JavaScript를 전송하지 않아 번들 크기를 줄이고 성능을 향상시킵니다.
- 서버 컴포넌트의 장점: 비동기 데이터 가져오기, 백엔드 리소스 직접 액세스, 성능 향상
- 데모 애플리케이션 소개: '프로젝트 작업 관리' 데모 웹페이지를 통해 서버 컴포넌트의 기능과 이점을 설명합니다.
- 데모 기술스택: Next.js, Prisma ORM, Azure SQL 데이터베이스, Tailwind CSS 사용

<img width="1424" alt="Screenshot 2024-11-27 at 8 45 30 PM" src="https://github.com/user-attachments/assets/65b75967-d80d-4c65-9618-318ae0cd45fc">

- 데모 앱을 Light House 에 돌려보았을 때, 초기로딩은 매우 느리고, First Contentful Paint 3.1s 로 느리지만, JS코드가 이 컴포넌트엔 없기 때문에 Total Blocking Time 은 0이며, 모든 페이지가 한번에 그려지기 때문에 Layout Shift 도 0이다.
- 전반적인 성능 점수는 나쁘지 않지만 UX가 좋지않은 상태이다. 이제 이 데모 웹을 기반으로 개선해보자.

### 1. Suspense 사용하기
- 다른 탭으로 이동할 때, 해당 탭의 서버 컴포넌트가 렌더링 될 때까지 기다려야하므로 대기시간이 꽤 길다.
- 대기 상태를 사용자에게 표시해주기 위해 `<Suspense/>` 로 감싸서 Skeleton 을 제공해준다.
- 기존에는 layout 에서 모든 데이터를 페칭하고 있었는데, 각 컴포넌트에서 필요한 데이터를 페칭하도록 분리해주고, 각각을 Suspense 로 감쌌다.

<img width="472" alt="Screenshot 2024-11-27 at 9 00 46 PM" src="https://github.com/user-attachments/assets/ddab4791-05cf-4f03-8539-6df746f288e6">

### 2. `useTransition()` 사용하기
- useTransition 은 상태변화를 일으키는 함수의 우선순위를 낮추어 실행시키고, 실행될 때까지 isPending 을 통해 boolean 값으로 상태변화 지연 여부를 알려주는 훅이다.
- startTransition 은 상태변화를 일으키는 콜백 함수를 받고, isPending 은 상태변화 지연 여부를 가진다.
- 이 데모에서는 검색어 입력시 url의 search param을 업데이트하여 새로운 데이터를 페칭해오는 구조이므로, search param을 업데이트하는 과정을 `startTransition` 으로 감싸고, `isPending` 값을 사용하여 route 를 업데이트하는동안에 검색어 입력란에 로딩 스핀을 노출해준다.
- [useTransition 한글 문서](https://ko.react.dev/reference/react/useTransition)

### 3. `useOptimistic` 사용하기 
- 비동기 작업이 진행 중일 때 다른 상태를 보여줄 수 있게 해주는 React Hook
- [useOptimistic 한글 문서](https://ko.react.dev/reference/react/useOptimistic#use)
  - 인자로 일부 상태를 받아, 비동기 작업동안 달라질 수 있는 그 상태의 복사본을 반환합니다.
  - 이 상태는 “낙관적” 상태라고 불리는데, 실제로 작업을 완료하는 데 시간이 걸리더라도 사용자에게 즉시 작업의 결과를 표시하기 위해 일반적으로 사용됩니다.
- 이 데모에서는, 유저가 카테고리 리스트에서 새로운 카테고리를 선택 또는 해제할 때, 실제 데이터를 페치해올 때까지 시간이 소요되므로 그 시간동안 낙관적 업데이트를 통해 유저에게 즉각적인 피드백을 주도록 개선했습니다.
<img width="714" alt="Screenshot 2024-12-03 at 9 36 28 PM" src="https://github.com/user-attachments/assets/ce8e7cf3-7fd7-4f6f-8a5e-5550d56ae19c">

### 4. `cache` 사용하기
- [cache 한글 문서](https://ko.react.dev/reference/react/cache)
- 데모 앱에서 카테고리 리스트를 페칭해오는 부분이 2번 중복되어서, 이 함수를 react cache 로 감싸주었다.
- cache 적용 후, 페이지 로드 시간이 2.6s -> 2.1s 로 단축되었다.
<img width="334" alt="스크린샷 2024-12-04 오후 1 40 21" src="https://github.com/user-attachments/assets/8f32b9f2-a753-4274-998f-d27bb82b9e8c">

### 5. `staleTime` 사용하기
- 클라이언트 사이드의 라우터 캐시에서 세그먼트를 캐싱할 수 있게 해주는 기능
- dynamic 속성: `Link`의 prefetch 속성이 지정되지 않았거나, `false` 로 설정된 경우에 사용됨 (기본값: 0)
- static 속성: `Link`의 prefetch 속성이 true 이거나, router.prefetch 를 호출할때 사용됨 (기본값: 5분)
- [next.js staleTime 문서](https://nextjs.org/docs/app/api-reference/next-config-js/staleTimes)
- 이 데모에서는, `dynamic: 30` 설정으로 30초간 캐싱하여, 이미 데이터를 불러왔던 페이지로 이동하는 경우 빠르게 데이터가 보이도록 사용성을 개선했습니다.
```
const nextConfig = {
  experimental: {
    staleTimes: {
      dynamic: 30,
      static: 180,
    },
  },
}
```
