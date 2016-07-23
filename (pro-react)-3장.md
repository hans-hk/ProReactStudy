## 해야 할 것들 
* PropType,기본값 설정에 대해서 이해 하고 커스텀 propType을 작성한다.(kanban**(coding)**)
* 컴퍼넌트 조합 전략(예제 보기)
* 컴퍼넌트 수명주기 
* 불변성에 대한 개요, 중첩된 객체(deep copy), 불변성 도우미(react-addons-update)
* 칸반(proreact api 이용한 개발 및 태스크 관련 메서드 추가, 낙관전 업데이트 롤백;**(coding)**)
   
## 속성 유효성 검사
많은 개발자들이 동시에 큰 프로젝트 참여시에는 각각의 컴퍼넌트의 프로퍼티에 대한 명세들이 필요하다.
그래서 리엑트는 PropTypes라는 걸 제공하는데, 이것의 장점은 
1. 언제든지 컴포넌트를 열고 어떤 속성이 필요한지, 그리고 속성의 형식이 무엇인지 알 수 있다.
2. 문제가 발생한 경우 리액트가 콘솔에 오류 메세지를 출력해 어떤 속성이 잘못되었거나 누락되었는 지, 그리고 문제가 발생한 render 메서드가 무엇인지 알려줄 수 있다.(**프로그램 자체에는 영향을 미치지지는 않는다.**)

```javascript
import React, {Component, PropTypes} from 'react';
import {render} from 'react-dom';

class Greeter extends Component {
    render() {
        return (
            <h1>{this.props.salutation}</h1>
        )
    }
}

Greeter.propTypes = {
    salutation: PropTypes.string.isRequired
};

// render(<Greeter salutation="Hello World"/>, document.getElementById('root'));
render(<Greeter/>, document.getElementById('root'));
```
### 속성 기본값
```javascript
import React, {Component, PropTypes} from 'react';
import {render} from 'react-dom';

class Greeter extends Component {
    render() {
        return (
            <h1>{this.props.salutation}</h1>
        )
    }
}

Greeter.propTypes = {
    salutation: PropTypes.string
};

Greeter.defaultProps = {
    salutation: "Hello World"
};

// render(<Greeter salutation="Hello World"/>, document.getElementById('root'));
render(<Greeter/>, document.getElementById('root'));
```
### 기본 제공되는 propTypes 유효성 검사기
책 참조  
[(공식문서)재사용 가능한 컴포넌트](https://facebook.github.io/react/docs/reusable-components-ko-KR.html)  
  
### 칸반 앱: 속성 형식 정의  
실습  
  
### 커스텀 propTypes 유효성 검사기
[(공식문서)재사용 가능한 컴포넌트](https://facebook.github.io/react/docs/reusable-components-ko-KR.html)  
  
#### 칸반 앱 : 커스텀 propTypes 유효성 검사기 정의
실습  
[es6  문자열 보간](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals)  
  
## 컴퍼넌트 조합 전략과 모범 사례
### 상태 저장 컴포넌트와 순수 컴포넌트
### 어떤 컴포넌트가 상태 저장이어야 할까?
### 데이터 흐름과 컴포넌트 통신
## 컴포넌트 수명 주기
### 수명주기 단계와 메서드 
### 수명주기 함수의 실제 활용 : 데이터 가져오기
## 불변성에 대한 개요
### 일반 자바스크립트의 불변성
### 중첩된 객체
### 리엑트 불변성 도우미
### 배열 인덱스
## 칸반 앱: 약간의 복잡성 추가
### 외부 API에서 초기 카드 가져오기
### 태스크 콜백을 속성과 연결
### 태스크 조작
### 기본적인 낙관적 업데이트 롤백
## 정리
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/slice

https://facebook.github.io/react/docs/update-ko-KR.html

http://webframeworks.kr/tutorials/react/react-animation/
