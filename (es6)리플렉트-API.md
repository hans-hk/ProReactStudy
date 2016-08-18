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
  
_경험 상 실제로 많이 쓰이지는 않습니다만,(지극히 개인적인 견해?) 그래도 가끔씩 사용해 먹을 때가 있긴 합니다.(core 코드 작성할 때?) 가벼운 마음으로 보고 상큼하게 잊어 먹읍시다 :)_    
_(**정말로 지극히 개인적인 견해**)_   

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
  
### Return value
호출 결과  
  
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
  
_사실 생각보다는 자주 씁니다._

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
  
### Return value
결과  
  
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
_새로운 생성자 설정은 꽤 유용할지도?? 하지만, 무언가 많이 혼란스러울 수도 있겠네요._   
  
## Reflect.defineProperty
객체에 새 프로퍼티 추가 또는 수정  
반환 값이 실패 여부라는 데이서 Object.defineProperty과는 차이가 있다.Object.defineProperty는 실패하면 throw하며, 반환은 수정된 결과를 반환 한다. 이렇듯 ES5와는 이러한 미세한 차이를 가진다.  
   
> es5 부터 모든 객체의 프로퍼티는 데이터 프로퍼티, 접근자 프로퍼티 둘 중 하나다. 데이터 프로퍼티는 쓰기 가능 또는 불가 상태의 값을 가지는 반면, 접근자 프로퍼티는 프로퍼티 값을 조회.지정하는 함수의 게터-세터 쌍을 가진다. 이러한 데이터 프로퍼티 속성으로는 value, writable, enumerable, configurable이 있고, 접근자 프로퍼티 속성으로는 set, get, enumerable, configurable이 있다.  
  
#### 데이터 프로퍼티 서술자
* value : 프로퍼티 값. 기본은 undefined
* writable : 쓰기(할당)가능. 기본은 false
* enumerable : 열거(for...in 루프, Object.keys() 등에서) 가능. 기본은 false
* configurable : 속성 변경/삭제 가능. 기본은 false
  
#### 접근자 프로퍼티 서술자
* set : 프로퍼티 값을 지정하는 함수.
* get : 프로퍼티의 값을 반환하는 함수.
* enumerable : 열거(for...in 루프, Object.keys() 등에서) 가능. 기본은 false
* configurable : 속성 변경/삭제 가능. 기본은 false
   
> 자바스크립트 엔진은 서술자를 보고 데이터 프로퍼티인지, 접근자 프로퍼티인지 판단한다. Reflect.defineProperty, Object.defineProperty, Object.defineProperties, Object.create를 쓰지 않고 추가한 프로퍼티는 writable,enumerable,configurable 속성이 모두 true로 설정된다. 물론, 이후 변경이 가능하다.    
  
> Reflect.defineProperty, Object.defineProperty, Object.defineProperties 호출 시 이미 객체에 동일한 이름의 프로퍼티가 있다면 해당 프로퍼티를 덥어쓴다. 서술자에 따로 지정하지 않은 속성은 유지된다. 데이터/접근자 프로퍼티는 상호 변환이 가능한데, 변환하게 되면 서술자에 지정하지 않은 enumerable, configurable속성은 보존되지만 다른 송성은 기본 값으로 설정된다.  
   
### SynTax
> Reflect.defineProperty(target, propertyKey, attributes)  
    
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
    
descriptor  
프로퍼티 서술 객체   
  
### Return value
성공여부(true/false)  
  
### Exceptions
대상이나 객체가 아니면 타입에러  
  
### Example
```javascript
var myObj = {}
Reflect.defineProperty(obj, "name", {
  value: "do",
  writable: true,
  configurable: true,
  enumerable: true
});

console.log(`Reflect.defineProperty : ${obj.name}`);

var myObj2 = {
  __name__: "do"
}

Reflect.defineProperty(myObj2, "name", {
  get: function(){
    return this.__name__;
  },
  set: function(newName){
    this.__name__ = newName;
  },
  configurable: true,
  enumerable:true
})

myObj2.name = "kdo";
console.log(`Reflect.defineProperty : ${myObj2.name}`);

//체크
if (Reflect.defineProperty(target, property, attributes)) {
  // success
} else {
  // failure
}
```
  
_뭔가 있어보이지만, 실제로 apply가 더 많이 쓴다._  
  
## Reflect.deleteProperty
프로퍼티 삭제  
  
### SynTax
> Reflect.deleteProperty(target, propertyKey)   
   
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
    
### Return value    
성공여부(true/false)  
     
### Exceptions
대상이나 객체가 아니면, 타입에러  
  
### Example
```javascript
var myObj3 = {
  name: "kdo"
}

console.log(myObj3.name)
Reflect.deleteProperty(myObj3, "name");
console.log(`Reflect.deleteProperty : ${myObj3.name}`);

var obj = { x: 1, y: 2 };
Reflect.deleteProperty(obj, "x"); // true
obj; // { y: 2 }

var arr = [1, 2, 3, 4, 5];
Reflect.deleteProperty(arr, "3"); // true
arr; // [1, 2, 3, , 5]

// Returns true if no such property exists
Reflect.deleteProperty({}, "foo"); // true

// Returns false if a property is unconfigurable
Reflect.deleteProperty(Object.freeze({foo: 1}), "foo"); // false
```
[freeze](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)   
_차라리 delete가 낮지 않을까요?_  
  
## Reflect.enumerate(**현재는 스펙에서 삭제 되었습니다!**) 
설명 패스!!  
  
## Reflect.get   
객체 프로퍼티 조회  
  
### SynTax
> Reflect.get(target, propertyKey[, receiver])  
   
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
      
receiver(optional)   
getter가 호출될 때 제공되는 함수     
   
### Return value    
프로퍼티 값   
   
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
var obj = {
  __name__:"kdo"
}

Reflect.defineProperty(obj,"name",{
  get: function(){
    return this.__name__;
  }
});

console.log(`Reflect.get : ${obj.name}`);

var name = Reflect.get(obj, "name", {__name__:"do"});

console.log(`Reflect.get : ${name}`);

// Object
var obj = { x: 1, y: 2 };
Reflect.get(obj, "x"); // 1

// Array
Reflect.get(["zero", "one"], 1); // "one"

// Proxy with a get handler
var x = {p: 1};
var obj = new Proxy(x, {
  get(t, k, r) { return k + "bar"; }
});
Reflect.get(obj, "foo"); // "foobar"
  
```
  
## Reflect.getOwnPropertyDescriptor
프로퍼티 서술자(descriptor)를 조회한다.  
  
### SynTax
> Reflect.getOwnPropertyDescriptor(target, propertyKey)
   
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
       
### Exceptions
대상이나 새로운 대상이 생성자가 아니면, 타입에러  
  
### Example
```javascript
var obj = {
  name : "kdo"
}

var descriptor = Reflect.getOwnPropertyDescriptor( obj, "name");
console.log(descriptor);

Reflect.getOwnPropertyDescriptor([], "length");
// {value: 0, writable: true, enumerable: false, configurable: false}

Reflect.getOwnPropertyDescriptor("foo", 0);
// TypeError: "foo" is not non-null object

Object.getOwnPropertyDescriptor("foo", 0);
// { value: "f", writable: false, enumerable: true, configurable: false }

```
  
## Reflect.getPrototypeOf  
프로토타입 조회  
  
### SynTax
> Reflect.getPrototypeOf(target)  
     
### Parameters
target 
대상  

### Return value
주어진 객체의 프로토타입 없다면, null
  
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
var obj3 = {
  __proto__:{
    name: "do"
  }
}

var obj31 = Reflect.getPrototypeOf(obj3);
console.log(obj31);
console.log(obj31.name);

Reflect.getPrototypeOf({}); // Object.prototype
Reflect.getPrototypeOf(Object.prototype); // null
Reflect.getPrototypeOf(Object.create(null)); // null
```
  
## Reflect.has
프로퍼티 존재 여부 확인   
   
### SynTax
> Reflect.has(target, propertyKey)   
    
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
       
### Return value
존재여부(true/false)
  
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
var obj = {
  __proto__:{
    name: "do"
  },
  age: 12
}

console.log(Reflect.has(obj,'name'));
console.log(Reflect.has(obj,'age'));

Reflect.has({x: 0}, "x"); // true
Reflect.has({x: 0}, "y"); // false

// returns true for properties in the prototype chain 
Reflect.has({x: 0}, "toString");

// Proxy with .has() handler method
obj = new Proxy({}, {
  has(t, k) { return k.startsWith("door"); }
});
Reflect.has(obj, "doorbell"); // true
Reflect.has(obj, "dormitory"); // false

```
  
## Reflect.isExtensible
확장가능 객체인지 확인   
Object.preventExtensions(), Object.freeze(),Object.seal() 로 메서드를 확장 할 수 없게 할 수 있다.  
Object.isExtensible랑 같다.   
    
### SynTax
> Reflect.isExtensible(target)  
   
### Parameters
target 
대상  

### Return value
확장 가능 여부(true/false)    
  
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
// New objects are extensible. 
var empty = {};
Reflect.isExtensible(empty); // === true 

// ...but that can be changed. 
Reflect.preventExtensions(empty); 
Reflect.isExtensible(empty); // === false 

// Sealed objects are by definition non-extensible. 
var sealed = Object.seal({}); 
Reflect.isExtensible(sealed); // === false 

// Frozen objects are also by definition non-extensible. 
var frozen = Object.freeze({}); 
Reflect.isExtensible(frozen); // === false
```
  
## Reflect.ownKeys
프로퍼티 키를 원소로 담는 배열 반환(상속이 아닌)  
  
### SynTax
> Reflect.ownKeys(target)  
   
### Parameters
target 
대상  
  
### Return value
대상 객체의 프로퍼티 키 배열  
  
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
Reflect.ownKeys({z: 3, y: 2, x: 1}); // [ "z", "y", "x" ]
Reflect.ownKeys([]); // ["length"]

var sym = Symbol.for("comet");
var sym2 = Symbol.for("meteor");
var obj = {[sym]: 0, "str": 0, "773": 0, "0": 0,
           [sym2]: 0, "-1": 0, "8": 0, "second str": 0};
Reflect.ownKeys(obj);
// [ "0", "8", "773", "str", "-1", "second str", Symbol(comet), Symbol(meteor) ]
// Indexes in numeric order, 
// strings in insertion order, 
// symbols in insertion order
```

## Reflect.preventExtensions
객체 확장을 제한   

### SynTax
> Reflect.preventExtensions(target)  
   
### Parameters
target 
대상  
  
### Return value
결과(true/false)  
   
### Exceptions
대상이 객체가 아니면, 타입에러  
  
### Example
```javascript
// Objects are extensible by default.
var empty = {};
Reflect.isExtensible(empty); // === true

// ...but that can be changed.
Reflect.preventExtensions(empty);
Reflect.isExtensible(empty); // === false

Reflect.preventExtensions(1);
// TypeError: 1 is not an object

Object.preventExtensions(1);
// 1
```
  
## Reflect.set
프로퍼티 set   
   
### SynTax
> Reflect.set(target, propertyKey, value[, receiver])   
   
### Parameters
target 
대상  

propertyKey  
프로퍼티 명칭  
  
value  
셋팅 값  
       
receiver(optional)   
setter가 호출될 때 제공되는 함수      
   
### Return value
결과(true/false)
  
### Exceptions
대상이 객체가 아니면, 타입에러    
  
### Example
```javascript
var obj99 = {};
Reflect.set(obj99, "prop", "value"); // true
console.log(`obj99.prop : ${obj99.prop}`); // "value"

// Array
var arr = ["duck", "duck", "duck"];
Reflect.set(arr, 2, "goose"); // true
arr[2]; // "goose"

// It can truncate an array.
Reflect.set(arr, "length", 1); // true
arr; // ["duck"];

// With just one argument, propertyKey and value are "undefined".
var obj = {};
Reflect.set(obj); // true
Reflect.getOwnPropertyDescriptor(obj, "undefined");
// { value: undefined, writable: true, enumerable: true, configurable: true }
```
  
## Reflect.setPrototypeOf
프로토타입 값을 지정  
  
### SynTax
> Reflect.setPrototypeOf(target, prototype)
   
### Parameters
target 
대상  

prototype  
새로운 프로토타입    
    
### Return value   
결과(true/false)  
    
### Exceptions
대상과 프로토타입이 객체가 아니면, 타입에러  
  
### Example
```javascript
Reflect.setPrototypeOf({}, Object.prototype); // true

// It can change an object's [[Prototype]] to null.
Reflect.setPrototypeOf({}, null); // true

// Returns false if target is not extensible.
Reflect.setPrototypeOf(Object.freeze({}), null); // false

// Returns false if it cause a prototype chain cycle.
var target = {};
var proto = Object.create(target);
Reflect.setPrototypeOf(target, proto); // false
```

# 참조
* [MDN reflect api](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
* [Reflect1](http://codepen.io/otwm/pen/YWgAYP)
* [Reflect2](http://codepen.io/otwm/pen/wWOPry?editors=0011)