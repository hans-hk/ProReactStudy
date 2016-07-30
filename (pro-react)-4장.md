## 해야할 것들
* css 애니메이션에 대한 기본적인 이해
* 리액트에서 애니메이션(ReactCSSTransitionGroup) 
* 드래그 앤 드랍(react-dnd)
* 칸반 : 애니메이션과 드래그 앤드 드롭
* 트로틀링 함수

## 리액트의 애니메이션
여러 가지의 애니메이션이 있지만, 여기서는 [ReactCssTransitionGroup](ReactCssTransitionGroup) 만을 다룹니다.  
먼저, css 애니메이션을 알아봅시다.
  
## CSS 트랜지션과 애니메이션의 기초
> CSS를 이용한 애니메이션에는 CSS트랜지션과 CSS 키프레임 애니메이션의 두가지 범주가 있다.
* CSS 트랜지션은 시작 상태와 종료 상태의 두가지 고유한 상태 간에 값을 보간하는 애니메이션이다.
* CSS 키프레임 애니메이션은 시작과 종료 외에도 키프레임을 이용해 중간 단계를 제어하는 방법으로 더 복잡한 애니메이션을 만들 수 있게 해준다.   
  
### CSS 트랜지션 
> CSS 트랜지션은 두 CSS 속성 값 사이를 전환하는 방법으로 애니메이션(보간)을 적용하는 방법이다. 

트랜지션 속성
* 애니메이션을 적용할 요소 속성 이름(생략 시 전부 대상)
* 애니메이션 시간
* 가속 곡선을 제어할 타이밍 함수( ex)ease-in or ease-out )([easing의 기본](https://developers.google.com/web/fundamentals/design-and-ui/animations/the-basics-of-easing?hl=ko))
* 애니메이션을 시작하기 전 선택적 지연시간

[css transition예제](https://github.com/otwm/ProReactStudy/blob/master/chapter04/basicCssTransition/transition.html)  
  
cf ) [css트랜지션 사용하기](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions)  
  
### webkit 접두사 관련 사항  
책 참조
  
### css 키프레임 애니메이션  
> 트랜지션 기반 애니메이션에서는 애니메이션의 두 지점만 제어할 수 있으며, 모든 중간 단계는 브라우져가 보간을 통해 생성한다.
css 애니메이션을 만드는 다른 기법으로 keyframe 속성을 이용하는 키프레임 애니메이션이 있으며, 브라우저가 모든 것을 처리하는 트랜지션 기법에 비해 애니메이션 시퀀스의 중간 단계를 훨씬 세부적으로 제어할 수 있다.   
[keyframe](https://github.com/otwm/ProReactStudy/blob/master/chapter04/basicCssTransition/keyframe.html)  
   
cf )   
[Keyframe Animation Syntax](https://css-tricks.com/snippets/css/keyframe-animation-syntax/)    
[CSS Animation for Beginners](https://robots.thoughtbot.com/css-animation-for-beginners)  
[http://codepen.io/dwarcher/pen/yinsq](http://codepen.io/dwarcher/pen/yinsq)  
  
![keyframe](https://github.com/otwm/ProReactStudy/blob/master/chapter04/doc/keyframe.png)  
만능은 아니다.
## 다른 형태의 애니메이션 
### DOM 기반 웹 애니메이션 라이브러리
> 피쳐 측면에서 가장 완성도가 높은 것으로 알려져 있는 라이브러리로는 [Velocity](http://velocityjs.org/)와 [Greensock](http://velocityjs.org/)이 있고, 최근의 강자로는 [popmotion](https://popmotion.io/)이 있습니다. Velocity의 경우 React와 사용하려는 시도가 많이 있어, 트위터에서 개발한 [velocity-react](https://github.com/twitter-fabric/velocity-react) 같은 라이브러리를 사용하는 것도 하나의 방법입니다.

> 이러한 라이브러리들은 stagger나 decay 등의 일반적인 애니메이션 패턴들이 미리 만들어져 있어, 몇 개의 메서드만 사용해서 쉽게 애니메이션을 할 수 있다는 장점이 있습니다.  
  
  
### SVG
[Adding a third dimension to SVG pictures](http://debeissat.nicolas.free.fr/svg3d.php)  
[you](http://svg-wow.org/camera/camera.xhtml)    
   
### CANVAS  
[Tearable Cloth](http://codepen.io/dissimulate/pen/KrAwx)  

## 프로그래밍 방식으로 CSS 트랜지션과 애니메이션 시작 
[menu](https://github.com/otwm/ProReactStudy/blob/master/chapter04/basicCssTransition/menu.html)  
  
## ReactCSSTransitionGroup
> CSSTransitionGroup의 API는 굉장히 단순합니다. 마운트와 언마운트시 transitionName에 -enter -enter-active 등의 현재 상태를 표현하는 클래스를 교환해주는 것에 불과합니다. jQuery로 css 애니메이션을 할 때 'active', 'inactive' 등의 상태를 붙여서 애니메이션을 하는 것처럼 말이죠. css는 SCSS로 작성하면 훨씬 편리하게 작성할 수 있고, [믹스인](http://stackoverflow.com/questions/31553622/defining-react-csstransitiongroup-animations-with-stylus-mixin)을 사용하여 transitionName과 패러미터를 주는 식으로 줄여 쓸 수 있습니다.  
  
```javascript
npm install --save react-addons-transition-group
```

책 128p 참조  
[git](https://github.com/otwm/ProReactStudy/blob/master/chapter04/src/App.js)  리비젼 참조 8cc93231458638497e446398c4204105fe470e8c

## 다른 react 애니메이션 라이브러리
react-motion, Animated(네이티브 용) 
  
[webframework](http://webframeworks.kr/tutorials/react/react-animation/)   
  
## 드래그 앤 드롭(리액트 Dnd)
리액트 Dnd는 다른 drag and drop라이브러리와는 조금 다르다.  
[플럭스 아키텍쳐](http://facebook.github.io/flux/)와 닮아 있으며, 실제로도 내부적으로 사용하고 있다.  
  
### backand 
리액트 Dnd는 [html5 drag and drop api](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API)를 내장하고 있다. 그것은 드래그 될 때, 이전 드래그에 대한 스냅샷을 가지고 있으며, 커서가 움직일 때 마다 drawing 하지 않기 때문에 편리하다. 또한 파일 드랍 이벤트를 다룰 수 있는 유일한 방법이기도 하다.   
  
불행히도 HTML5 drag and drop API는 몇몇 불리한 점도 있는데, 터치 스크린에서 동작 하지 않으며, 타 브라우져 대비 IE상에서 커스터마이징 할 수 있는 여지가 적다. 
  
그래서, 리액트 Dnd에서는 이 부분을 플러그인 방식으로 지원하고 있으며, HTML5 drag and drop API를 꼭 쓰지 않아도 된다.리엑트 Dnd 상에는 여러 구현이 존재 하며, 더 추가 될 것이다.  
  
백엔드는 리엑트의 synthetic event system(브라우져간 차이를 추상화하고 네이티브 DOM 이벤트를 처리) 유사한 역활로 동작하지만, 그럼에도 불구하고 리엑트나 리액트의 synthetic event system과는 의존관계가 없다. 내부적으로 모든 백엔드는 DOM 이벤트를 내부 flux actions 으로전송한다.

### 아이템과 타입
리엑트 Dnd는 실제로 플럭스 처럼 뷰가 아닌 데이터를 사용하며, 스크린에서 어떠한 것을 드래그 했을 때, 그것을 컴포넌트나 DOM노드라고 하지 않고, 특정 타입의 아이템이라고 한다.(리엑트 Dnd 상에서)  
  
그래서 아이템이란 드래그되는 순수한 자바스크립트 객체의 묘사이다. 예를 들어 칸반 보드 상에서 카드를 하나 들었다고 하면 아이템은 { cardId:42 } 이런 식으로 표현 될 것이다.체스 게임이라면 말을 하나 집어들었다면, { fromCell: 'C5', piece: 'queen' } 일 것이다. 이와 같이 순수 객체로 묘사하는 것은 컴퍼넌트간의 디커플링이나 각각이 서로를 모르게 하는데 도움이 된다.  
  
타입은 어플리케이션 내에서 아이템의 전체 클래스를 유일하게 식별 할 수 있는 문자(또는 심볼)이다. 칸반이라고 하면 'card'같은 것이 긋것이다.(이후 예제 참조)  
  
타입은 복잡하고 또한 더 복잡해지는 앱 개발에서 적절한 drag/drop설정을 정확히 하는데 도움이 되며, drage source와 drop target을 하나의 타입에 동시에 설정도 가능 하다. 또한 이러한 타입을 constants로 가지게 될 것이다.  
  
### 


```javascript
npm install --save react-dnd
npm install --save react-dnd-html5-backend
```
드래그 앤 드롭을 구현하는 react-dnd-html5-backend 말고 다른 버전이 있다.

### 타입
### 사양 객체
### 콜렉팅 함수



[lodash의 throttle과 debounce](http://hyunseob.github.io/2016/04/24/throttle-and-debounce/)
[webframework](http://webframeworks.kr/tutorials/react/react-animation/)