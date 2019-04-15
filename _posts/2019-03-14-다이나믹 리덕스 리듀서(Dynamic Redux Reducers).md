---
layout: post
title: "다이나믹 리덕스 리듀서(Dynamic Redux Reducers)"
date: 2019-03-14 15:20:00 +0900
categories: React Redux

---

역자글: 본 글은 Tilergaw님의 https://tylergaw.com/articles/dynamic-redux-reducers/ 글을 허락을 받아 번역 진행하였습니다. 아래 부터는 번역된 본문입니다.



이 글은 제가 React/Redux 프로젝트를 진행하며 필요했던 것들에 대하여 적었습니다. 전에도 겪었던 문제이지만, 제가 직접 이런 걸 해결해야하는 건 처음이었어요. 저한테는 어려운 문제였고, 이 복잡한 문제를 해결하기 위해 천천히 많은 시간을 사용하여 익혔습니다. 나와 같은 문제를 겪는 누군가에게 도움이 되었으면 해요.
이제 진행했던 것에 대해서 자세히 설명해볼게요. 이건 [라이브 데모](https://qk3n9xmm3w.codesandbox.io)이고요.  이건 [수정가능한 sandbox](https://codesandbox.io/s/qk3n9xmm3w)입니다. 변화를 확인하기 위한 가장 좋은 방법은 [Redux 개발자 도구 확장 플러그인을 사용하는 것입니다.](https://github.com/zalmoxisus/redux-devtools-extension)

글을 진행하기에 앞서 전 여러분이 Redux에 대한 이해가 있고, `react-redux`를 사용하여 진행한다는 걸 가정하겠습니다. 또한 비슷한 문제를 겪고 있고, 해당 문제에 대한 해결책을 찾고 있다고 가정합니다.

## 난 대체 무엇을, 왜 하고 있는가?

보통 Redux를 사용하면 여러분은 `createStore`를 사용하여 스토어(store)를 만들때 리듀서(reducer) 함수를 제공하잖아요. 전 제가 나중에 필요할 때 리듀서 함수를 추가 하고 싶었어요.

코드 스플리팅[^code-splitting]을 사용하면, `createStore` 를 사용하는 게 불가능하기 때문에 많은 사람들에게 필요하다고 생각합니다. 다이나믹 리듀서를 써야할 완벽한 상황인거죠.

제 프로젝트는 코드 스플리팅을 사용하지 않습니다. 다이나믹 리듀서를 꼭 쓸 필요는 없고 그냥 제 취향입니다. 모듈에 대한 정보들이 프로젝트 구조에 흩어져 있는 건 원치 않았어요. 전 각각의 특징들이 각각의 디렉토리에 격리되어 있길 원했습니다. 리듀서와 컴포넌트, 스타일 등이 한 곳에 있는 것을 의미하죠. 메인 리듀서를 생성할 때 리듀서를 삽입하면 되지만, 이렇게 되면 모듈 리듀서가 메인 리듀서에 결속되어 버립니다.

## 이미 존재하는 해결책

구글링을 통해 [이런 해결책](https://stackoverflow.com/a/33044701)을 찾았는데요. Dan Abramov의 답변이 좋은 방법이라고 생각했고 제 코드에서도 대부분 그의 코드를 사용했습니다.
Dan의 답변에서 리듀서를 주입하는 예시들은 모두 설득력이 있었습니다. 전 React Router를 사용하는 중이지만, 그가 적어둔 방식대로 사용하진 않았어요. 하지만 또 이 방식대로 제 라우트를 바꾸고 싶진 않았죠. 또한 공식 문서에서 그가 예시로 든 방법을 찾을 수도 없었어요. 그래서 그냥 복붙하는 건 피하려고 했죠. 그리고 완벽하게 코드를 이해한 뒤 제 프로젝트에 추가 하고 싶었습니다. 
일단 제가 찾은 두가지를 알려드리자면, [redux-dynamic-reducer](https://github.com/ioof-holdings/redux-dynamic-reducer)와 [paradux](https://github.com/asteridux/paradux)인데요. 전 의존성을 추가로 집어넣고 싶진 않아서 사용하진 않았습니다만, 여러분들한테 도움이 될 수도 있을 것 같아요.

## 데모에서는...

[데모](https://qk3n9xmm3w.codesandbox.io)는 `/records` 로 연결되는 간단한 페이지입니다. 페이지가 로드되면, Redux state 트리는 두개의 키를 포함합니다. 각각의 리듀서 함수는 스토어가 생성될때 연결됩니다.
페이지에는 `/records` 라는 링크가 있죠. 여러분이 링크를 타고 이동할 때 Records에 또 다른 리듀서 함수를 추가 합니다. 나머지 포스팅 내용은 이걸 어떻게 처리하는 지에 대해 적을 거에요. 

## 코드

[CodeSandbox](https://codesandbox.io/s/qk3n9xmm3w)를 이용하셔서 따라하실 수 있어요. 루트 리듀서인 `/rootReducer.js`로 먼저 시작해보겠습니다.

```jsx
import { combineReducers } from "redux";
import layout from "./reducers/layout";
import home from "./reducers/home";
/**
 * @param {Object} - key/value of reducer functions
 */
const createReducer = asyncReducers =>
  combineReducers({
    home,
    layout,
    ...asyncReducers
  });
export default createReducer;
```

Dan의 스택오버플로우 답변의 내용을 가져왔는데요. 이 코드는 `layout`과 `home` 두가지 리듀서 함수를 가집니다. 이것들은 글로벌 리듀서이고 모듈의 레벨에 있는 게 아니에요. 즉 루트 리듀서에 들어갑니다. 
여기서 중요한 점은 `asyncReducers` 파라미터입니다. 이 코드를 통해 `combineReducers`에 리듀서를 나중에 추가할 수 있죠. 
다음은 `/initializeStore.js`에서 스토어를 생성하는 것 입니다. 다시 한번 말씀드리지만 대부분은 Dan의 예제에서 가져온 거에요.

```jsx
import { createStore } from "redux";
import createReducer from "./rootReducer";
const initializeStore = () => {
  const store = createStore(createReducer());
  store.asyncReducers = {};
  store.injectReducer = (key, reducer) => {
    store.asyncReducers[key] = reducer;
    store.replaceReducer(createReducer(store.asyncReducers));
    return store;
  };
  return store;
};
export default initializeStore;
```

`initializeStore`의 첫줄은 `createReducer`에서 Redux 스토어를 초기화 하는 부분입니다. 보통 Redux를 사용할 때 여러분들이 해야하는 거죠. 이 스토어는 `home`, `layout`과 함께 준비되면서 설정됩니다. 
`createStore`는 순수한 객체를 반환하고, 이를 통해 아이템들을 추적할 수 있는 장점을 얻을 수 있습니다. `store.asyncReducers`를 이용하여 다이나믹 리듀서를 제공하도록 할 것입니다. `store.injectReducer` 는 Dan의 예제와는 다릅니다. `injectAsyncRebucer`와 같은 동작을 하지만 편리하게 사용하기 위해 `store` 객체에 붙혔어요. 나중에 다시 보여드리겠습니다.
`injectReducer` 는 두가지 역할[^responsibilities]을 갖습니다. 모든 다이나믹 리듀서는 `asyncReducers`에 저장합니다. 이렇게 하면 `injectReducer`를 호출 할때 다른 다이나믹 리듀서를 잃지 않게됩니다. `replaceReducer`는 Redux의 부분이기 때문에 건드리지 않습니다.  `replaceReducer`를 호출하면 넘겨준 리듀서로 대체됩니다. 

### 적용하기 까다로운 부분

모든 게 괜찮게 돌아가는 듯 보였지만, 금방 아니라는 걸 깨달았죠. 스토어가 있고, 새로운 리듀서에 추가해야 할 함수가 있죠. 하지만 어떻게 접근해서 호출해야 할까요? 전 또 미친듯한 구글링을 통해서 찾아보았지만, 저와 같은 경우를 찾지 못했어요. 그래서 그냥 해결방법을 생각해보았죠.
어디서 `store` 객체에 접근해야 하는 지 생각하는 데 오래 걸렸어요.`/index.js`의 `Provider` 컴포넌트를 사용하는 부분에서 힌트를 얻었어요. 이건 `React/Redux`프로젝트의 표준이거든요.

```jsx
import React from "react";
import { render } from "react-dom";
import { Provider } from "react-redux";
import initializeStore from "./initializeStore";
import App from "./App";
const store = initializeStore();
render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

`store`를 `Provider`에 넘겨주면, 모든 자식 컴포넌트들은 connect 함수를 통해 사용할 수 있게 됩니다. `store`에 대해 많이 읽고 배우다 보니 각 컴포넌트의  `context` 에서도 가능하다는 것도 알았어요. React `context`에 대해서 알아보셨다면, 이렇게 사용하면 안된다는 걸 아실거에요. 하지만 제 목적을 위해서라면 괜찮겠다 싶을 정도까지는 격리되어 보였어요. 이게 맞는지 틀린지는 시간이 흐른 뒤에 알 수 있을거에요. 제 `context` 사용법에 대한 자세한 내용은 다시 말씀드릴게요.

## 조각들을 함께 두기

전 약간의 코드로 리듀서를 추가하고 싶습니다. `/withReducer.js`에서 고차 컴포넌트[^higher-order component] 를 사용합니다.

```jsx
import React from "react";
import { object } from "prop-types";
const withReducer = (key, reducer) => WrappedComponent => {
  const Extended = (props, context) => {
    context.store.injectReducer(key, reducer);
    return <WrappedComponent {...props} />
  };
  Extended.contextTypes = {
    store: object
  };
  return Extended;
};
export { withReducer };
```

그리고 `routes/Records/Records.js`에서의 사용 예시입니다.

```jsx
import { withReducer } from "../../withReducer";
import reducer from "./ducks";
const Records = () => (...);
export default withReducer("records", reducer)(Records);
```

`Records.js`와 함께 사용법을 알려드리죠. `routes/Records/ducks/index.js`로 부터 `records` 리듀서를 삽입해요. 리듀서는 그닥 다른 일을 하진 않습니다.  하드 코딩된 초기 state를 세팅해주고 그대로 리턴합니다. 컴포넌트는 컨테이너 컴포넌트 같은 역할을 하죠. 이걸 `connect`해줘야 하지만 데모에서는 그냥 하지 않았습니다.

중요한 부분은 마지막 줄이에요. `withReducer`를 호출하고 'records'라는 키와 record reducer를 제공합니다. 그러면 반환된 함수를 호출하고, `Records` 컴포넌트를 제공하게 되는 거죠.

`Records`는 React 컴포넌트에요. React Router`<Route />`의 `component ` 값을 삽입합니다. 

### withReducer 컴포넌트

`withReducer`는 고차 컴포넌트에요. `key`파라미터는 Redux state 트리의 키가 되어집니다. `reducer`파라미터는 추가할 reducer를 적어줘요. 그건 `WrappedComponent`라는 하나의 파라미터를 갖는 함수를 반환하는데, `WrappedComponent`는  정상적인 React component라고 예상합니다. 이전의 사용 예시에서, `Records` 컴포넌트 였어요. 자 이제 `withReducer`의 중요한 부분으로 넘어가 봅시다. 이 부분은 저나 여러분들 다 혼란스러울 수 있습니다.



```jsx
...
Extended.contextTypes = {
  store: object
};
...
```

`Extended`는 상태가 없는[^stateless] 컴포넌트 입니다. 그러므로 꼭 contextTypes 프로퍼티가 `context`로 부터 정의해야합니다. React 문서를 읽어보면,

> 만약 contextTypes이 정의되어 있으면 함수의 속성으로 정의되어 있으면, 상태가 없는 함수 컴포넌트들 또한 context에 의해 참조될 수 있습니다.
>
> <cite>[reactjs.org/docs/context.html#referencing-context-in-stateless-functional-components](https://reactjs.org/docs/context.html#referencing-context-in-stateless-functional-components)</cite>
>
> 속성을 정의하여 `contextTypes`에서  컴포넌트(`store`)에 접근하고 싶었습니다. `prop-types` 라이브러리의 `object` 타입을 사용했어요. 
>
> `contextTypes`를 정의할 때 `context`를 두번째 파라미터로 전달 받습니다. `Extended` 선언에서 확인하실 수 있어요.

```
...
const Extended = (props, context) => {...}
...
```

자 그럼 `Extended`는 이제 `store` 객체에 접근 할 수 있습니다. 왜냐하면 `<Provider store={store}>` 가 `/index.js`에 있기 때문이죠.  `context`를 통해 모든 자식 컴포넌트들은 다 사용 가능해집니다. 

이러면 `getChildContext`와 `childContextTypes`가 `Provider.js` 에서  발생됩니다. 여러분들께서 `context`의 예제들을 찾아보시면 코드가 읽기 좋다는 걸 아시게 될겁니다. [^deprecated]

`initializeStore.js`에서 store 객체에 `store.injectReduecr`라는 함수를 만들었죠. 자 이제 그 함수를 이용하여 새로운 reducer를 추가해보겠습니다.



```
...
const Extended = (props, context) => {
  context.store.injectReducer(key, reducer);
  return <WrappedComponent {...props} />;
};
...
```

컴포넌트를 변경하지 않았어요. `Extended`는 그저 각 컴포넌트의 속성만을 반환합니다.

## 이게 동작하는 지 어떻게 확인해보죠?

자 일단 코드는 동작하는 것처럼 보입니다만, 이런 류는 타입의 변화를 시각화하긴 어렵습니다. 제가 위에서 이미 [Redux 개발자 도구 확장 플러그인](https://github.com/zalmoxisus/redux-devtools-extension)을 언급했었죠? 전 개인적으로 이게 제일 좋았어요.  [데모](https://qk3n9xmm3w.codesandbox.io)에서 스토어를 만들 때 개발도구 스니펫을 추가해두었습니다. 만약 여러분들이 확장 플러그인을 설치 한 뒤 Redux panel을 본다면, 새로운 리듀서가 state 트리에서 변경되는 걸 보실 수 있을겁니다.

![Animated gif showing a new reducer added in Redux devtools Chrome extension.](https://d3vv6lp55qjaqc.cloudfront.net/items/1n0J3V3G0j1X2a1M1C00/Screen%20Recording%202018-01-07%20at%2009.52%20PM.gif)

데모에서 결과를 더 보여주기위해, store의 데이터를 보여주기 위해 `connect`를 이용하여 record 라우트와 연결하였습니다. 

```
...
const mapStateToProps = (state, props) => {
  const { match: { params: { id } } } = props;
  return {
    recordId: id,
    record: state.records[id] || {}
  };
};
export default connect(mapStateToProps)(Record);

```

모든 코드들은 `/routes/Records/routes/Record.js` 안에 있어요.

## 해결 방법

제가 위에서 이건 React/Redux 프로젝트 할때 보통 여러가지 이유로 필요한 것이라고 언급했었죠? 전 전에도 비슷하지만 다른 메소드들로 다이나믹 라우트를 사용했습니다. 다른 사람들은 다른 방법들을 가지고 있었고, 이것 또한 방법 중 하나일 뿐 정답은 아닐 수 있습니다.

만약 여러분들한테 이 글이 도움이 되었거나 더 나은 방법이 있으면 알려주세요. 항상 더 좋은 방법은 있다고 생각합니다.

[^responsibilities]: 역자주) 원문에서는 responsibilities(책임)로 표현되었지만 역할로 번역하였습니다.
[^higher-order component]: HOC, https://reactjs-kr.firebaseapp.com/docs/higher-order-components.html
[^deprecated]: 역자주) 현재 getChildContex와 childContextTypes의 경우 deprecated 되어 다른 방식으로 구동 됩니다. 자세한 내용은 <https://reactjs.org/docs/legacy-context.html> 를 참고하세요.
[^code-splitting]: 역자주) 코드 스플리팅에 관한 한국어 글은 velopert님의 다음 글을 참조해주세요.  https://velog.io/@velopert/react-code-splitting 
[^stateless]: 마땅한 대체어가 생각나지 않아, 상태가 없는으로 해석 했습니다.
