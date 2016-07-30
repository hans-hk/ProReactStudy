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



[lodash의 throttle과 debounce](http://hyunseob.github.io/2016/04/24/throttle-and-debounce/)