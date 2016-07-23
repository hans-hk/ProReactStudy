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
#### primitives
기본형식에 대한 validation  
PropTypes.array, PropTypes.bool, PropTypes.func, PropTypes.number, PropTypes.object, PropTypes.string
  
#### combilned
PropTypes.oneOfType : Object가 가질수 있는 타입들 지정
```javascript
PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
]);
```
  
PropTypes.arrayOf : prop는 array 이고, 그 구성 타입을 지정    

```javascript
PropTypes.arrayOf(PropTypes.number);
```
PropTypes.objecOf : prop는 obejct 이고, 그 구성 타입을 지정
```javascript
PropTypes.objectOf(PropTypes.number);
```
  
PropTypes.shape : Object의 타입을 명확히 지정
```javascript
PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
})
```
#### specail
PropTypes.node : prop는 랜더될수 있는 특정 value (number, string, element, array)일 수 있다.  
PropTypes.element : prop는 React Element이다  
PropTypes.instanceOf : pop는 주어진 클래스의 인스턴스이다.  
PropTypes.oneOf : prop를 특정 값으로 제한한다  
```javascript
PropTypes.oneOf([ "News", "Photos" ])
```


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
* 순수컴포넌트 :  속성을 받고 이를 뷰에 렌더링만 한다. -> 상대적으로 재사용과 테스트가 용이하다.
* 상태저장컴포넌트 : 상태를 다루는 컴포넌트. 대부분 순수 컴포넌트가 다루기 용이 하지만, 상태에 대한 관리가 반드시 필요하게 된다.  

### 어떤 컴포넌트가 상태 저장이어야 할까?  
  
1. 해당하는 상태를 기준으로 렌더링하는 모든 컴포넌트를 찾는다.
2. 공통 소유자 컴포넌트를 찾는다.(계층에서 상태를 필요로 하는 모든 컴포넌트 상위에 있는 단일 컴포넌트)
3. 공통 소유자나 계층에서 더 상위에 있는 다른 컴포넌트가 상태를 소유해야 한다.
4. 해당 상태를 소유하기에 적절한 컴포넌트를 찾을 수 없느 경우 단순히 상태를 저장하기 위한 컴포넌트를 새로 만들고 계층에서 공통 소유자 컴포넌트를 위쪽에 추가한다.
  
### 데이터 흐름과 컴포넌트 통신
* 상위 컴포넌트에서 state를 관리. 하위 컴포넌트에는 prop로 전달. (상위에서 하위로)
* 상위 컴포넌트에서 event handler를 prop로 하위 컴포넌트에 전달.
  - event handler는 내부에서 this.setState를 사용
* 하위 컴포넌트에서 handler를 onchange와 같은 이벤트 핸들러로 활용. (하위에서 상위로)

## 컴포넌트 수명 주기
* 마운팅
* 언마운팅
* 속성 변경
* 상태 변경

[수명 주기](https://github.com/studye/react/wiki/React-Life-Cycle)  
  
### 수명주기 함수의 실제 활용 : 데이터 가져오기
컨택트 예제 
  
## 불변성에 대한 개요
**기본적인 원칙**   
* this.state를 직접 변경하지 말 것!!!
* 상태 변경은 setState 호출로 해야 한다.  
  
Why???   
* 리액트 패러다임과 맞지 않다.(기본적인 상태 변경 등의 방식이 무시되는 ...)
* 나중에 setState를 호출 시 변경이 무효화 되는 위험성.(데이터베이스의 분실갱신 같은 걸까요??) 
* 성능 개선문제 ( 객체 비교에 방식 관련. 책참조.)
  
### 일반 자바스크립트의 불변성
**객체를 변경해서는 안된다. 완전히 새로운 객체로 상태를 변경하여야 한다.**    
**비파괴 메서드를 사용하라.**    
  
```javascript
let updatedPassengers = this.state.passengers;
updatedPassengers.push("son");
this.setState({
    passengers: updatedPassengers
});
//(x)
let updatedPassengers = this.state.passengers.concat("son");
this.setState({
    passengers: updatedPassengers
});
//(0)
```
[concat](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)  
  
### 중첩된 객체  
Object.assign은 Deep copy 하지 않는다.
```javascript
   let a = { 
        p1:1,
        p2:{pp1:1,pp2:2}//새롭게 복제 하지 않고 참조 형태의 복사
   }
```
### 리엑트 불변성 도우미

[update](https://facebook.github.io/react/docs/update-ko-KR.html)  
[splice](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)  
[slice](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)  
   
### 배열 인덱스
## 칸반 앱: 약간의 복잡성 추가
### 외부 API에서 초기 카드 가져오기
### 태스크 콜백을 속성과 연결
### 태스크 조작
### 기본적인 낙관적 업데이트 롤백
## 정리

http://webframeworks.kr/tutorials/react/react-animation/
