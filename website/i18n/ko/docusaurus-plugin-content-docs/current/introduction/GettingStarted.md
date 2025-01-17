---
id: getting-started
title: Redux 시작하기
description: '소개 > 시작하기: Redux를 배우고 사용하기 위한 자료'
---

import LiteYouTubeEmbed from 'react-lite-youtube-embed';
import 'react-lite-youtube-embed/dist/LiteYouTubeEmbed.css'

# Redux 시작하기

Redux는 자바스크립트 앱을 위한 예측 가능한 상태 컨테이너입니다.

Redux는 여러분이 일관적으로 동작하고, 서로 다른 환경(서버, 클라이언트, 네이티브)에서 작동하고, 테스트하기 쉬운 앱을 작성하도록 도와줍니다. 여기에 더해서 [시간여행형 디버거와 결합된 실시간 코드 수정](https://github.com/reduxjs/redux-devtools)과 같은 훌륭한 개발자 경험을 제공합니다.

여러분은 Redux를 [React](https://reactjs.org)나 다른 뷰 라이브러리와 함께 사용할 수 있습니다. Redux는 매우 작지만(의존 라이브러리 포함 2kB), 사용 가능한 애드온은 매우 많습니다.

## 설치

### Redux Toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org)은 Redux 로직을 작성하기 위해 저희가 공식적으로 추천하는 방법입니다. RTK는 Redux 앱을 만들기에 필수적으로 여기는 패키지와 함수들을 포함합니다. 대부분의 Redux 작업을 단순화하고, 흔한 실수를 방지하며, Redux 앱을 만들기 쉽게 해주는 모범 사례를 통해 만들어졌습니다.

RTK는 [저장소 준비](https://redux-toolkit.js.org/api/configureStore),
[리듀서 생산과 불변 수정 로직 작성](https://redux-toolkit.js.org/api/createreducer),
[상태 "조각" 전부를 한번에 작성](https://redux-toolkit.js.org/api/createslice) 등 일반적인 작업들을 단순화해주는 유틸리티를 포함하고 있습니다.

여러분이 첫 프로젝트에 Redux를 새로 도입하는 입문자든 기존 앱을 단순화하고 싶은 경험자든 상관 없이,
**[Redux Toolkit](https://redux-toolkit.js.org/)**은 더 나은 Redux 코드를 만들게
도와줍니다.

Redux Toolkit은 NPM에서 패키지로 받아 모듈 번들러나 Node 앱에서 사용 가능합니다.

```bash
# NPM
npm install @reduxjs/toolkit

# Yarn
yarn add @reduxjs/toolkit
```

### React Redux 앱 만들기

React와 Redux로 새 앱을 만들기 위해 추천하는 방법은 [Create React App](https://github.com/facebook/create-react-app)를 위한 [공식 Redux+JS 템플릿](https://github.com/reduxjs/cra-template-redux)을 사용하는 것입니다. 이를 통해 **[Redux Toolkit](https://redux-toolkit.js.org/)**와 React Redux가 React 컴포넌트와 통합되는 이점을 누릴 수 있습니다.

```sh
npx create-react-app my-app --template redux
```

### Redux 코어

Redux 코어 라이브러리는 NPM에서 패키지로 받아 모듈 번들러나 Node 앱에서 사용 가능합니다.

```bash
# NPM
npm install redux

# Yarn
yarn add redux
```

`window.Redux` 전역변수를 선언해주는 UMD 패키지도 사용 가능합니다. UMD 패키지는 [`<script>` 태그](https://unpkg.com/redux/dist/redux.js)로 바로 사용 가능합니다.

자세한 사항은 [설치](Installation.md) 페이지를 보세요.

## 기본 예제

여러분의 앱의 상태 전부는 하나의 저장소(_store_)안에 있는 객체 트리에 저장됩니다.
상태 트리를 변경하는 유일한 방법은 무엇이 일어날지 서술하는 객체인 액션(_action_)을 보내는 것 뿐입니다.
액션이 상태 트리를 어떻게 변경할지 명시하기 위해 여러분은 리듀서(_reducers_)를 작성해야 합니다.

```js
import { createStore } from 'redux'

/**
 * 이것이 (state, action) => state 형태의 순수 함수인 리듀서입니다.
 * 리듀서는 액션이 어떻게 상태를 다음 상태로 변경하는지 서술합니다.
 *
 * 상태의 모양은 당신 마음대로입니다: 기본형(primitive)일수도, 배열일수도, 객체일수도,
 * 심지어 Immutable.js 자료구조일수도 있습니다.  오직 중요한 점은 상태 객체를 변경해서는 안되며,
 * 상태가 바뀐다면 새로운 객체를 반환해야 한다는 것입니다.
 *
 * 이 예제에서 우리는 `switch` 구문과 문자열을 썼지만,
 * 여러분의 프로젝트에 맞게
 * (함수 맵 같은) 다른 컨벤션을 따르셔도 좋습니다.
 */
function counter(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1
    case 'DECREMENT':
      return state - 1
    default:
      return state
  }
}

// 앱의 상태를 보관하는 Redux 저장소를 만듭니다.
// API로는 { subscribe, dispatch, getState }가 있습니다.
let store = createStore(counter)

// subscribe()를 이용해 상태 변화에 따라 UI가 변경되게 할 수 있습니다.
// 보통은 subscribe()를 직접 사용하기보다는 뷰 바인딩 라이브러리(예를 들어 React Redux)를 사용합니다.
// 하지만 현재 상태를 localStorage에 영속적으로 저장할 때도 편리합니다.

store.subscribe(() => console.log(store.getState())))

// 내부 상태를 변경하는 유일한 방법은 액션을 보내는 것뿐입니다.
// 액션은 직렬화할수도, 로깅할수도, 저장할수도 있으며 나중에 재실행할수도 있습니다.
store.dispatch({ type: 'INCREMENT' })
// 1
store.dispatch({ type: 'INCREMENT' })
// 2
store.dispatch({ type: 'DECREMENT' })
// 1
```

상태를 바로 변경하는 대신, **액션**이라 불리는 평범한 객체를 통해 일어날 변경을 명시합니다. 그리고 각각의 액션이 전체 애플리케이션의 상태를 어떻게 변경할지 결정하는 특별한 함수인 **리듀서**를 작성합니다.

보통의 Redux 앱에는 하나의 루트 리듀서 함수를 가진 단 하나의 저장소가 있습니다. 앱이 커짐에 따라 루트 리듀서를 상태 트리의 서로 다른 부분에서 개별적으로 동작하는 작은 리듀서들로 나눌 수 있습니다. React 앱을 하나의 루트 컴포넌트에서 시작해서 여러 작은 컴포넌트의 조합으로 바꾸는 것과 동일합니다.

이런 아키텍처가 카운터 앱에서는 너무 과한 것처럼 보이지만, 크고 복잡한 앱에서는 이 패턴의 확장성이 잘 드러납니다. 액션에 따른 모든 변경을 추적할 수 있기 때문에, 매우 강력한 개발자 도구를 가능하게 해주기도 합니다. 여러분은 사용자 세션을 기록한 다음 액션 하나하나를 다시 실행해 볼 수 있습니다.

## Redux 배우기

Redux를 배우는 데 도움이 될 다양한 자료가 있습니다.

### Redux 핵심 튜토리얼

[**Redux 핵심 튜토리얼**](../tutorials/essentials/part-1-overview-concepts.md)은 추천 API와 모범 사례를 통해 어떻게 하면 Redux를 올바르게 사용할 수 있는지를 가르쳐주는 "하향식" 튜토리얼입니다. 여기서부터 시작하기를 추천합니다.

### Redux Fundamentals Tutorial

[**Redux 기반 튜토리얼**](../tutorials/fundamentals/part-1-overview.md)은 요약 없이 기초 원리부터 "Redux가 어떻게 동작하는가"와 왜 Redux 표준 패턴이 있는지를 알려주는 "상향식" 튜토리얼입니다.

### Additional Tutorials

- The Redux repository contains several example projects demonstrating various aspects of how to use Redux. Almost all examples have a corresponding CodeSandbox sandbox. This is an interactive version of the code that you can play with online. See the complete list of examples in the **[Examples page](./Examples.md)**.
- Redux creator Dan Abramov's **free ["Getting Started with Redux" video series](https://app.egghead.io/playlists/fundamentals-of-redux-course-from-dan-abramov-bd5cc867)** and **[Building React Applications with Idiomatic Redux](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)** video courses on Egghead.io
- Redux maintainer Mark Erikson's **["Redux Fundamentals" conference talk](https://blog.isquaredsoftware.com/2018/03/presentation-reactathon-redux-fundamentals/)** and [**"Redux Fundamentals" workshop slides**](https://blog.isquaredsoftware.com/2018/06/redux-fundamentals-workshop-slides/)
- Dave Ceddia's post [**A Complete React Redux Tutorial for Beginners**](https://daveceddia.com/redux-tutorial/)

### Other Resources

- The **[Redux FAQ](../FAQ.md)** answers many common questions about how to use Redux, and the **["Recipes" docs section](../usage/index.md)** has information on handling derived data, testing, structuring reducer logic, and reducing boilerplate.
- Redux maintainer Mark Erikson's **["Practical Redux" tutorial series](http://blog.isquaredsoftware.com/series/practical-redux/)** demonstrates real-world intermediate and advanced techniques for working with React and Redux (also available as **[an interactive course on Educative.io](https://www.educative.io/collection/5687753853370368/5707702298738688)**).
- The **[React/Redux links list](https://github.com/markerikson/react-redux-links)** has categorized articles on working with [reducers and selectors](https://github.com/markerikson/react-redux-links/blob/master/redux-reducers-selectors.md), [managing side effects](https://github.com/markerikson/react-redux-links/blob/master/redux-side-effects.md), [Redux architecture and best practices](https://github.com/markerikson/react-redux-links/blob/master/redux-architecture.md), and more.
- Our community has created thousands of Redux-related libraries, addons, and tools. The **["Ecosystem" docs page](./Ecosystem.md)** lists our recommendations, and there's a complete listing available in the **[Redux addons catalog](https://github.com/markerikson/redux-ecosystem-links)**.

## 도움과 논의

The **[#redux channel](https://discord.gg/0ZcbPKXt5bZ6au5t)** of the **[Reactiflux Discord community](http://www.reactiflux.com)** is our official resource for all questions related to learning and using Redux. Reactiflux is a great place to hang out, ask questions, and learn - come join us!

You can also ask questions on [Stack Overflow](https://stackoverflow.com) using the **[#redux tag](https://stackoverflow.com/questions/tagged/redux)**.

If you have a bug report or need to leave other feedback, [please file an issue on the Github repo](https://github.com/reduxjs/redux)

## Redux를 사용해야 할까요?

Redux는 상태를 관리하기에 좋은 도구이지만 여러분의 상황에 적당한지는 따져 보아야 합니다. **단지 누군가가 사용하라고 했다는 이유만으로 Redux를 사용하지는 마세요 - 시간을 들여서 잠재적인 이점과 그에 따르는 단점을 이해하세요.**

Redux를 사용하기 적절한 때를 알기 위한 몇 가지 제안이 있습니다:

- 계속해서 바뀌는 상당한 양의 데이터가 있다
- 상태를 위한 단 하나의 근원이 필요하다
- 최상위 컴포넌트가 모든 상태를 가지고 있는 것은 더 이상 적절하지 않다

> **Redux가 어떻게 사용되어야 하는지에 대한 여러 생각들을 보려면:**
>
> - **[Redux FAQ: When should I use Redux?](../faq/General.md#when-should-i-use-redux)**
> - **[당신에게 Redux는 필요 없을지도 모릅니다.](https://medium.com/@Dev_Bono/당신에게-redux는-필요-없을지도-모릅니다-b88dcd175754)**
>
> - **[The Tao of Redux, Part 1 - Implementation and Intent](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/)**
>
> - **[The Tao of Redux, Part 2 - Practice and Philosophy](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-2/)**
> - **[Redux FAQ](../FAQ.md)**
