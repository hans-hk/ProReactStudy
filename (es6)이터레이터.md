다루어질 이야기들
* 심볼을 객체 프로퍼티 키로 사용
* 객체에 이터레이션 규약을 구현
* 제네레이터 객체의 생성과 활용
* for...of 루프로 순회
* 꼬리 호출 최적화

# ES6 심볼
심볼은 ES6에서 처음 선보인 완전히 새로운 원시 타입! 유일하며, 변경할 수 없다.
```javascript
var s = Symbol();
```
심볼은 리터럴 형식이 없고, 오직 Symbol() 함수로만 생성된다. 이 함수는 호출할 때 마다 유일한 심볼을 반환한다.
```
Symbol([description])
```

description Optional  
Optional, 문자열(string). 디버깅을 위해 사용할 수 있는 심볼의 description. 심볼에 접근하는 용도로는 사용할 수 없음.

cf)  
기본형 변수에 대해 명시적 래퍼 객체를 만드는 것은 ECMAScript 6 부터 더 이상 지원되지 않는다. 하지만, new Boolean, new String 그리고 new Number 와 같이 이미 존재하고 있는 기본형 래퍼 객체는 래거시(lagacy) 요인으로 인해 아직 생성가능하다.
  
심볼 설명 부분 참조  
[심볼](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

## 심볼을 프로퍼티로 사용
```javascript
let obj = null;
let s1 = null;

(function(){
    let s2 = Symbol();
    s1 = s2;
    obj = {[s2]:'s1'}
    console.log(obj[s2]);
    console.log(obj['s2'] == obj[s1]);
})();

console.log(obj[s1]);
```
결과는 
```
s1
true
s1
```

** ES6 심볼이 등장한 가장 중요한 이유가 바로 객체 프로퍼티 키로 사용해서 얘기치 안ㅇㅎ게 프로퍼티 키와 충돌하는 일을 방지 하는 것이다.**
## Symbol.for(string)
