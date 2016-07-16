#Dom 추상화의 내부

##리액트의 이벤트
실제로 하나의 이벤트가 dom에 연결 되는 것이며, 핸들러는 매핑 되는 듯 하다.  

실제로 살펴본 결과 [공식문서](https://facebook.github.io/react/docs/events-ko-KR.html)에서 설명하는 것처럼 Synthetic 객체가 생성되고, 이는 리엑트 내의 dispatchEvent 메서드를 통해서,PooledClass를 통해 풀링되어 결국에 SyntheticEvent로 들어오는 것 까지는 확인이 되었다.(여기까지만 하자. 더하면 다칠듯...;;;)  
<img src="https://github.com/otwm/ProReactStudy/blob/master/chapter02/file/react_event_dispatch.png" width="200"/>
<img src="https://github.com/otwm/ProReactStudy/blob/master/chapter02/file/react_event_pooled.png" width="200"/>
<img src="https://github.com/otwm/ProReactStudy/blob/master/chapter02/file/react_event_syntheticEvent.png" width="200"/>

cf)  
[(공식문서)이벤트 시스템](https://facebook.github.io/react/docs/events-ko-KR.html)  
[MDN 메모리 누수](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_Management)  
[넥스트리 메모리 누수](http://www.nextree.co.kr/p7363/)  

기반지식)  
[이벤트 캡쳐, 버블링(코드 포함)](http://www.java2s.com/Book/JavaScript/DOM/Event_Flow_capture_target_and_bubbling.htm)  
[이벤트 캡쳐, 버블링(한글)](http://aroundck.tistory.com/1807)  
[w3c event 정의](https://www.w3.org/TR/DOM-Level-3-Events/)  
  
정말 안봐도 되는 것들)(이런 건 왜 여기다...)  
[proxy](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy)  
  
JSX는 실제 html의 이벤트 API는 이용하지 않는다. 또한 반드시 **낙타** 표기법을 이용하니 알아두자.  

##JSX 자세히 살펴보기
JSX는 자바스크립트 코드 안에 선언적인 XML스타일의 구문을 작성할 수 있게 해주는 리액트의 선택적 자바스크립트 구분확장이다.
html과 유사하지만, 다른 XML태그 집합을 사용하는 것도 있다.(네이티브 등)
트랜스파일을 거치면서 script 코드로 변환된다.

* 트리형 요소를 쉽게 표현.(<== 이것이 존재 이유인듯.)
* 태그특성은 모두 낙타 표기법.
* 기타 xhtml 문법에 맞게(?)(열고 닫기 등)
* 모든 사향은 Dom api 기반(class(x), className(0)) (!!JSX는 html이 아닌 자바스크립트의 구문 확장이다.)

### 단일 루트 노드
ex)
```jsx
return (
    <h1>Hello1</h1>
    <h1>Hello2</h1>
)
// X

return (
    <div>
        <h1>Hello1</h1>
        <h1>Hello2</h1>
    </div>
)
//O
```
### 조건절
ex)
```jsx
return (
    <h1 className={ if(condition){"abc"} }>Hello1</h1>
)
// X

return (
    <h1 className={ condition?"abc":"" }>Hello1</h1>
)
//O

return (
    { condition?
        <h1 className="abc">Hello1</h1>
        : null
    }
)
//O

render(){
    let className;
    if(condition){
        className = "abc";
    }
    return (
        <div className={className}>Hello1</div>
    )
}
//O
```
(내 생각엔) 마지막 것이 제일 나은 듯.

###공백
{"   "}
### 주석
<!-- comment -->(x)
/** comment */(x)

### 동적 HTML 렌더링

**리액트는 [XSS](http://www.kisa.or.kr/uploadfile/201312/201312161355109566.pdf) 공격 방지 기능이 기본적으로 내장되어 있다.**  
<img src='https://pbs.twimg.com/media/CYfJeWsUEAAeNz8.jpg' width='250'/>  
==>> 그래서 노드 동적생성하는 것은 그다지 추천하지 않는다.  
==>> xss 보호기능을 끄려면 dangerouslySetInnerHTML을 이용한다.  
  
###JSX를 배제하고 리액트 이용
[JSX 배제](https://facebook.github.io/react/docs/glossary-ko-KR.html)  
그래도 된다.(왜?? 뭣하러??)

```javascript
R.p({},
  'The live example is the same. The only difference is that we render to ',
  R.code({},
    'mountNode'
  ),
  ', ' +
  'which is just the DOM node for the example.'
)
```
왜 JSX를 써야하는 가를 보여주는 코드??


### 요소 팩토리
```javascript
//간단한 커멘트 폼
React.DOM.form({className:"commentForm"},
   React.DOM.input({type:"text",placeholder:"Name"}),
   React.DOM.input({type:"text",placeholder:"Comment"}),
   React.DOM.input({type:"submit",value:"Post"})
)

//JSX
<form className='commentForm'>
    <input type='text' placeholder='Name'/>
    <input type='text' placeholder='Comment'/>
    <input type='submit' value='Post'/>
</form>

//좀 더...
import React, {Component} from 'react';
import {render} from 'react-dom';

let {
   form,
   input
} = React.DOM;

class CommentForm extends Component {
   render(){
       return form({className:"commentForm"},
            input({type:'text',placeholder:'Name'}),
            input({type:'text',placeholder:'Comment'}),
            input({type:'submit',placeholder:'Post'})
       )
   }
}

/**
 * 딱히 어떤 스타일이 더 나은지에 대해서는 생각해 볼 필요가 있지만,
 * 현재로서, 일반적인 경우는 기본적인 JSX 만으로 충분해 보인다.(지극히 사견)
 */
```

### 커스텀 팩토리 
```javascript
let factory = React.createFactory(ComponentClass);
let root = factory({custom:'prop'});
render(root,document.getElementById('test'));
```
  
### 인라인 스타일링
예제
참조)
[리액트를 이해하다 7편](http://blog.coderifleman.com/post/123886912084/reactjs%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8B%A47)

### 폼 처리
#### 제어 컴포넌트(Controlled Component)
Controlled Component는 State에 따라 값을 관리하는 Componenet 입니다. 이를 이용해 텍스트 필드를 재작성합니다.

예제
##### 특수사례
* TextArea : 일관성을 위해 value 속성을 사용.(html과 다름)
* Select : 역시 value 속성 사용 가능하다.

#### 비제어 컴포넌트(UnControlled Component)
UnControlled Componenent는 반대로 값을 관리하지 않는 컴포넌트로 초기값을 설정한 값은 defaultValue로 지정합니다. 이 경우는 앞 절에서처럼 onChange()에서 항상 값을 state에 반영해도 되고, 반영하고 싶을 때만 DOM에서 value를 취득하여 갱신하는 것도 가능합니다.

예제

````javascript
var LiveText = React.createClass({
  getInitialState() {
    return {
      textValue: "initial value"
    };
  },
  changeText(e) {
    this.setState({textValue: this.refs.inputText.getDOMNode().value });
  },
  render() {
    return (
      <div>
        <p>{this.state.textValue}</p>
        <input type="text" ref="inputText" defalutValue="initial value" />
        <button onClick={this.changeText}>change</button>
      </div>
    );
  }
});
````

### 가상 DOM의 작동 방식
**우리가 이해해야 하는 것들**   
* DOM 트리의 노드를 비교할 때 노드가 다른 유형일 경우(예:div를 span으로 변경) 리엑트는 이를 서로 다른 하위 트리로 취급해 첫 번째 항목을 버리고 두번째 항목을 생성/삽입한다.
* 커스텀 컴포넌트에도 동일한 논리를 적용한다. 컴포넌트가 동일한 유형이 아닌 경우 리액트는 컴포넌트가 렌더링하는 내용을 비교조차 하지 않고 DOM에서 첫번째 항목을 제거한 후 두번째 항목을 삽입한다.
* 노드가 같은 유형인 경우 리액트는 둘 중 한 가지 방법으로 이를 처리한다.
 - DOM요소의 경우(예: <div id='before'/>를 <div id='after'/>로 변경) 리액트는 특성과 스타일만 변경한다.(요소트리는 대체하지 않음)
 - 커스텀 컴포넌트의 경우(예: <Contact details={false} />를 <Contact details={true}/>로 변경) 리엑트는 컴퍼넌트를 대체하지 않고 새로운 속성을 현재 마운팅된 컴포넌트로 전달한다. 그러면 이 컴포넌트에서 새로 render()가 트리거 되고 새로운 결과를 이용한 프로세스가 다시 시작된다.   
   
> 애플리케이션 개발자가 VIRTUAL DOM을 직접 신경 쓰는 경우는 key 속성 지정과 성능 향상의 목적으로 shouldComponentUpdate()를 구현할 때입니다.  
    
참조)   
* [react 공식 사이트 virtual dom](https://facebook.github.io/react/docs/glossary-ko-KR.html)
* [virtual dom과 dom의 차이](http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/)
* [state of virtual dom](https://blog.jscrambler.com/state-virtual-dom/)
* [Learning Virtual DOM and React Diff Algorithm](http://www.oyecode.com/2015/09/reactjs-learning-virtual-dom-and-react.html)
* [virtual dom의 좋은 점](http://blog.coderifleman.com/post/122771353444/reactjs%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8B%A44)
* [Matt-Esch/virtual-dom](https://github.com/Matt-Esch/virtual-dom)  
  
### 키
virtual dom에서 반복되는 요소들을 다룰 시 성능을 높이기 위해 key 속성을 이용하는 것이 좋다.  
책을 참조할 것!  
   
### ref
컴퍼넌트 내부에 하위 노드나 컴퍼넌트에 접근하고 싶을 때 사용할 수 있다.
그러나 대부분의 경우 실제 DOM을 조작하는 것보다 리액트 모델 안에서 더 깔끔하게 코드를 구성할
수 있는 방법이 있따!!!

예제
##칸반 앱: 카드가 열려있는지 여부 확인
실습  
[색상 추가](https://github.com/otwm/ProReactStudy/commit/95f1d23f4ba9e5a3c9db921d6230dff35781c7ce)  
[toggle method extract!](https://github.com/otwm/ProReactStudy/commit/03710fa8b58306c72a008677dcce6472f0cd3d1d)  
  
##정리



## 기타
**[zapier react tutorial](https://zapier.com/engineering/react-js-tutorial-guide-gotchas/)**
[js oop](https://github.com/dotNetTree/I-Konow-JS/blob/master/oop-in-js/01_java_to_js.md)
[virtual dom에 대한 이해](https://gist.github.com/sebmarkbage/fcb1b6ab493b0c77d589#file-react-terminology-md)

## 남은 과제들
* [es6, react.Component]구체적으로 생성자 안에 super 하는 일은 무엇인가?

## 설명
* 칸반 소스를 가지고 2장 설명
* 공식 문서 보기