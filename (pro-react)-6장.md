# 해야 할 것들 
* 플럭스에 대한 이해와 사용방법 숙지

# 개요
* 리엑트는 단방향 데이터 흐름과 컴포넌트의 조합이 핵심.
* React의 Tree 구조에서 하위 노드로의 props 전달을 위해서는 많은 단계의 callback 함수를 생성 처리해야함.
(특히나 리팩토링은 aaaa....)
  
=> 그럼에도 리엑트의 UI 구성방법은 합당합니다.(복잡성은 낮아지고, 관심사가 분리, 반응형에 좋다(?))  
  
# 플럭스란
* Facebook에서 Application achitectural guideline 으로 제시.
* React와 별개의 구조. React에 종속적이지 않다.
* 기본적으로는 액션, 스토어, 디스패쳐등으로 구성된다.

**MVC와는 다르다! MVC와는...**   
  
![](https://camo.githubusercontent.com/40f58293ab7a1dcc6bfabe6e76bd79dd95bd7e19/68747470733a2f2f66616365626f6f6b2e6769746875622e696f2f666c75782f696d672f666c75782d73696d706c652d66382d6469616772616d2d6578706c61696e65642d31333030772e706e67)  
_개인적으로는 책에서의 그림 설명이 더 좋다고 생각한다.(책 참조)_  

## Store
* Store는 store이다. 결국 데이터 관리하는 역활
* 플럭스 아키텍처에서 이 스토어의 데이터가 변경 될 때, 뷰는 알림을 받게 된다.
* 스토어는 이벤트를 발송하며, 뷰는 스토어를 구독하는 관계.(땔래야 땔 수 없는 관계?)
* **스토어는 완전히 폐쇄된 블랙박스이다. 공용접근자(getter)를 제공하지만, 스토어의 데이터를 변경, 갱신, 삽입은 불가능!! 즉, 읽기만 가능하다.**(이봐, MVC 랑은 다르지??) 

## Action
* 액션은 액션이다.즉 '앱에서 일어나는 일'이다. 
* 액션은 컴포넌트로 부터 생성되어(사용자 반응), 디스패쳐를 통해 스토어로 전달된다.

## Dispatcher
* Action 과 Store를 연결하는 중개자.
* 여러 Store 사이의 순서를 제어.
_책의 그림을 한번 다시 보자._

[event emitter](http://haruair.com/blog/3396)
EventEmitter는 Node.JS에 내장되어 있는 일종의 옵저버 패턴 구현이다. 

## 실용성 없는 최소 플럭스 앱
책 참조(비지니스 로직)   
git hub 참조(코드)   

# Flux Util
2.1 버전 이후, 몇몇 유틸리티가 추가되었다.(개발 시 반복되는 작업을 줄일 수 잇음.)  
  
## Store

*   flux 기본 Store 구조 구현
*   EventEmitter 내장.
*   __onDispatch 함수 구현을 통해 Dispatcher 등록.

## Reduce Store

*   특별한 종류의 Store
*   reduce 라는 함수를 통해 현재의 State 를 재구성
*   몇가지 state에 대한 제약 사항이 따름
*   Single primitive values
    *   Array primitive (ex [1,2,3,4])
    *   Object primitive (ex { name : "sshyun", age : "38"})
    *   React immutable helper들을 사용하여 만든 중첩 Object.

## Map Store

*   ReduceStore 의 변종.
*   Single Value 대신 key / value Store 를 지원.

## [](#container)Container

*   Store 의 변경에 따라 App 의 state 를 자동으로 업데이트 해주는 Continer
*   APP pure 한 Container 역할
    *   create {pure : true} 옵션.
    *   UI Rendering은 하지 않음.
*   어떤 Props 에도 접근이 불가.
*   Store의 Event Listening 자동 처리.
*   WillMount / Unmount시의 동작 내장.

# [](#asynchronous-flux)Asynchronous Flux

*   React에서는 Ajax 처리와 같은 비동기 처리를 Dispatcher 에 waitFor 함수를 사용해서 실행 순서를 보장한다.

<div class="highlight highlight-source-js">

<pre><span class="pl-k">var</span> flightDispatcher <span class="pl-k">=</span> <span class="pl-k">new</span> <span class="pl-en">Dispatcher</span>();

<span class="pl-c">// Keeps track of which country is selected</span>
<span class="pl-k">var</span> CountryStore <span class="pl-k">=</span> {country<span class="pl-k">:</span> <span class="pl-c1">null</span>};

<span class="pl-c">// Keeps track of which city is selected</span>
<span class="pl-k">var</span> CityStore <span class="pl-k">=</span> {city<span class="pl-k">:</span> <span class="pl-c1">null</span>};

<span class="pl-c">// Keeps track of the base flight price of the selected city</span>
<span class="pl-k">var</span> FlightPriceStore <span class="pl-k">=</span> {price<span class="pl-k">:</span> <span class="pl-c1">null</span>};

<span class="pl-smi">CountryStore</span>.<span class="pl-smi">dispatchToken</span> <span class="pl-k">=</span> <span class="pl-smi">flightDispatcher</span>.<span class="pl-en">register</span>(<span class="pl-k">function</span>(<span class="pl-smi">payload</span>) {
  <span class="pl-k">if</span> (<span class="pl-smi">payload</span>.<span class="pl-smi">actionType</span> <span class="pl-k">===</span> <span class="pl-s"><span class="pl-pds">'</span>country-update<span class="pl-pds">'</span></span>) {
    <span class="pl-smi">CountryStore</span>.<span class="pl-smi">country</span> <span class="pl-k">=</span> <span class="pl-smi">payload</span>.<span class="pl-smi">selectedCountry</span>;
  }
});

<span class="pl-smi">CityStore</span>.<span class="pl-smi">dispatchToken</span> <span class="pl-k">=</span> <span class="pl-smi">flightDispatcher</span>.<span class="pl-en">register</span>(<span class="pl-k">function</span>(<span class="pl-smi">payload</span>) {
  <span class="pl-k">if</span> (<span class="pl-smi">payload</span>.<span class="pl-smi">actionType</span> <span class="pl-k">===</span> <span class="pl-s"><span class="pl-pds">'</span>country-update<span class="pl-pds">'</span></span>) {
    <span class="pl-c">// `CountryStore.country` may not be updated.</span>
    <span class="pl-smi">flightDispatcher</span>.<span class="pl-en">waitFor</span>([<span class="pl-smi">CountryStore</span>.<span class="pl-smi">dispatchToken</span>]);
    <span class="pl-c">// `CountryStore.country` is now guaranteed to be updated.</span>

    <span class="pl-c">// Select the default city for the new country</span>
    <span class="pl-smi">CityStore</span>.<span class="pl-smi">city</span> <span class="pl-k">=</span> <span class="pl-en">getDefaultCityForCountry</span>(<span class="pl-smi">CountryStore</span>.<span class="pl-smi">country</span>);
  }
});

<span class="pl-smi">FlightPriceStore</span>.<span class="pl-smi">dispatchToken</span> <span class="pl-k">=</span>
  <span class="pl-smi">flightDispatcher</span>.<span class="pl-en">register</span>(<span class="pl-k">function</span>(<span class="pl-smi">payload</span>) {
    <span class="pl-k">switch</span> (<span class="pl-smi">payload</span>.<span class="pl-smi">actionType</span>) {
      <span class="pl-k">case</span> <span class="pl-s"><span class="pl-pds">'</span>country-update<span class="pl-pds">'</span></span><span class="pl-k">:</span>
      <span class="pl-k">case</span> <span class="pl-s"><span class="pl-pds">'</span>city-update<span class="pl-pds">'</span></span><span class="pl-k">:</span>
        <span class="pl-smi">flightDispatcher</span>.<span class="pl-en">waitFor</span>([<span class="pl-smi">CityStore</span>.<span class="pl-smi">dispatchToken</span>]); <span class="pl-c">// CityStore 에서 처리후에 다음 단계 실행.</span>
        <span class="pl-smi">FlightPriceStore</span>.<span class="pl-smi">price</span> <span class="pl-k">=</span>
          <span class="pl-en">getFlightPriceStore</span>(<span class="pl-smi">CountryStore</span>.<span class="pl-smi">country</span>, <span class="pl-smi">CityStore</span>.<span class="pl-smi">city</span>);
        <span class="pl-k">break</span>;
  }
});</pre>

</div>

**흠, Flux랑 Redux와의 차이는 무엇일까요??**  
**Redux vs Flux 장단점은 무엇일까요??**  
**Redux나 Flux를 React가 아닌 다른 데에서도 효율적으로 잘 쓸수 있을까요??**   

[event emitter](https://github.com/facebook/emitter)
[dispatcher](https://facebook.github.io/flux/docs/dispatcher.html)
