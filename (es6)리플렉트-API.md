# 해야 할 것들
* Reflect API Overview
  
# 이건 뭐다???
자바스크립트 연산을 가로챌 수 있는 방법을 제공하는 내장 객체이다. 물론 ES5에서도 존재하지만(Object 메서드와 많이 중복된다.), 달라진 점은 ES5의 경우 실패 시 예외가 발생하는 반면에 ES6 Reflect 에서는 true/false의 불리언 값을 제공한다. 프록시 핸들러에서 비슷한 메서드가 존재하며, Reflect는 함수 객체가 아니고, 그래서 new 키워드는 당연히 사용할 수 없다.  
  
이번 장은 좀 지루 합니다 :)   
<img src='http://cfile22.uf.tistory.com/image/245CAA3C559989F122F9DE' width='400'/>

# Reflect API Matrix
| API | 뭐에 쓰는고?? | 대응되는 것들 |
| :--- | :--- | :--- |
| **Reflect.apply()**| 주어진 this값으로 타겟 함수 호출 | Function.prototype.apply() |
| Reflect.construct()| 함수를 생성. 다른 생성자의 프로토타입을 매치 할 수 있다. | |
| **Reflect.defineProperty()**| 객체에 새 프로퍼티 추가 또는 수정 |  Object.defineProperty() |
| Reflect.deleteProperty()| 프로퍼티 삭제 | delete |
| _Reflect.enumerate()_| 열거관련. 현재는 삭제되었다. |  |
| Reflect.get()| 객체 프로퍼티 조회 | |
| Reflect.getOwnPropertyDescriptor()| 프로퍼티 서술자(descriptor)를 조회한다. | Object.getOwnPropertyDescriptor()|
| Reflect.getPrototypeOf()| 프로타입 조회 | Object.getPrototypeOf() |
| Reflect.has()| 프로퍼티 존재 여부 확인 | in |
| Reflect.isExtensible()| 확장가능 객체인지 확인 | Object.isExtensible()|
| Reflect.ownKeys()| 프로퍼티 키를 원소로 담는 배열 반환(**상속이 아닌**) | |
| Reflect.preventExtensions()| 객체 확장을 제한 | Object.preventExtensions()|
| Reflect.set()| 프로퍼티 set | |
| Reflect.setPrototypeOf()| 프로타입 값을 지정 | |
  
_경험 상 실제로 많이 쓰이지는 않습니다만,(지극히 개인적인 견해?) 그래도 가끔씩 사용해 먹을 때가 있긴 합니다.(core 코드 작성할 때?) 가벼운 마음으로 보고 상큼하게 잊어 먹읍시다 :) (**정말로 지극히 개인적인 견해**)_

# API
## Reflect.apply
주어진 this값으로 타겟 함수 호출
  
### SynTax
> Reflect.apply(target, thisArgument, argumentsList)
### Parameters
target 
대상  
  
thisArgument  
제공될 this  
  
argumentsList  
아규먼트 배열  
  
### Exceptions
대상이 호출 불가능할 때

### Example
```javascript
function myFunc(a,b,c){
  return this.value + a + b + c;
}

var value = Reflect.apply( myFunc ,{value:100} ,[10,20,30]);
console.log(`Reflect.apply : ${value}`);

Reflect.apply(Math.floor, undefined, [1.75]); 
// 1;

Reflect.apply(String.fromCharCode, undefined, [104, 101, 108, 108, 111]);
// "hello"

Reflect.apply(RegExp.prototype.exec, /ab/, ["confabulation"]).index;
// 4

Reflect.apply("".charAt, "ponies", [3]);
// "i"
```

## Reflect.construct
함수를 생성. 다른 생성자의 프로토타입을 매치 할 수 있다.  
  
### SynTax
> Reflect.construct(target, argumentsList[, newTarget])
   
### Parameters
target 
대상  

argumentsList  
아규먼트 배열  
    
newTarget(optional)   
프로타입으로 사용될 생성자   
  
### Exceptions
대상이나 새로운 대상이 생성자가 아니면, 타입에러  
  
### Example
```javascript
function constructor(a, b){
  this.a = a;
  this.b = b;
  
  this.f = function(){
    return this.a + this.b + this.c;
  }
}

function constructor2(){}
constructor2.prototype.c = 100;

var obj = Reflect.construct(constructor, [1,2], constructor2);
console.log(`Reflect.construct : ${obj.f()}`)
 
var d = Reflect.construct(Date, [1776, 6, 4]);
d instanceof Date; // true
d.getFullYear(); // 1776

function someConstructor() {}
var result = Reflect.construct(Array, [], someConstructor);

Reflect.getPrototypeOf(result); // someConstructor.prototype
Array.isArray(result); // true
```

## Reflect.defineProperty
## Reflect.deleteProperty
## Reflect.enumerate(**현재는 스펙에서 삭제 되었습니다!**)
설명 패스!!
## Reflect.get
## Reflect.getOwnPropertyDescriptor
## Reflect.getPrototypeOf
## Reflect.has
## Reflect.isExtensible
## Reflect.ownKeys
## Reflect.preventExtensions
## Reflect.set
## Reflect.setPrototypeOf


# 참조
* [MDN reflect api](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
* [Reflect1](http://codepen.io/otwm/pen/YWgAYP)
* [Reflect2](http://codepen.io/otwm/pen/wWOPry?editors=0011)