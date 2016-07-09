프로리엑트 책 관련 코드는 아래의 링크에서 찾아 볼수 있습니다.
[프로리엑트](https://github.com/pro-react/)  

## 리엑트가 이야기 하는 리엑트
### JUST THE UI

React.js는 UI 컴포넌트를 만들기 위한 라이브러리입니다. 컴포넌트 지향 프레임워크는 여러 가지가 있지만 React.js는 정말 UI 컴포넌트만 지원합니다. 비록 지원하는 범위는 작지만, 애플리케이션을 만드는 방법을 크게 바꿀 수 있다는 점이 재미있습니다. 또한, 이해 비용이 적어 도입하기 쉬우며 Backbone.js의 뷰 부분을 React.js로 구현하거나 Angular.js의 directives를 React.js를 사용해 구현하는 등 여러 환경과 조합해 사용할 수 있습니다.

### VIRTUAL DOM

React.js는 자바스크립트 내에 DOM Tree와 같은 구조체를 VIRTUAL DOM으로 갖고 있습니다. 다시 그릴 때는 그 구조체의 전후 상태를 비교하여 변경이 필요한 최소한의 요소만 실제 DOM에 반영합니다. 따라서 무작위로 다시 그려도 변경에 필요한 최소한의 DOM만 갱신되기 때문에 빠르게 처리할 수 있습니다.

### DATA FLOW

React.js는 단방향 데이터 흐름을 지향합니다. 따라서 Angular.js의 양방향 데이터 바인딩을 사용할 때처럼 작성할 코드의 양이 확연히 줄거나 하지는 않습니다. 그렇지만, 애플리케이션의 데이터를 관리하는 모델 컴포넌트가 있고 그 데이터를 UI 컴포넌트에 전달하는 단순한 데이터 흐름으로 이해하고 관리하기 쉬운 애플리케이션을 만들 수 있습니다.  
([현재는 내용이 조금 바꾸여 있네요](https://facebook.github.io/react/index.html))  
  
[JUST THE UI,VIRTUAL DOM,DATA FLOW](http://blog.coderifleman.com/post/122232296024/reactjs%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8B%A41)


[[환경설정하기]]  
code [code](https://github.com/otwm/ProReactStudy/tree/master/kanban)  

[map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map)  
[filter](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)  
[es6 import](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import)  
[es6 export](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/export)  
  
컴퍼넌트, 리엑트 엘리먼트,...  

리엑트 컴퍼넌트에서 super 사용시 기본적으로 인자는 props, context, updater를 제공 받는다.(리엑트 컴퍼넌트 소스 및 아래 링크  참조)  
```javascript
/**
 * Base class helpers for the updating state of a component.
 */
function ReactComponent(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue;
}
```
[Reusable Components es6 classes](https://facebook.github.io/react/docs/reusable-components.html#es6-classes)  
[React Life Cycle](https://github.com/studye/react/wiki/React-Life-Cycle)  
  
  
렌더 등. jsx, 트랜스 파일러, 웹팩 , class

