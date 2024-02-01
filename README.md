# MSW (Mock Service Worker)

# MSW (v1.X)

## Set up

### CRA

`$ npx create-react-app my-app --template typescript`

### MSW 라이브러리 설치

`$ npm i msw --save-dev`

### —save-dev 는 무엇인가?

+)

```json
"devDependencies": {
  "msw": "^2.1.5"
}
```

### CLI 도구 생성

`$ npx msw init ./public --save`

MSW에서 제공해주는 CLI 도구인 mockServiceWorker.js 파일을 ./public 경로에 생성합니다.

서비스 워커를 통해 작동하는 MSW를 서비스 워커 등록을 위해 기본 코드를 제공하는 것입니다.

만약 이 도구를 MSW에서 제공하지 않고 우리가 직접 작성해야 했다면 서비스 워커에 대해서도 공부해야 했을 테니까 우리의 작업 시간을 단축 시켜준 셈이라고 할 수 있습니다.

그렇다고 이대로 넘어가지 말고, 서비스 워커에 대해서도 추가적으로 공부를 해보며 MSW에 대한 이해도를 높여보는 것도 좋을 것 같습니다.

(Service Worker에 대해서는 많은 자료들이 있으니까 참고하시기 바랍니다. [MDN-Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) [카카오 기술블로그-서비스 워커에 대해 알아보고 Mock Response 만들기](https://fe-developers.kakaoent.com/2022/221208-service-worker/) [web.dev-서비스 워커](https://web.dev/learn/pwa/service-workers?hl=ko))

