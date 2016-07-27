## 해야 할 것들 
* es6에서의 클래스 사용법
  - es6의 클래스와 프로타입 메서드 
  - get/set
  - 제네레이터 메서드
  - 정적 메서드 
  - 상속
  - 조합 메서드
  - 프로퍼티 속성
  - 클래스는 호이스팅이 안된다더라...
  - 생성자 메서드 결과 오버라이드 
  - 정적 접근자 프로퍼티, Symbol.species
  - 암시적 파라미터, new.target
  - 객체 리터럴 super 사용
  
## es5의 객체 지향
[[(es5)prototype과 자바스크립트 객체 지향]]  
  
## 클래스
> 자바스크립트 객체 지향 모델이 생성자/프로토타입 기반의 상속에 기초하고 있다.  
> es6 클래스는 기존의 모델에 단지 새롭게 추가된 구문일 뿐, 전혀 새로운 객체 지향 모델이 아니다.
> 다만, 그럴지라도 es6에서는 좀 더 간단하면서도 명확하게 접근하고 있다. 
> 사실 클래스 자신도 함수 이며, 클래스는 생성자로 사용한 함수를 생성하는 새로운 구문이다.
  
### 정의
#### 클래스 선언
```javascript
class Student {
  constructor(name){
     this.name = name;
  }
}
var s1 = new Student('kdo');
console.log(s1.name);
```
  
* 클래스는 함수다.
* 클래스 바디 안의 모든 코드는 기본적으로 [strict 모드](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)이다.  

es5 코드 
```javascript
function Student(name){
 this.name = name
}
var s1 = new Student('kdo');
console.log(s1.name);
```

```javascript
class Student {
  constructor(name){
     this.name = name;
  }
}

function School(name){
   this.name = name;
}

console.log(typeof Student);// function 
console.log(typeof Student == typeof School); //true
```
**결론 : 그래서 클래스는 그냥 함수다.**  
  
#### 클래스 표현식
```javascript
var Student = class {
  constructor(name){
     this.name = name;
  }
}
```
  
## 프로타입 메서드
**클래스 바디 안에 있는 메서드는 모두 클래스의 protype 프로퍼티로 추가된다.**
```javascript
class Person {
  constructor(name){
     this.name = name;
  }

  printProfile(){
     consolo.log("이름: " + this.name + ", 나이 : " + this.age );
  }
}
var p = new Student('psy',27);
p.printProfile(); // 이름 : psy , 나이 :27

console.log("printProfile" in p.__proto__ ); //true
console.log("printProfile" in Persion.protype );//true
```

## get/set 메서드 
```javascript
class Person {
  constructor(name){
     this._name_ = name;
  }

  get name(){
     return this._name_;
  }

  set name(name){
     this._name_ = name;  
  }
}

var p = new Person('kdo');
console.log(p.name); //kdo
p.name = 'psy';
console.log(p.name); //psy

console.log('name' in p.__proto__);// true
console.log('name' in Person.prototype);// true

console.log(Object.getOwnPropertyDescriptor(Person.prototype, "name").set );
// function name(name) { this._name_ = name; }
console.log(Object.getOwnPropertyDescriptor(Person.prototype, "name").get );
// function name() { return this._name_; }
console.log(Object.getOwnPropertyDescriptor(p, "_name_").value );
// psy
```
cf) [getOwnPropertyDescriptor](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor)