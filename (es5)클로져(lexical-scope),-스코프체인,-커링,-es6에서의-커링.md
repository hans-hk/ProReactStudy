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
> ECMA-262 3 Edition 에서는 함수객체는 \[\[scope\]\]라는 프로퍼티를 가지며 \[\[scope\]\]는 함수객체가 생성되는 시점과 관련된 Object Reference 정보를 가지고 있으며 이 Object Reference 정보들을 Scope Chain 이라고 한다고 정의한다. 즉 scope는 함수객체가 접근가능한 Valiable Object의 유효범위이며 이 Valiable Object들의 집함을 Scope Chain 이라고 한다.

> 함수객체는 생성과정에서 함수구문 내에서 선언된 지역변수들을 Valiable Object 라는 객체에 저장한다. 그리고 전역객체(Global Object)의 Valiable Object 와 연결된다. 전역객체의 Valiable Object는 전역객체 자신이다. 이때 이 Valiable Object들에 대한 연결들을 Scope Chain 으로 관리하고 [[scope]]를 통해 Scope Chain을 참조하여 함수객체가 가지는 유효범위를 설정하게 되는 것이다.
  
```javascript
var x = 1;
function foo() {
    var y = 10;
    return a+x;
}
console.log(z);
>ReferenceError;
```

![scope chain](http://insanehong.kr/post/javascript-scope/@img/diagram1.jpeg)

### lexical과 dynamic
#### Dynamic dimension
매 함수 호출 시 새로운 스코프가 생성된다. 스코프 체인에는 스코프 쌓이지 않고 계속 교체된다.
```javascript
function g() {
}
function f() {
    g();
}
```
#### Lexical dimension
```javascript
function myFunction(myParam) {
    var myVar = 123;
    return myFloat;
}
var myFloat = 1.3;
// Step 1
myFunction('abc');  // Step 2
```

<img src='http://speakingjs.com/es5/images/spjs_2001.png' width=500/>

### example
closure1)  
```javascript
    function makeAdder(x, y) {
        var adder = function() {
            return x + y;
        };

        return adder;
    }

    var f = makeAdder(2, 3);
    var result = f();
    console.log("Result is: " + result);     
```
  
<img  src='https://github.com/otwm/ProReactStudy/blob/master/submission/img/closure1.png?raw=true' 
   width='500' />

dynamic)
```javascript
function dynamic1(){
    var a = 1;
    console.log('in 1 a : ' + a);
    dynamic2();
}

function dynamic2(){
    var a = 2;
    console.log('in 2 a : ' + a);
}

function dynamic3(){
    var a = 3;
    console.log('in 3 a : ' + a);
    dynamic1();
}

dynamic3();
````

closure2)  
```javascript
function test(param1){
    var test = 1;
    var cal1 = param1 + test;
    return function(param2){
        var test = 2;
        var cal2 = cal1 + param2 + test;
        return function(param3){
            var test = 3;
            var cal3 = cal2 + param3 + test;
            return cal3;
        }
    }
}

var returnedTest = test(10);
var temp1 = returnedTest(20);
var temp2 = temp1(30);

console.log(temp2);

````
<img  src='https://github.com/otwm/ProReactStudy/blob/master/submission/img/closure2.png?raw=true' 
   width='500' />

그래서,  
* 클로저는 해당 스코프가 계속 쌓인다.
* 클로저의 스코프는 함수에 저장된다.
* dynamic의 경우에는 스코프가 쌓이는 것이 아니라, 지속적으로 교체된다.
* 변수는 스코프 체인 내에서 로컬 부터 시작해서 글로벌 영역으로 찾게 된다.(체인)

closure3)
```javascript
    function makeAdder(x, y) {
        var adder = function() {
            return x + y;
        };
        x = 10;
        return adder;
    }

    var f = makeAdder(2, 3);
    var result = f();
    console.log("Result is: " + result);//x의 값은 2인가? 10인가?
    
    var g = makeAdder(4, 5);
    var anotherResult = g();
    console.log("Another result is: " + anotherResult);//x의 값은 3인가 10인가??
```

<img src='http://archive.oreilly.com/oreillyschool/courses/advancedjavascript/images/ClosureDiagram3.jpg' width=600 />  
  
closure4)  
```javascript
    function makeCounter() {
        var count = 0;
        return function() {
            count = count + 1;
            return count;
        };
    }

    var count = makeCounter();
    console.log("Counter: " + count());
    console.log("Counter: " + count());
    console.log("Counter: " + count());
    console.log("Counter: " + count());
    console.log("Counter: " + count());
```

**클로져와 루프**   
closureExample.html    
```html
<!doctype html>
<html>
<head>
    <title> Closures for divs </title>
    <meta charset="utf-8">
    <style>
        div {
            position: relative;
            margin: 10px;
            background-color: red;
            border: 1px solid black;
            width: 100px;
            height: 100px;
        }
    </style>
    <script>
        window.onload = function() {
            var numDivs = 3;
            for (var i = 0; i < numDivs; i++) {
                var div = document.getElementById("div" + i);
                div.onclick = function() {
                    console.log("You just clicked on div number " + i);
                };
            }
        };
    </script>
</head>
<body>
<div id="div0"></div>
<div id="div1"></div>
<div id="div2"></div>
</body>
</html>
```
실제로 div를 클릭하게 되면 모두 3이 나온다???   
<img src='https://pbs.twimg.com/media/Ch5YCHpUUAEIJLo.jpg' />  
    
진짜로 우리가 원하는 건 이것!  
closureExample2.html  
```html
<!doctype html>
<html>
<head>
    <title> Closures for divs </title>
    <meta charset="utf-8">
    <style>
        div {
            position: relative;
            margin: 10px;
            background-color: red;
            border: 1px solid black;
            width: 100px;
            height: 100px;
        }
    </style>
    <script>
        window.onload = function() {
            var numDivs = 3;
            for (var i = 0; i < numDivs; i++) {
                var div = document.getElementById("div" + i);
                div.onclick = (function(divNum) {
                    return function() {
                        console.log("You just clicked on div number " + divNum);
                    };
                })(i);
            }
        };
    </script>
</head>
<body>
<div id="div0"></div>
<div id="div1"></div>
<div id="div2"></div>
</body>
</html>
```
클로져는 클로져로 해결..   
  
기타 다른 예제도 보면 괜찮을 듯 하네요. [orelly](http://archive.oreilly.com/oreillyschool/courses/advancedjavascript/Closures.html)    


# 커링
# es6에서의 커링
# 참조 사이트 
[mdn 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)  
[closure](http://speakingjs.com/es5/ch16.html)  
[orelly](http://archive.oreilly.com/oreillyschool/courses/advancedjavascript/Closures.html)  
[insanehong](http://insanehong.kr/post/javascript-scope/)  