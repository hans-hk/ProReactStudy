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

**ES6 심볼이 등장한 가장 중요한 이유가 바로 객체 프로퍼티 키로 사용해서 얘기치 않게 프로퍼티 키와 충돌하는 일을 방지 하는 것이다.**
## Symbol.for(string)
Symbol 객체는 키/값 쌍의 레지스트리를 갖고 있다. Symbol.for()로 심볼을 생성할 때마다 레지스트리에 추가된고 이 메서드는 심볼을 반환한다. 이미 존재하는 서술로 심볼을 생성하면 기존 심볼을 그대로 반환한다. Symbol.for는 항상 전역 범위의 심볼을 생성한다.

```javascript
let obj = {};

(function(){
  let s1 = Symbol("name");
  obj[s1] = "수지";
})();

// 이 위치에서 obj[s1]은 안보인다.

(function(){
   let s2 = Symbol.for("age");
   obj[s2] = 27;
})();

console.log(obj[Symbol.for("age")]);// 27
```
  
## 이터레이션 규약
ECMAScript 2015 (ES6) 의 한 가지 추가 사항은 새로운 문법이나 built-in 이 아니라, protocol 입니다. 이 protocol 은 일정 규칙만 충족한다면 어떠한 객체에 의해서도 구현될 수 있습니다.  
(그렇다고 하는데 정작 이터레이션 이 외에 본 것은 없다는 건 함정!)  
    
ES6는 이터러블 규약(iterable protocol)과 이터레이터 규약(iterator protocol) 두가지로 나누어 규정한다.
### 이터레이터 규약  
next 메서드를 구현해야 한다.

| Property | Value|
|:---:|:---:|
|next	|<p style='text-align:left'>파라미터는 없고 두개의 프로퍼티를 가지는 오브젝트를 반환한다.<br/>done:이터레이터의 종료 여부(boolean)반환,<br/>value:실제 값</p> |
```javascript
let obj = {
   array:[1,2,3,4,5],
   nextIndex: 0,
   next : function(){
      if(this.nextIndex < this.array.length){
         return {
             value:this.array[this.nextIndex++], 
             done:false
         };
      }
      return {done:true}
   }
}

console.log(obj.next().value);
console.log(obj.next().value);
console.log(obj.next().value);
console.log(obj.next().value);
console.log(obj.next().value);
console.log(obj.next().done);
```
결과  
1  
2  
3  
4  
5  
true  

### 이터러블 규약 
이터러블은 @@iterator 메서드를 제공한다. 이 말은 Symbol.iterator 심볼을 프로퍼티 키로 갖고 있으며, 그 값으로 이터레이터 객체를 반환하는 펑션을 제공한다.  
```javascript
let obj = {
   array : [1,2,3,4,5],
   nextIndex: 0,
   [Symbol.iterator]: function(){
       return { 
          array:this.array,
          nextIndex: this.nextIndex,
          next:function(){
            if(this.nextIndex < this.array.length){
              return {
                value:this.array[this.nextIndex++], 
                done:false
              };
            }
            return {done:true}
          }
       }
   }
}

let iterable = obj[Symbol.iterator]();

console.log(iterable.next().value)
console.log(iterable.next().value)
console.log(iterable.next().value)
console.log(iterable.next().value)
console.log(iterable.next().value)
console.log(iterable.next().done)
```
결과  
1  
2  
3  
4  
5  
true  
  
## 제너레이터 
제너레이터는 평벙한 함수처럼 생겼지만, 하나의 값만 반환하는 게 아니라 한번에 하나씩 여러 값을 반환하는 함수다. 이 함수를 호출하면 즉시 바디를 실행하지 않고 제너레이터 객체(즉 , 이터러블 + 이터레이터 프로토콜을 모두 구현한 객체)의 새 인스턴스를 반환한다. 제너레이터 객체는 제너레이터 함수의 새로운 실행 콘텍스트를 갖고, next() 메소드를 실행하면 제너레이터 함수 바디를 죽 실행하다가 yield 키워드를 만나면 바로 중지하고 yield된 값을 반환한다.그리고 다시 next() 메서드를 부르면 멈춘 지점부터 실행이 재개되고 그 다음 yield 된 값을 낸다. 제너레이터 함수에 더 이상 yield 할 값이 남아있지 않을 때 done 프로퍼티는 true가 된다.  
제너레이터 펑션은 fucntion* 으로 표기한다.
```javascript
function* generator_func(){
   yield 1;
   yield 2;
   yield 3;
   yield 4;
   yield 5;
}

let gen = generator_func();
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().value);
```
<img src='http://s2.quickmeme.com/img/ab/abdd928ceb8c9e10268b31c3c26be47c2fe1edb8037feae792a1fcc40a05e8ca.jpg' width='500'/>   
조큼 더 활용하면,...
```javascript
function* generator_func(){
   var a = yield 12;
   var b = yield a + 1;
   var c = yield b + 2;
   yield c + 3;
}

var gen = generator_func();
console.log(gen.next().value);
console.log(gen.next(5).value);
console.log(gen.next(11).value);
console.log(gen.next(78).value);
console.log(gen.next().done);
```
결과는  
12  
6  
13  
81  
true  

## return(value) 메서드
```javascript
function* generator_func(){
    yield 1;
    yield 2;
    yield 3;
}

var gen = generator_func();
console.log(gen.next().value);
console.log(gen.return(22).value);
console.log(gen.next().done);
```
결과는 
1  
22  
true   
  
## throw(exception) 메서드 
```javascript
function* generator_func(){
  try{
     yield 1;
  }catch(e){
     console.log('error');
  }

  try{
     yield 2;
  }catch(e){
     console.log('error2');
  }
}

var gen = generator_func();

console.log(gen.next().value);
console.log(gen.throw("123").value);
console.log(gen.throw("1234").done);
```
결과는   
1
error
2
error2
true  
  
## yield* 
```javascript
function* generator_func(){
   yield 2;
   yield 3;
}

function* generator_func2(){
  yield 1;
  yield* generator_func();
  yield* [4,5];
}

var gen = generator_func2();
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().value);
console.log(gen.next().done);
```
결과는   
1   
2   
3   
4   
5   
true     
<img src='http://cfile7.uf.tistory.com/image/250B9B3C55769F3010710D' width='500'/>   
뭔가 이건... ;;; 새로운 우주가 보이는 군요...!    
   
   
## for ...of 루프
이것은 이터러블 순회하기 위한 루프   
```javascript
function* generator_func(){
  yield 1;
  yield 2;
  yield 3; 
}

let arr = [1,2];

for(let value of generator_func()){
   console.log(value);
}

for(let value of arr){
   console.log(value);
}
``` 
결과는   
1  
2   
3  
1  
2  

[iterator protocol](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration_protocols)   
[for ... of](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...of)   
    
## 꼬리 호출 최적화  
es6는 꼬리 호출에 대한 최적화를 합니다.(strict 모드에서)  
여하튼 꼬리 호출에서 새롭게 실행 스택을 만들지 않고 기존 스택을 재사용(?) 한다고 하여 좀 더 최적화가 가능 하다고 하네요.  
([tail call optimization in javascript](http://www.2ality.com/2015/06/tail-call-optimization.html))  
위 링크 문서에서는 일반적인 호출과 꼬리 호출에서 최적하는 부분을 설명하고 있습니다. 또한 어떤 것이 꼬리 호출인지에 대해서도 설명하고 있군요.
몇몇 부분만 참고 하자면, 최적화는 다음과 같은 코드가 있을 때,   
```javascript
function id(x) {
        return x; // (A)
    }
    function f(a) {
        let b = a + 1;
        return id(b); // (B)
    }
    console.log(f(2)); // (C)
```
Normal execution                          
<img src='http://4.bp.blogspot.com/-hnHFx8yImQw/VZJ5rW5XJyI/AAAAAAAABI8/fNjrEgBgWlQ/s1600/stack_frames_3.jpg' width='350'/>  
Tail call optimization    
<img src='http://4.bp.blogspot.com/-5A7wdXF9iiE/VZJ5q8ImSKI/AAAAAAAABI4/soyxT7WBQ-s/s1600/stack_frames_2_tco.jpg' width='350'/>   
이런 식을 불어나는 호출 스택을 축약 해버린다는 군요.   
  
또한 몇몇 햇깔릴 수 있는 꼬리 tail call에 대해서 이야기 하는데요. 인용하면
## 표현 상에서 꼬리 호출
> 애로우 펑션은 몸체로서 표현을 가질 수 있다. 꼬리 호출 최적화를 위해 우리는 그러므로 표현 내에서 함수 호출이 tail position에 있는 지를 이해해야만 한다. 오로지 다음 표현만이 꼬리 호출을 포함할 수 있다.  
다시 말해 아래의 표현으로 꼬리 호출을 포함한다는 이야기...
* 삼항 연산자 
* || 연산자
* && 연산자
* , 연산자
그러면,

```javascript
    const a = x => x ? f() : g();
```
여기서 f()과 g()는 tail position에 있는 거고,
```javascript
    const a = () => f() || g()
```
여기서 f()는 tail position이 아니고,g()는 tail position에 있는 거고, 그러한 이유는 
```javascript
    const a = () => {
        let fResult = f(); // not a tail call
        if (fResult) {
            return fResult;
        } else {
            return g(); // tail call
        }
    };
```
코드 컨버젼 했을 때 위와 같기 때문이고 

```javascript
    const a = () => f() && g();
```
여기서 f()는 tail position이 아니고,g()는 tail position에 있는 거고, 그러한 이유는 
```javascript
const a = () => {
        let fResult = f(); // not a tail call
        if (!fResult) {
            return fResult;
        } else {
            return g(); // tail call
        }
    };

```
컨버젼 시 위와 같기 때문,
```javascript
const a = () => (f() , g());
```
여기서 f()는 tail position이 아니고,g()는 tail position에 있는 거고, 그러한 이유는 
```javascript
    const a = () => {
        f();
        return g();
    }
```
이러네요.

기타 여러가지 케이스에 대해 설명이 나오네요. 문서 코드를 한번 읽어 보시기 바래요.
([tail call optimization in javascript](http://www.2ality.com/2015/06/tail-call-optimization.html))  

cf)   
[es6 이터레이터 스펙](http://www.ecma-international.org/ecma-262/6.0/#sec-iteration)은 이렇습니다만, 사람이 볼 문서는 아니군요...;;
