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

## 제네레이터 메소드
<img src='http://www.hotel-r.net/im/hotel/de/relax-1.jpg' width='400' alt'일단 마음을 진정시키자!' title='일단 마음을 진정시키자!'/>    
[[(es6)이터레이터]]  
    
```javascript
class MyClass {
 * generator_func(){
   yield 1;
   yield 2;
   yield 3;
   yield 4;
   yield 5;
 }
}

var obj = new MyClass();
let gen = obj.generator_func();

console.log(gen.next().value); //1
console.log(gen.next().value); //2
console.log(gen.next().value); //3
console.log(gen.next().value); //4
console.log(gen.next().value); //5
console.log(gen.next().done);  //true
```

## 정적메서드 
```javascript
class Student {
  constructor(name){
     this.name = name;
  }

  static findName(student){
     return student.name;
  }
}

var s = new Student('kdo');
var name = Student.findName(s); 
console.log(name);//kdo
```

es5)  
```javascript
function Student(name){
 this.name = name;
}

Student.findName = function(student){
   return student.name;
}

var s = new Student('kdo');
var name = Student.findName(s); 
console.log(name);//kdo
```
  
![class](http://exploringjs.com/es6/images/classes----methods.jpg)  
[es6 class](http://exploringjs.com/es6/ch_classes.html)  
  
**그래서 결국엔 es6나 es5나 클래스 구현 방식에 큰 차이는 없다.**  
이쯤에서 기억해야 할 것은 **모든 것은 프로타입과 연관 되어 있따! 다만 es5의 새로운 구현이다.**  
```javascript  
var A = class (){
  constructor(){
  }
  
  a(){
     b();//자바가 아니다.
  }
  
  b(){ 
      console.log('test');
  }
}

new A().a();//error

A = class (){
  constructor(){
  }
  
  a(){
     this.b();
  }
  
  b(){ 
      console.log('test');
  }
}
new A().a();//test
```  
  
## 상속
es6 에서는 extends, super 키워드가 도입되었다.  
super는  
* 클래스 coonstructor 메서드에서 부모 생성자를 호출한다.
* 클래스 메소드 내부에서 부모 생성자의 정적/비정적 메소드를 참조한다.
  
```javascript  
function A(a){
   this.a = a;
}

A.protype.printA = function(){
   console.log(this.A);
};

class B extends A {
   constructor(a, b){
      super(a);
      this.b = b;
   }

   printB(){
        console.log(this.b);
   }

   static sayHello(){
      console.log('hello');
   }
}

class C extends B {
   constructor(a, b ,c){
      super(a ,b );
      this.c = c;
   }    
   
   printC(){
         console.log(this.c);
   }

   printAll(){
       this.printC();
       super.printB();
       super.printA();
   }
}

var obj = new C(1,2,3);
obj.printAll();// 3  2  1
c.sayHello();//hello
```

```javascript  
class Super {
  static whoami() {
    return "Super";
  }
  lognameA() {
    console.log(Super.whoami());
  }
  lognameB() {
    console.log(this.constructor.whoami());
  }
}
class Sub extends Super {
  static whoami() {
    return "Sub";
  }
}
new Sub().lognameA(); // Super
new Sub().lognameB(); // Sub
```

> 자식 클래스에 constructor 메소드가 없으면 부모 클래스의 constructor 메서드가 자동으로 호출 된다.
  
 
## 조합 메소드명
```javascript
class myClass {
  static ['my' + 'Method'](){
     cosole.log('hi');
  }
}

myClass['my' + 'Method']();//hi
```
```javascript
var s = Symbol('test');

class myClass2 {
   static [s](){
      console.log('hi');
   }
}

myClass2[s]();//hi
```
## 프로퍼티 속성
* 정적 메소드는 쓰기 가능, 설정 가능이지만 열거 불가다.
* 클래스의 prototype와 prototype.constructor 프로퍼티는 쓰기 불가, 열거 불가, 설정 불가이다.
* prototype 프로퍼티의 속성은 쓰기 가능, 설정 가능이지만 열거 불가이다.
  
cf )   
[ES6 In Depth: 클래스](http://hacks.mozilla.or.kr/2016/03/es6-in-depth-classes/)
[Enumerability in ECMAScript 6](http://www.2ality.com/2015/10/enumerability-es6.html)

## 클래스는 호이스팅 안 된다.
```javascript
foo(); // works, because `foo` is hoisted
    
function foo() {}

//--------------------------------------
new Foo(); // ReferenceError
    
class Foo {}

//--------------------------------------
function functionThatUsesBar() {
    new Bar();
}
    
functionThatUsesBar(); // ReferenceError
class Bar {}
functionThatUsesBar(); // OK
```

## 생성자 메서드 결과 오버라이딩
constructor 메서드는 내부에 return 문이 없을 경우 새 인스턴스를 반환한다.  
return문이 있다면 해당 값을 반환한다.
```javascript
class myClass {
   constructor(){
      return Object.create(null);
   }
}

console.log(new myClass() instanceof myClass);// false
```
  
## 정적 접근자 프로퍼티, Symbol.species
정적 접근자 프로퍼티, @@species(Symbol.species 같은 Symbol의 내장객체는 보통 이렇게 표현 합니다.)는 부모 생성자 메서드가 새 인스턴스를 반환하면 어떤 생성자를 써야 할 지 알려줘야 할 때 자식 생성자에 선택적으로 추가한다.
```javascript
class myCustomArray1 extends Array {
   static get [Symbol.species](){
       return Array;
   }
}

class myCustomArray2 extends Array {}

var arr1 = new myCustomArray1(0,1,2,3,4);
var arr2 = new myCustomArray2(0,1,2,3,4);

console.log(arr1 instanceof myCustomArray1);//true
console.log(arr2 instanceof myCustomArray2);//true

arr1 = arr1.map(function(value){
     return value + 1;
});

arr2 = arr2.map(function(value){
     return value + 1;
});

console.log( arr1 instanceof myCustomArray1 );
console.log( arr2 instanceof myCustomArray2 );

console.log( arr1 instanceof Array );
console.log( arr2 instanceof Array );
```