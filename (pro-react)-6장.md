<div class="markdown-body">

# [](#flux)Flux

*   React의 중심철학은 한방향의 Data flow.
*   그러나 중첩되고 복잡한 Application 을 구현시에 이런 철학을 지켜내기는 어려움.
*   React의 Tree 구조에서 하위 노드로의 props 전달을 위해서는 많은 단계의 callback 함수를 생성 처리해야함.
*   Application 이 커짐에 따라 어떻게 React 컴포넌트 트리 구조간의 Callback 관리와 Data 전달및 접근을 효과적인 처리에 대한 해답.

# [](#flux-achitecture)Flux Achitecture

*   Facebook에서 Application achitectural guideline 으로 제시.
*   React와 별개의 구조. React에 종속적이지 않다?
*   Flux의 포인트는 Application의 Data 의 흐름을 한방향으로 유지 시켜주는것

![](https://camo.githubusercontent.com/40f58293ab7a1dcc6bfabe6e76bd79dd95bd7e19/68747470733a2f2f66616365626f6f6b2e6769746875622e696f2f666c75782f696d672f666c75782d73696d706c652d66382d6469616772616d2d6578706c61696e65642d31333030772e706e67)

## [](#store)Store

*   Data를 View 에 전달 하는 역할.
*   현재 Application의 상태값을 저장하고 있고 이를 반환만 해준다.
*   Black Box 로 되어 있고 Data를 접근 하는 부분만 공개되어있다.
*   Store에서 Event를 통하여 View의 데이터를 갱신.
*   Action 에 대한 처리 정의.
*   Data 갱신.

<div class="highlight highlight-source-js">

<pre><span class="pl-k">import</span> {<span class="pl-smi">EventEmitter</span>} <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>fbemitter<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> <span class="pl-smi">AppDispatcher</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./AppDispatcher<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> <span class="pl-smi">Constants</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./Constants<span class="pl-pds">'</span></span>;

<span class="pl-k">const</span> <span class="pl-c1">UPDATE_EVENT</span> <span class="pl-k">=</span> <span class="pl-s"><span class="pl-pds">"</span>update<span class="pl-pds">"</span></span>;
<span class="pl-k">let</span> __emitter <span class="pl-k">=</span> <span class="pl-k">new</span> <span class="pl-en">EventEmitter</span>();
<span class="pl-k">let</span> jobList <span class="pl-k">=</span> [];

<span class="pl-k">let</span> DoStore <span class="pl-k">=</span> {

    <span class="pl-en">getState</span>(){
        <span class="pl-k">return</span> jobList;
    },  
    <span class="pl-en">addLitener</span>(<span class="pl-smi">callback</span>){
        <span class="pl-k">return</span> <span class="pl-smi">__emitter</span>.<span class="pl-en">addListener</span>(<span class="pl-c1">UPDATE_EVENT</span>, callback);
    }

};

<span class="pl-smi">DoStore</span>.<span class="pl-smi">dispatchToken</span> <span class="pl-k">=</span> <span class="pl-smi">AppDispatcher</span>.<span class="pl-en">register</span>((<span class="pl-smi">action</span>) <span class="pl-k">=></span> {
        <span class="pl-k">switch</span>(<span class="pl-smi">action</span>.<span class="pl-c1">type</span>){
            <span class="pl-k">case</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_CREATE</span> <span class="pl-k">:</span>
                jobList <span class="pl-k">=</span> [];
                <span class="pl-smi">__emitter</span>.<span class="pl-en">emit</span>(<span class="pl-c1">UPDATE_EVENT</span>);
                <span class="pl-k">break</span>;
            <span class="pl-k">case</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_INSERT</span> <span class="pl-k">:</span>
                <span class="pl-smi">jobList</span>.<span class="pl-c1">push</span>(<span class="pl-smi">action</span>.<span class="pl-smi">job</span>);
                <span class="pl-smi">__emitter</span>.<span class="pl-en">emit</span>(<span class="pl-c1">UPDATE_EVENT</span>);
                <span class="pl-k">break</span>;
            <span class="pl-k">case</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_DELETE</span> <span class="pl-k">:</span>
                jobList <span class="pl-k">=</span> <span class="pl-smi">jobList</span>.<span class="pl-en">filter</span>(<span class="pl-k">function</span>(<span class="pl-smi">val</span>, <span class="pl-smi">index</span>){
                    <span class="pl-k">return</span> index <span class="pl-k">!==</span> <span class="pl-smi">action</span>.<span class="pl-smi">jobIndex</span>;
                });
                <span class="pl-smi">__emitter</span>.<span class="pl-en">emit</span>(<span class="pl-c1">UPDATE_EVENT</span>);
                <span class="pl-k">break</span>;
        }
    });

<span class="pl-k">export</span> <span class="pl-v">default</span> <span class="pl-smi">DoStore</span>;</pre>

</div>

## [](#action)Action

*   Appication의 동작을 정의.
*   Click 이나, Ajax, Socket 이벤트 등등의 동작.
*   모든 액션은 type 과 옵션 payload 로 구성된다.
*   dispatcher 를 통해 Store 에 Data를 전달.

<div class="highlight highlight-source-js">

<pre><span class="pl-k">import</span> <span class="pl-smi">AppDispatcher</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./AppDispatcher<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> <span class="pl-smi">Constants</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./Constants<span class="pl-pds">'</span></span>;

<span class="pl-k">let</span> DoAction <span class="pl-k">=</span> {

    <span class="pl-en">createList</span>(){
        <span class="pl-smi">AppDispatcher</span>.<span class="pl-en">dispatch</span>({
            type <span class="pl-k">:</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_CREATE</span>
        });
    },

    <span class="pl-en">createDo</span>(<span class="pl-smi">jobStr</span>){
        <span class="pl-smi">AppDispatcher</span>.<span class="pl-en">dispatch</span>({
            type <span class="pl-k">:</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_INSERT</span>,
            job <span class="pl-k">:</span> jobStr
        });
    },

    <span class="pl-en">deleteDo</span>(<span class="pl-smi">jobIndex</span>){
        <span class="pl-smi">AppDispatcher</span>.<span class="pl-en">dispatch</span>({
            type <span class="pl-k">:</span> <span class="pl-smi">Constants</span>.<span class="pl-c1">DO_DELETE</span>,
            jobIndex <span class="pl-k">:</span> jobIndex
        });
    }
};

<span class="pl-k">export</span> <span class="pl-v">default</span> <span class="pl-smi">DoAction</span>;</pre>

</div>

## [](#dispatcher)Dispatcher

*   Action 과 Store를 연결하는 중개자.
*   여러 Store 사이의 순서를 제어.
*   비동기의 여러 Store 동작이 필요할때 처리순서에 대한 제어를 수행.
*   Action 별 Callback 에대한 등록.
    *   Store당 하나의 ID에 맵핑되는 Callback을 등록. 해당 Callback실행을 통해 Data 전달.

<div class="highlight highlight-source-js">

<pre><span class="pl-k">import</span> {<span class="pl-smi">Dispatcher</span>} <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>flux<span class="pl-pds">'</span></span>;

<span class="pl-k">class</span> <span class="pl-en">AppDispatcher</span> <span class="pl-k">extends</span> <span class="pl-e">Dispatcher</span>{
    <span class="pl-en">dispatch</span>(<span class="pl-smi">action</span> <span class="pl-k">=</span> {}){
        <span class="pl-en">console</span>.<span class="pl-c1">log</span>(<span class="pl-s"><span class="pl-pds">"</span>DISPATCH<span class="pl-pds">"</span></span>, action);
        <span class="pl-v">super</span>.<span class="pl-en">dispatch</span>(action);
    }
}

<span class="pl-k">export</span> <span class="pl-v">default</span> <span class="pl-smi">new</span> <span class="pl-en">AppDispatcher</span>();</pre>

</div>

## [](#view)View

*   Store의 State 데이터를 사용하여 View 를 그려줌.
*   React Component
*   Store의 이벤트 리스너 등록.

<div class="highlight highlight-source-js">

<pre><span class="pl-k">import</span> <span class="pl-smi">React</span>, { <span class="pl-smi">Component</span> } <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>react<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> { <span class="pl-smi">render</span> } <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>react-dom<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> <span class="pl-smi">DoStore</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./DoStoreExStore<span class="pl-pds">'</span></span>;
<span class="pl-k">import</span> <span class="pl-smi">DoAction</span> <span class="pl-k">from</span> <span class="pl-s"><span class="pl-pds">'</span>./DoAction<span class="pl-pds">'</span></span>;

<span class="pl-k">class</span> <span class="pl-en">App</span> <span class="pl-k">extends</span> <span class="pl-e">Component</span> {
    <span class="pl-en">constructor</span>(){
        <span class="pl-v">super</span>(<span class="pl-k">...</span>arguments);
        <span class="pl-smi">DoAction</span>.<span class="pl-en">createList</span>();
        <span class="pl-v">this</span>.<span class="pl-smi">state</span> <span class="pl-k">=</span> {
            jobList <span class="pl-k">:</span> <span class="pl-smi">DoStore</span>.<span class="pl-en">getState</span>()
        };
    }

    <span class="pl-en">componentDidMount</span>(){
        <span class="pl-v">this</span>.<span class="pl-smi">storeSubscription</span> <span class="pl-k">=</span> <span class="pl-smi">DoStore</span>.<span class="pl-en">addListener</span>(<span class="pl-smi">data</span> <span class="pl-k">=></span> <span class="pl-v">this</span>.<span class="pl-en">handleStoreChange</span>(data));
    }

    <span class="pl-en">componentWillMount</span>(){
        <span class="pl-k">if</span>(<span class="pl-v">this</span>.<span class="pl-smi">storeSubscription</span>){
            <span class="pl-v">this</span>.<span class="pl-smi">storeSubscription</span>.<span class="pl-en">remove</span>();
        }
    }

    <span class="pl-en">handleStoreChange</span>(){
        <span class="pl-v">this</span>.<span class="pl-en">setState</span>({jobList <span class="pl-k">:</span> <span class="pl-smi">DoStore</span>.<span class="pl-en">getState</span>()});
    }

    <span class="pl-en">_createJob</span>(<span class="pl-c1">event</span>){
        <span class="pl-smi">DoAction</span>.<span class="pl-en">createDo</span>(<span class="pl-v">this</span>.<span class="pl-smi">refs</span>.<span class="pl-c1">text</span>.<span class="pl-c1">value</span>);
        <span class="pl-v">this</span>.<span class="pl-smi">refs</span>.<span class="pl-c1">text</span>.<span class="pl-c1">value</span> <span class="pl-k">=</span> <span class="pl-s"><span class="pl-pds">"</span><span class="pl-pds">"</span></span>;
    }
    <span class="pl-en">_deleteJob</span>(<span class="pl-c1">event</span>){
        <span class="pl-c1">event</span>.<span class="pl-en">preventDefault</span>();
        <span class="pl-k">let</span> index <span class="pl-k">=</span> <span class="pl-c1">event</span>.<span class="pl-c1">target</span>.<span class="pl-c1">getAttribute</span>(<span class="pl-s"><span class="pl-pds">"</span>data-index<span class="pl-pds">"</span></span>);
        <span class="pl-smi">DoAction</span>.<span class="pl-en">deleteDo</span>(<span class="pl-c1">Number</span>(index));
    }
    <span class="pl-en">_getJobList</span>(){
        <span class="pl-k">const</span> <span class="pl-c1">jobList</span> <span class="pl-k">=</span> <span class="pl-v">this</span>.<span class="pl-smi">state</span>.<span class="pl-smi">jobList</span>;

        <span class="pl-k">return</span> <span class="pl-smi">jobList</span>.<span class="pl-en">map</span>((<span class="pl-smi">job</span>, <span class="pl-smi">index</span>) <span class="pl-k">=></span> {
            <span class="pl-k">return</span> (<span class="pl-k"><</span>li key<span class="pl-k">=</span>{ index }<span class="pl-k">></span> {job} <span class="pl-k"><</span>span<span class="pl-k">><</span>a href<span class="pl-k">=</span><span class="pl-s"><span class="pl-pds">"</span>#<span class="pl-pds">"</span></span> data<span class="pl-k">-</span>index<span class="pl-k">=</span>{ index } onClick<span class="pl-k">=</span>{ <span class="pl-v">this</span>.<span class="pl-smi">_deleteJob</span>.<span class="pl-en">bind</span>(<span class="pl-v">this</span>) }<span class="pl-k">></span>Delete<span class="pl-k"><</span><span class="pl-k">/</span>a<span class="pl-k">><</span><span class="pl-k">/</span>span<span class="pl-k">><</span><span class="pl-k">/</span>li<span class="pl-k">></span>)
        });
    };

    <span class="pl-en">render</span>(){
        <span class="pl-k">return</span> (
            <span class="pl-k"><</span>div<span class="pl-k">></span>
            <span class="pl-k"><</span>header<span class="pl-k">></span>ToDO App<span class="pl-k"><</span><span class="pl-k">/</span>header<span class="pl-k">></span>
        <span class="pl-k"><</span>span<span class="pl-k">></span><span class="pl-k">do</span> Action <span class="pl-k">:</span> <span class="pl-k"><</span><span class="pl-k">/</span>span<span class="pl-k">><</span>input type<span class="pl-k">=</span><span class="pl-s"><span class="pl-pds">"</span>text<span class="pl-pds">"</span></span> ref<span class="pl-k">=</span><span class="pl-s"><span class="pl-pds">"</span>text<span class="pl-pds">"</span></span> name<span class="pl-k">=</span><span class="pl-s"><span class="pl-pds">"</span>job<span class="pl-pds">"</span></span><span class="pl-k">/</span><span class="pl-k">></span> <span class="pl-k"><</span>button onClick<span class="pl-k">=</span>{ <span class="pl-v">this</span>.<span class="pl-smi">_createJob</span>.<span class="pl-en">bind</span>(<span class="pl-v">this</span>) }<span class="pl-k">></span>Insert<span class="pl-k"><</span><span class="pl-k">/</span>button<span class="pl-k">></span>
        <span class="pl-k"><</span>ul<span class="pl-k">></span>
            { <span class="pl-v">this</span>.<span class="pl-en">_getJobList</span>() }          
        <span class="pl-k"><</span><span class="pl-k">/</span>ul<span class="pl-k">></span>
            <span class="pl-k"><</span><span class="pl-k">/</span>div<span class="pl-k">></span>
        );
    }
}

<span class="pl-en">render</span>(<span class="pl-k"><</span>App <span class="pl-k">/</span><span class="pl-k">></span>, <span class="pl-c1">document</span>.<span class="pl-c1">getElementById</span>(<span class="pl-s"><span class="pl-pds">'</span>root<span class="pl-pds">'</span></span>));</pre>

</div>

# [](#flux-util)Flux Util

## [](#store-1)Store

*   flux 기본 Store 구조 구현
*   EventEmitter 내장.
*   __onDispatch 함수 구현을 통해 Dispatcher 등록.

## [](#reduce-store)Reduce Store

*   특별한 종류의 Store
*   reduce 라는 함수를 통해 현재의 State 를 재구성
*   몇가지 state에 대한 제약 사항이 따름
*   Single primitive values
    *   Array primitive (ex [1,2,3,4])
    *   Object primitive (ex { name : "sshyun", age : "38"})
    *   React immutable helper들을 사용하여 만든 중첩 Object.

## [](#map-store)Map Store

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

</div>