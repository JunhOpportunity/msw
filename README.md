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

### Handler 생성

가짜 응답을 작성해야 하는데요, 이때 REST API 응답 방법과 유사하니 이미 지식이 있으신 분들이라면 쉽게 작성이 가능합니다.

주의해야 할 점은, MSW의 버전에 따라 코드 작성 방법이 다르다는 점입니다.

1.X 버전은 `rest` 라는 객체를 사용하지만 2.X 버전은 `http` 라는 객체를 사용합니다.

또한, 사용하는 객체가 다르기 때문에 객체 내에서 호출되는 속성도 약간의 차이가 존재합니다.

따라서 본인의 버전에 맞게 작성해주셔야 합니다.

저는 React 기반의 프로젝트에서는 1.X 버전을, Next 기반의 프로젝트에서는 2.X 버전을 사용해 MSW를 적용시켜 보았으므로 각 버전의 예시 코드들을 모두 보여드리도록 하겠습니다.

(현재 NextJS에서는 MSW를 완벽하게 지원하고 있지 않은 상태이기 때문에 MSW를 적용하기 위해서는 공식문서에서 제시하는 과정 외에 추가적인 작업이 필요한 상태입니다. 또한 추가적인 과정이 MSW에서 정식으로 지원하는 코드가 아니기도 하고 일시적인 해결 방안이라 MSW 측에서도 권장하지 않는 방법입니다. 따라서 모든 코드는 React에 MSW를 적용하는 상황에 대한 예시라는 것을 알고 계셔야 합니다.)

```jsx
// 1.X
import { rest } from "msw";

export const handlers = [
	rest.get('/api/login', (req, res, ctx) => {
    return res(
			ctx.status(200), 
			ctx.json({
				userName: "JunHo",
				NickName: "JunhOpportunity",
				userImage: "https://.../profile/1",
			}),
			ctx.set('Cache-Control', 'no-cache'),
			ctx.cookie('connect.sid', 'msw-cookie', {
				httpOnly: true,
				path: '/'
			}),
		);
  })
];
```

```jsx
// 2.X
// /src/mock/handlers.ts
import { http, HttpResponse } from 'msw'
 
export const handlers = [
  http.get('/api/login', () => {
    return HttpResponse.json(
			{
				userName: "JunHo",
				NickName: "JunhOpportunity",
				userImage: "https://.../profile/1",
			},
			{
				headers: {
					Cache: "no-cache",
					'Set-Cookie': 'connect.sid=;HttpOnly;Path=/,Max-Age=0'
				}
			}
		)
  })
];
```

[Mocking responses](https://mswjs.io/docs/basics/mocking-responses)

### 서비스 워커 생성 및 삽입

Handler를 작성한 후에는 이를 기반으로 MSW 서버를 생성해야 합니다.

이때 필요한 것이 `setupWorker()` 함수입니다.

작성한 Handler를 기반으로 MSW 서버를 생성해야 하기 때문에 위의 `handlers` 를 setupWorker() 함수의 인자로 넘겨줍시다.

```jsx
// 1.X
import { setupWorker } from 'msw'
import { handlers } from './handlers'
 
export const worker = setupWorker(...handlers)
```

```jsx
// 2.X
// src/mocks/browser.ts
import { setupWorker } from 'msw/browser'
import { handlers } from './handlers'
 
export const worker = setupWorker(...handlers)
```

서비스 워커를 생성한 뒤에 index.js 파일에서 삽입하면 됩니다.

```tsx
import { worker } from './mock/browser';

if(process.env.REACT_APP_API_MOCKING === "enabled") {
  worker.start({ onUnhandledRequest: 'bypass' });
}
```
