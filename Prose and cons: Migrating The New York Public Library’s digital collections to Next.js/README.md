## 뉴욕 공립 도서관 디지털 컬렉션을 Next.js로 마이그레이션하기

뉴욕 공립 도서관에서 일하는 Emma Mansell이 접근성과 성능, 그리고 향후 피쳐 통합을 고려하여 디지털 컬렉션 웹사이트를 Ruby on Rails에서 Next.js로 마이그레이션한 과정을 설명합니다.

### 1. 뉴욕 공립 도서관과 디지털 컬렉션 소개 [(0:00 - 6:17)](https://youtu.be/esOYhqevuqw?si=u_H1R5Z1pXMMxCAr)
- 뉴욕 공립 도서관 디지털 컬렉션은 100만개가 넘는 모든 디지털 자료 (인쇄물, 사진, 비디오, 오디오 등) 를 검색하고 열람하는 웹 사이트이다.
- Next.js 를 사용하며 가장 크게 고려한 점: 접근성, SEO
- 대규모 조직이 아닌 저희처럼 소규모 팀, 제한된 리소스, 그리고 접근성을 가장 우선시하는 경우에 Next.js가 어떻게 작동하는지를 보여주려 합니다.

### 2. 기존 Ruby on Rails 웹사이트의 한계 [(6:17 - 7:08)](https://youtu.be/esOYhqevuqw?si=24DFxCHYb-Q7jurn&t=377)
- 성능은 좋지만 2013년에 구축된 옛날 코드이다.
- 접근성 표준을 충족하지 못했다.
- 글꼴 크기, 색상 대비 등을 더 현대적이고 접근성이 뛰어나도록 개선이 필요했다.

### 3. Next.js 를 선택한 이유 [(7:08 - 8:52)](https://youtu.be/esOYhqevuqw?si=Izb_yJkYDxyoVKdY&t=428)
- 가볍고, 쉽게 바로 사용 가능
- 하이브리드 렌더링 지원 (Static Generation + SSR)
- Next.js 9~11 버전 개발 경험이 있는 개발자가 있었다.

### 4. Next.js 마이그레이션 단계 [(8:52 - 10:00)](https://youtu.be/esOYhqevuqw?si=08e23K7dVP2rT1y2&t=532)
- URL 기반으로 마이그레이션을 단계적으로 진행하는 전략을 사용했다.
- 초기에는 Ruby 의 Controller 함수를 1:1로 Next.js API 경로와 매핑하려 했으나 이 방법대신, 파일 기반 라우팅을 사용하는 방법으로 변경했다.
  > Ruby on Rails 에서 라우팅은 일반적으로 Controller 와 Action 을 기반으로 설정한다.
  > 즉, `routes.rb` 파일에 특정 URL 경로와 그에 대응하는 Controller 함수를 매핑하는 식이다.
  > 이때, Controller 파일 안에는 여러 Action 들이 있고, 각 함수가 특정한 요청 (URL)과 연결되는 구조이다.
  > 
  > 한편, Next.js 에서는 **파일 기반 라우팅** 방식을 사용하므로, pages 폴더 내부에 파일을 생성하면, 파일명과 경로고 곧 서비스할 URL 로 연결된다.
  >
  > 그래서 마이그레이션 초기에는 RoR Controller 함수 하나하나를 api 경로로 만드려고 했는데, Next.js 파일 기반 라우팅 패턴이 편리하므로 이 패턴을 그대로 활용했다는 의미이다.
<img width="2102" alt="스크린샷 2024-11-18 오후 8 24 35" src="https://github.com/user-attachments/assets/949481ee-9885-4e12-b467-218494a1c92c">

### 5. 서버 컴포넌트 활용의 어려움 [(10:00-11:14)](https://youtu.be/esOYhqevuqw?si=NsEDKnwu0lTOJX9a&t=601)
- 자체 디자인 시스템이 Chakra UI 기반으로 제작되어 있다보니, 서버 측 렌더링과 호환되지 않는 코드가 있다는 문제점이 있었다.
- 그래서 client-side 에서 그려져야하는 코드는 `'use client'` 를 사용하되, 이 지시문을 최대한 아래로 이동시키는 것을 목표로 했다.
- 아래 핑크색 원이 자체 디자인 시스템의 provider 이다.
<img width="2093" alt="스크린샷 2024-11-18 오후 8 27 32" src="https://github.com/user-attachments/assets/c611a13f-1412-4777-bc92-d44ad2502ee4">

### 6. Next.js 에서 얻은 다른 이점들 [(11:14-12:24)](https://youtu.be/esOYhqevuqw?si=qr_GC_Vnf1Mkf1IB&t=674)
- **라우터 캐싱**: 범용 header, footer 를 가지고 있어서, 페이지 로드 시간을 단축하는데 큰 도움이 되었다.
- **Error Boundary**: 모든 오류를 한곳에서 처리하여 편리하게 사용했다.
- **Loading, Suspense**: 데이터를 페칭해오는 동안 사용자에게 스켈레톤을 노출시켜 의미있는 UI를 제공했다.

### 7. Vercel Preview를 통한 협업 및 접근성 향상 [(12:24-14:08)](https://youtu.be/esOYhqevuqw?si=G6h018pSmzQF1Goe&t=744)
- Vercel Preview를 통해 반복적인 개발 속도를 높이고 의사 결정 과정을 10배 단축하여 효율성을 극대화했다.
- Vercel Preview를 활용하여 개발 담당자들과 접근성 컨설턴트 간의 협업을 강화하고 빠른 피드백을 통해 접근성을 크게 개선했다.

### 8. 향후 계획, Q&A [(14:08-)](https://youtu.be/esOYhqevuqw?si=Y5KQzMHrhkR5jsCs&t=848)
- 향후 Next Image를 활용한 이미지 최적화를 할 계획.
- 접근성 평가는 전문 접근성 컨설턴트를 통해 이루어진다. 개발 단계에서는 light house 를 이용한다.
- 영국 도서관 해킹 사건으로 인해, 현재 보안 구성이 되어있는 AWS 에 배포 중인데 Vercel 에서도 가능하다면 하고 싶다.
