# 자바스크립트에서의 스코프
자바스크립트는 다른 언어와 달리 조금 특별한 scope 체계를 가지고 있습니다.그에 대해 알아봅시다.
 
## 지역변수와 전역변수 
* 지역변수 : 함수 내에서 선언된 변수를 지역 변수라고 합니다. 해당 함수 블럭을 벗어나게 되면 당연히 메모리에서 사라집니다.   
* 전역변수 : global 영역에 선언된 전역변수는 자바스크립트 코드 어디에서든 사용할 수 있습니다.   
  
이렇듯 **자바스크립트는 기본적으로 함수 단위의 범위를 가집니다.** 이런 부분 타 언어와는 다른 비교되는 부분이며, es6에서는 이런 점을 보완하기 위해 [let](https://github.com/otwm/ProReactStudy/wiki/%28es6-1%EC%9E%A5%29%EB%AC%B8%EB%B2%95-%EB%A7%9B%EB%B3%B4%EA%B8%B0#let)이라는 키워드가 나왔습니다.  
  
지역변수
```javascript
function foo() {
   var x= 1; // 함수안에서 선언된 변수임에 지역변수이다. 
   return x;
}
console.log(foo());
> 1
console.log(x); // 외부에서 참조할수 없다.
>undefined
```
  
전역변수  
```javascript
var x= 1; // global 영역에 선언된 전역변수

function foo() {
   return x; // 전역변수 x를 return
}
console.log(foo());
> 1
console.log(x); // 어디서든 참조가능한 전역변수
> 1
```  
  
```javascript
var x='hello'; // 전역변수 x 에 hello 할당.

function foo() {
   var x='world';  // 지역변수 x 에 world할당.
   return x;  
}

console.log(foo());
>world // 함수실행에 따라 지역변수 x에 할당된 world 가 출력된다.
console.log(x);
>hello // 전역변수 x에 할당된 hello 가 출력된다.
```  
위의 코드가 했깔리시나요?? 이 정도에서 무너지면 안됩니다.ㅜㅜ  
  
**자바스크립트에서 스코프는 Scope 체인으로 설명이 가능합니다.**(스코프 체인에 대해서는 조금 있다가 보도록 하지요. :) )
  
# 클로져(lexical scope)
> 클로저는 독립적인 (자유) 변수를 가리키는 함수이다. 또는, 클로저 안에 정의된 함수는 만들어진 환경을 '기억한다'.
  
이 코드를 보시지요. 이런한 코드가 C나 Java 같은 언어에서 가능할까요?
```javascript
function makeFunc() {
  var name = "모질라";
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```  
일반적으로 함수안에 정의된 지역변수는 함수가 실행하는 동안에만 존재한다. makeFunc() 함수가 종료될 때 이 함수 내부에 정의된 지역변수는 없어지는게 상식적이다. 어째서 이런 일이... 당연히 이런 코드는 일반적인 C나 Java 같은 언어에서는 에러가 발생합니다. Syntax가 맞지도 않을 뿐더러 이미 함수를 빠져나오는 상황에서 변수 name이 메모리 상에 존재할리도 없지요. 그런데 어째서 이런일이???   
  
일단은 이것이 흔히 이야기 하는 클로저라고 아시면 되겠습니다. 어휘적 스크프라고 하기도 하는데, 클로저는 두 개의 것(함수, 그 함수가 만들어진 환경)으로 이루어진 특별한 객체의 한 종류이다. 환경이라 함은 클로저가 생성될 때 그 범위 안에 있던 여러 지역 변수들로 이루어진다. 이 경우에 myFunc는 displayName 함수와 "모질라" 문자열을 포함하는 클로저라고 합니다.  
   
# 스코프체인
<img src='http://cfile217.uf.daum.net/image/1651CC344E1FCB883FCFFA' width=400/>  
도대체 자바스크립트는 왜 이렇게 체인을 조아하나요? ㅎ  
  
## Scope & Scope Chain
> ECMA-262 3 Edition 에서는 함수객체는 [[[scope]]]] 라는 프로퍼티를 가지며 [[[scope]]]]는 함수객체가 생성되는 시점과 관련된 Object Reference 정보를 가지고 있으며 이 Object Reference 정보들을 Scope Chain 이라고 한다고 정의한다. 즉 scope는 함수객체가 접근가능한 Valiable Object의 유효범위이며 이 Valiable Object들의 집함을 Scope Chain 이라고 한다.

# 커링
# es6에서의 커링
# 참조 사이트 
[mdn 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)