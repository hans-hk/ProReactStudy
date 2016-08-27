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
  
[dispatcher](https://facebook.github.io/flux/docs/dispatcher.html)  
_책의 그림을 한번 다시 보자._  
  
[event emitter](https://github.com/facebook/emitter)  
>## emit  
  
1.	방출하다  
2.	내뿜다  
3.	발산하다  
미국[imít] 영국[imít]   
  

## 실용성 없는 최소 플럭스 앱
### constants.js   
```javascript
export default {
    CREATED_ACCOUNT: 'created account',
    WITHDREW_FROM_ACCOUNT: 'withdrew from account',
    DEPOSITED_INTO_ACCOUNT: 'deposited into account'
}
```
  
### AppDispatcher.js   
```javascript
import {Dispatcher} from 'flux';

/**
 * 앱 디스패쳐
 */
class AppDispatcher extends Dispatcher {
    /**
     * 디스패치
     * 로깅 기능 확장
     * 기본 디스패치 기능
     * @param action
     */
    dispatch(action = {}) {
        console.log('Dispatched', action);
        super.dispatch(action);
    }
}

export default new AppDispatcher();
```

### BankActions.js   
```javascript
import AppDispatcher from './AppDispatcher';
import bankConstants from './constants';

let BankActions = {

    /**
     * 계좌 개설
     * 리덕스와는 다르다...!!
     */
    createAccount(){
        AppDispatcher.dispatch({
            type: bankConstants.CREATED_ACCOUNT,
            amount: 0
        });
    },
    /**
     * 입금
     * @param amount 금액
     */
    depositIntoAccount(amount){
        AppDispatcher.dispatch({
            type: bankConstants.DEPOSITED_INTO_ACCOUNT,
            amount: amount
        });
    },
    /**
     * 출금
     * @param amount 금액
     */
    withdrawFromAccount(amount){
        AppDispatcher.dispatch({
            type: bankConstants.WITHDREW_FROM_ACCOUNT,
            amount: amount
        })
    }
}

export default BankActions;
```

### BankBalanceStore.js   
```javascript
import {EventEmitter} from 'fbemitter';
import AppDispatcher from './AppDispatcher';
import bankConstants from './constants';

/**
 * 변경 이벤트
 * @type {string}
 */
const CHANGE_EVENT = 'change';

/**
 * 이벤트 에미터
 */
let __emitter = new EventEmitter();

/**
 * 계좌 금액
 * @type {number}
 */
let balance = 0;

let BankBalanceStore = {
    /**
     * 저장된 상태를 반환 한다.
     * @returns {number}
     */
    getState(){
        return balance;
    },
    /**
     * 리스너를 등록 한다.
     * 컴퍼넌트 상에서 사용할 콜백을 등록하기 위해 사용한다.
     * @param callback 콜백
     * @returns {*} 리스너 제거용 토큰
     */
    addListener: (callback) => {
        console.log('addListener');
        return __emitter.addListener(CHANGE_EVENT, callback);
    }
};

/**
 * 디스패쳐에 액션을 등록 했다.
 * 액션이 수행되면 기본적으로 emitter를 통해 컴퍼넌트에 값을 알려준다.
 */
BankBalanceStore.dispatchToken = AppDispatcher.register((action) => {
    switch (action.type) {
        case bankConstants.CREATED_ACCOUNT:
            balance = 0;
            //사실 현 소스 상 이 부분의 이밋은 의미가 없다.
            __emitter.emit(CHANGE_EVENT);
            break;
        case  bankConstants.DEPOSITED_INTO_ACCOUNT:
            balance = balance + action.amount;
            __emitter.emit(CHANGE_EVENT);
            break;
        case bankConstants.WITHDREW_FROM_ACCOUNT:
            balance = balance - action.amount;
            __emitter.emit(CHANGE_EVENT);
            break;
    }
});

export default BankBalanceStore;
```

### App.js   
```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';
import BankBalanceStore from './BankBalanceStore';
import BankActions from './BankActions';

/**
 * app 컴포넌트
 */
class App extends Component {
    /**
     * 생성자
     * 초기 계정을 설정 한다.
     */
    constructor() {
        super(...arguments);
        BankActions.createAccount();
        this.state = {
            balance: BankBalanceStore.getState()
        }
    }

    /**
     * 리스너 등록
     * 이제부터 컴퍼넌트는 스토어의 변화를 감지 하게 된다.
     */
    componentDidMount() {
        console.log('componentDidMount');
        this.storeSubscription = BankBalanceStore.addListener(data => {
            console.log('data : ' + data);// data는 현재 undefined
            this.handleStoreChange(data);
        });
    }

    /**
     * 스토어를 해체 한다.
     * 토큰을 이용하여 해체 한다.
     */
    componentWillUnmount() {
        this.storeSubscription.remove();
    }

    /**
     * 스토어의 상태 값을 컴퍼넌트의 상태로 변경
     */
    handleStoreChange() {
        console.log('test');
        this.setState({balance: BankBalanceStore.getState()});
    }

    /**
     * 입금.
     * 초기화
     */
    deposit() {
        BankActions.depositIntoAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';//refs 값을 이용하여 초기화 한다!
    }

    /**
     * 출금.
     * 초기화
     */
    withdraw() {
        BankActions.withdrawFromAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';
    }

    render() {
        return (
            <div>
                <header>Flux bank</header>
                <h1>금액 ${(this.state.balance).toFixed(2)}</h1>
                <div className="atm">
                    <input type="text" placeholder="금액" ref="amount"/>
                    <br/>
                    <button onClick={this.withdraw.bind(this)}>출금</button>
                    <button onClick={this.deposit.bind(this)}>입금</button>
                </div>
            </div>
        );
    }
}

render(<App/>, document.getElementById("root"));
```




# Flux Util
2.1 버전 이후, 몇몇 유틸리티가 추가되었다.(개발 시 반복되는 작업을 줄일 수 잇음.)  
  
## Store

*   flux 기본 Store 구조 구현
*   EventEmitter 내장.
*   __onDispatch 함수 구현을 통해 Dispatcher 등록.

```javascript
import AppDispatcher from './AppDispatcher';
import {Store} from 'flux/utils'
import bankConstants from './constants';

/**
 * 계좌 금액
 * @type {number}
 */
let balance = 0;

/**
 * 스토어
 */
class BankBalanceStore extends Store {
    /**
     * 상태 값
     * @returns {number}
     */
    getState() {
        return balance;
    }

    /**
     * 디스패치
     * @param action
     * @private
     */
    __onDispatch(action) {
        console.log('__onDispatch');
        switch (action.type) {
            case bankConstants.CREATED_ACCOUNT:
                balance = 0;
                //사실 현 소스 상 이 부분의 이밋은 의미가 없다.
                this.__emitChange();
                break;
            case  bankConstants.DEPOSITED_INTO_ACCOUNT:
                balance = balance + action.amount;
                this.__emitChange();
                break;
            case bankConstants.WITHDREW_FROM_ACCOUNT:
                balance = balance - action.amount;
                this.__emitChange();
                break;
        }
    }
}

export default new BankBalanceStore(AppDispatcher);
```

흠 뭔가 줄긴 했지만....:|

## Reduce Store

*   특별한 종류의 Store
*   reduce 라는 함수를 통해 현재의 State 를 재구성
*   몇가지 state에 대한 제약 사항이 따름
*   Single primitive values
    *   Array primitive (ex [1,2,3,4])
    *   Object primitive (ex { name : "sshyun", age : "38"})
    *   React immutable helper들을 사용하여 만든 중첩 Object.
```javascript
import AppDispatcher from './AppDispatcher';
import {ReduceStore} from 'flux/utils'
import bankConstants from './constants';

/**
 * 스토어
 */
class BankBalanceStore extends ReduceStore {
    /**
     * 초기값
     * @returns {number}
     */
    getInitialState() {
        return 0;
    }

    /**
     * 리듀스
     * @param state 상태
     * @param action 액션
     * @returns {*}
     */
    reduce(state, action) {
        switch (action.type) {
            case bankConstants.CREATED_ACCOUNT:
                return 0;
            case  bankConstants.DEPOSITED_INTO_ACCOUNT:
                return state + action.amount;
            case bankConstants.WITHDREW_FROM_ACCOUNT:
                return state - action.amount;
            default :
                return state;
        }
    }

}

export default new BankBalanceStore(AppDispatcher);
```

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

```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';
import {Container} from 'flux/utils'
import BankBalanceStore from './BankBalanceStore';
import BankActions from './BankActions';

/**
 * app 컴포넌트
 */
class App extends Component {
    /**
     * 생성자
     * 초기 계정을 설정 한다.
     * 별도의 상태 설정은 없다.
     */
    constructor() {
        super(...arguments);
        BankActions.createAccount();
    }

    /**
     * 입금.
     * 초기화
     */
    deposit() {
        BankActions.depositIntoAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';//refs 값을 이용하여 초기화 한다!
    }

    /**
     * 출금.
     * 초기화
     */
    withdraw() {
        BankActions.withdrawFromAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';
    }

    render() {
        return (
            <div>
                <header>Flux bank</header>
                <h1>금액 ${(this.state.balance).toFixed(2)}</h1>
                <div className="atm">
                    <input type="text" placeholder="금액" ref="amount"/>
                    <br/>
                    <button onClick={this.withdraw.bind(this)}>출금</button>
                    <button onClick={this.deposit.bind(this)}>입금</button>
                </div>
            </div>
        );
    }
}

/**
 * 스토어 등록
 */
App.getStores = () => ([BankBalanceStore]);
/**
 * 상태 가져오기
 * @param prevState
 */
App.calculateState = (prevState) => ({balance: BankBalanceStore.getState()});
/**
 * 컨테이너 랩핑
 * @type {App}
 */
const AppContainer = Container.create(App);

render(<AppContainer/>, document.getElementById("root"));
```

# Asynchronous Flux
```javascript
import AppDispatcher from './AppDispatcher';
import BankBalanceStore from './BankBalanceStore';
import  bankConstants from './constants';
import {ReduceStore} from 'flux/utils';

/**
 * 리워드 스토어
 */
class BankRewardsStore extends ReduceStore {
    /**
     * 초기 상태
     * @returns {string}
     */
    getInitialState() {
        return '흙수저';
    }

    /**
     * 리듀스
     * @param state 이전 상태
     * @param action 액션
     * @returns {*} 이후 상태
     */
    reduce(state, action) {
        /**
         * 이렇게 되면 의존 적이 된다.
         */
        this.getDispatcher().waitFor([
            BankBalanceStore.getDispatchToken()
        ]);
        if (action.type === bankConstants.DEPOSITED_INTO_ACCOUNT
            || action.type === bankConstants.WITHDREW_FROM_ACCOUNT
        ) {
            let balance = BankBalanceStore.getState();
            if (balance < 0) {
                return '막장';
            }
            if (balance < 5000) {
                return '흙수저';
            } else if (balance < 10000) {
                return '동수저';
            } else if (balance < 50000) {
                return '은수저';
            } else {
                return '금수저';
            }
        }
        return state;
    }
}

export default new BankRewardsStore(AppDispatcher);
```
  
```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';
import {Container} from 'flux/utils'
import BankBalanceStore from './BankBalanceStore';
import BankRewardsStore from './BankRewardsStore';
import BankActions from './BankActions';

/**
 * app 컴포넌트
 */
class App extends Component {
    /**
     * 생성자
     * 초기 계정을 설정 한다.
     * 별도의 상태 설정은 없다.
     */
    constructor() {
        super(...arguments);
        BankActions.createAccount();
    }

    /**
     * 입금.
     * 초기화
     */
    deposit() {
        BankActions.depositIntoAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';//refs 값을 이용하여 초기화 한다!
    }

    /**
     * 출금.
     * 초기화
     */
    withdraw() {
        BankActions.withdrawFromAccount(Number(this.refs.amount.value));
        this.refs.amount.value = '';
    }

    render() {
        return (
            <div>
                <header>Flux bank</header>
                <h1>금액 ${(this.state.balance).toFixed(2)}</h1>
                <h2>너는 {this.state.tier}</h2>
                <div className="atm">
                    <input type="text" placeholder="금액" ref="amount"/>
                    <br/>
                    <button onClick={this.withdraw.bind(this)}>출금</button>
                    <button onClick={this.deposit.bind(this)}>입금</button>
                </div>
            </div>
        );
    }
}

/**
 * 스토어 등록
 */
App.getStores = () => ([BankBalanceStore, BankRewardsStore]);
/**
 * 상태 가져오기
 * @param prevState
 */
App.calculateState = (prevState) => ({
    balance: BankBalanceStore.getState(),
    tier: BankRewardsStore.getState()
});
/**
 * 컨테이너 랩핑
 * @type {App}
 */
const AppContainer = Container.create(App);

render(<AppContainer/>, document.getElementById("root"));
```

## 생각해 보기 
**흠, Flux랑 Redux와의 차이는 무엇일까요??**  
**Redux vs Flux 장단점은 무엇일까요??**  
**Redux나 Flux를 React가 아닌 다른 데에서도 효율적으로 잘 쓸수 있을까요??**   

## Homework
* 칸반 앱 6장 내용 작성해보기  
* 6장 bank app, 항공권 app redux 버전과 비교해보기  




