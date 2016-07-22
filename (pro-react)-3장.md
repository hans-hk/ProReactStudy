## 속성 유효성 검사
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
