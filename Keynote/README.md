## Next.js 의 비전
개발자가 간단한 웹 사이트부터 매우 복잡한 앱까지 쉽게 구축하도록 지원하는 것

1. Make it Work
2. Make it Right
3. Make it Blazing Fast

## Turbo Pack

### 3가지 주요 사항

1. 이전의 컴파일러와의 동등함
2. 에코시스템 호환성 (Webpack Loaders, React Compiler, CSS Solutions, etc)
3. 최적화된 콜드 컴파일

### 향상점

- 초기 컴파일 시간이 최대 50%
- 코드 업데이트 속도가 90% 향상됨

## Turbo Pack + Cache (Experimental)

### 실제 컴파일 시간 비교
대용량의 예제 코드를 대상으로 테스트 해본 결과

- 기존 Compiler: 7s
- Turbopack: 4.5s
- Turbopack + cache: 317ms
