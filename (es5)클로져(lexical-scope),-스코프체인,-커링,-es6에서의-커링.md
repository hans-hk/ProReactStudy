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
<img src='http://upload.wikimedia.org/wikipedia/commons/1/1e/Indiandishes.jpg' width=500/>   
  
커링은 무엇일까요?  
  
쉽게 말하면, 커리 함수의 인수의 부분적인 적용을 가능하게하는 기능을 구축하는 방법입니다. 이것이 의미하는 것은 함수가 기대되는 모든 인수를 전달하고 결과를 얻을 또는 이러한 인수의 부분 집합을 전달하고 나머지 인수를 기다리고 기능을 되 찾을 수있을 것입니다. 정말 간단합니다.  
  
커리는 기능적인 개념을 중심으로 구축되고있는 같은 하스켈과 스칼라 등의 언어 중의 원소입니다. JavaScript는 기능적인 능력을 가지고 있습니다 만, 커리는 (적어도없는 언어의 현재 버전은) 기본적으로 내장되어 있지 않습니다. 그러나 우리는 이미 몇 가지 기능의 트릭을 알고, 우리는 너무 JavaScript로 우리를 위해 커리의 작업을 할 수 있습니다.  
  
당신이이 일을 할 수있는 감각을주고, 우리가 원하는 커리 기능을 구축하는 데 익숙한 구문을 사용하여 JavaScript에서 우리의 첫 번째 커리 함수를 만들어 보자 . 예를 들어 이름으로 누군가에게 인사 기능을 상상해 봅시다. 우리 모두의 이름과 인사를 취하고 콘솔 이름으로 인사를 기록하고 간단한 인사 함수를 만드는 방법을 알고 있습니다   

```javascript
var greet = function(greeting, name) {
  console.log(greeting + ", " + name);
};
greet("Hello", "Heidi"); //"Hello, Heidi"
```
  
이것을 커링의 형태로 바꾼다면,...
  
```javascript
var greetCurried = function(greeting) {
  return function(name) {
    console.log(greeting + ", " + name);
  };
};

var greetHello = greetCurried("Hello");
greetHello("Heidi"); //"Hello, Heidi"
greetHello("Eddie"); //"Hello, Eddie"
```
  
```javascript
var greetDeeplyCurried = function(greeting) {
  return function(separator) {
    return function(emphasis) {
      return function(name) {
        console.log(greeting + separator + name + emphasis);
      };
    };
  };
};

var greetAwkwardly = greetDeeplyCurried("Hello")("...")("?");
greetAwkwardly("Heidi"); //"Hello...Heidi?"
greetAwkwardly("Eddie"); //"Hello...Eddie?"

var sayHello = greetDeeplyCurried("Hello")(", ");
sayHello(".")("Heidi"); //"Hello, Heidi."
sayHello(".")("Eddie"); //"Hello, Eddie."

var askHello = sayHello("?");
askHello("Heidi"); //"Hello, Heidi?"
askHello("Eddie"); //"Hello, Eddie?"
```

## 전통적인 형태의 커링

당신은 매우 자세한 사용자 정의 함수를 많이 만들 필요가있는 경우 특히이 방법이 얼마나 강력한 볼 수 있습니다. 유일한 문제는 구문입니다. 당신은 이러한 커리 함수를 작성하면 당신이 중첩 함수를 반환 유지하고 괄호의 여러 세트를 필요로하는 새로운 기능 자체의 분리 된 인수를 포함한 각에서 그들을 호출 할 필요가 있습니다. 이것은 성가신 얻을 수 있습니다.

그 문제를 해결하기 위해 다른 방법은 모든 중첩 된 반환하지 않고 작성된 기존 함수의 이름입니다 신속하고 더러운 커리 함수를 작성하는 것입니다. 커리 함수는 함수의 인수 목록을 이끌어 원래 함수의 카레 버전을 반환하기 위해 그들을 사용할 필요가있을 것입니다 :
  
```javascript
var curryIt = function(uncurried) {
  var parameters = Array.prototype.slice.call(arguments, 1);
  return function() {
    return uncurried.apply(this, parameters.concat(
      Array.prototype.slice.call(arguments, 0)
    ));
  };
};
```
이것을 사용하려면, 우리는 우리가 사전 이입을하고 싶은대로 인수의 많은과 함께 임의의 숫자 인수를 취하는 함수의 이름을 전달합니다. 우리가 돌아갈 때 나머지 인수를 기다리고 함수입니다.
  
```javascript
var greeter = function(greeting, separator, emphasis, name) {
  console.log(greeting + separator + name + emphasis);
};
var greetHello = curryIt(greeter, "Hello", ", ", ".");
greetHello("Heidi"); //"Hello, Heidi."
greetHello("Eddie"); //"Hello, Eddie."
```  
그리고 그냥 예전처럼 우리는 우리의 커리 본래의 기능에서 파생 함수를 작성할 때 사용하는 인수의 수의 점에서 제한되지 않습니다 :  
```javascript
var greetGoodbye = curryIt(greeter, "Goodbye", ", ");
greetGoodbye(".", "Joe"); //"Goodbye, Joe."
```  

결론은...   
커리는 기능적인 JavaScript에서 매우 유용한 기술입니다. 당신이 일관되게 행동 사용하기 위해 신속하고 작은 쉽게 설정할 수있는 기능 라이브러리를 생성 할 수 있으며, 당신의 코드를 읽을 때 이해 할 수 있습니다. 당신의 코딩 연습에 커리를 추가하면 잠재적 인 반복을 많이하지 않도록하고 당신의 코드 전체에서 부분적으로 적용되는 기능의 사용을 장려하고, 네이밍 및 함수의 인수를 취급에 관하여 좋은 습관에 당신을 얻을 것을 도울 수 있습니다.


# es6에서의 커링
[화살표 함수](https://github.com/otwm/ProReactStudy/wiki/%28es6-1%EC%9E%A5%29%EB%AC%B8%EB%B2%95-%EB%A7%9B%EB%B3%B4%EA%B8%B0#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98)를 통한 커링이 가능해졌다. 마치 하스켈 같은 느낌이랄까??  
  
```javascript
const curring = a => b => c => { return a + b + c };
console.log( curring(1)(2)(3) ) ;
```

[currying-in-es6](https://h3manth.com/new/blog/2015/currying-in-es6/)  


# 참조 사이트 
[mdn 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)  
[closure](http://speakingjs.com/es5/ch16.html)  
[orelly](http://archive.oreilly.com/oreillyschool/courses/advancedjavascript/Closures.html)  
[insanehong](http://insanehong.kr/post/javascript-scope/)  
[sitepoint](https://www.sitepoint.com/currying-in-functional-javascript/)  
[currying-in-es6](https://h3manth.com/new/blog/2015/currying-in-es6/)