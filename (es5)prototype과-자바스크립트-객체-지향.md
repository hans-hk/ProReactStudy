# 객체 지향 자바스크립트
비록 다른 객체지향적인 언어들과의 차이점에 대한 논쟁들이 있긴 하지만, JavaScript는 강력한 객체지향 프로그래밍 능력들을 지니고 있다.
  
## 프로토타입기반 프로그래밍(Prototype-based programming)
프로토타입 기반 프로그래밍은 클래스가 존재하지 않는 객체지향 프로그래밍의 한가지 스타일로, 동작 재사용(behavior reuse, 클래스기반 언어에서는 상속이라고함)은 프로토타입으로서 존재하는 객체를 데코레이팅하는 과정을 통해 수행된다.

## 자바스크립트 객체지향 프로그래밍(JavaScript Object Oriented Programming)
```javascript
var student = {
  name : "수지",
  printName : function(){
  }
}

student.printName(); // 여러개의 인스턴스를 만들 수 없다.
```

### 생성자
생성자는 인스턴스화되는 순간((객체 인스턴스가 생성되는 순간) 호출된다. 생성자는 해당 클래스의 메서드이다. 자바스크립트에서는 함수 자체가 그 객체의 생성자 역할을 하기 때문에 특별히 생성자 메서드를 정의할 필요가 없다. 클래스 안에 선언된 모든 내역은 인스턴스화되는 그 시간에 실행된다.생성자는 주로 객체의 속성을 설정하거나 사용하기 위해 객체를 준비시키는 메서드를 호출할 때 주로 사용된다. 

```javascript
function Person() {
  alert('Person instantiated');
}

var person1 = new Person();
var person2 = new Person();
```

### The Property (object attribute)

```javascript
function Person(gender) {
  this.gender = gender;
  alert('Person instantiated');
}

var person1 = new Person('Male');
var person2 = new Person('Female');

//display the person1 gender
alert('person1 is a ' + person1.gender); // person1 is a Male
```
[Private Members in JavaScript](http://javascript.crockford.com/private.html)
>> javascript 에서도 private 멤버를 생성하는 트릭이 있고, priviliged 메서드를 통해 이에 접근할 수 있다.

## 메서드(The methods)
```javascript
function Person(gender) {
  this.gender = gender;
  alert('Person instantiated');
}

Person.prototype.sayHello = function()
{
  alert ('hello');
};

var person1 = new Person('Male');
var person2 = new Person('Female');

// call the Person sayHello method.
person1.sayHello(); // hello
```
```javascript
function Person(gender) {
  this.gender = gender;
}

Person.prototype.sayGender = function()
{
  alert(this.gender);
};

var person1 = new Person('Male');
var genderTeller = person1.sayGender;

person1.sayGender(); // alerts 'Male'
genderTeller(); // alerts undefined
alert(genderTeller === person1.sayGender); // alerts true
alert(genderTeller === Person.prototype.sayGender); // alerts true
```

위의 예제는 많은 개념들을 한꺼번에 보여주고 있다. 

먼저 이 예제는 자바스크립트에 "per-object methods" 가 존재하지 않는다는 것을 보여준다. JavaScript는 메서드에 대한 레퍼런스가 모두 똑같은 (프로토타입에 처음 정의한) 함수를 참조하고 있기 때문이다.
 
자바스크립트는 어떤 객체의 메서드로서 함수가 호출될 때 현재 "객체의 컨텍스트"를 특별한 "this" 변수에 "연결한다". 이는 아래와 같이 function 객체의 call 메서드를 호출하는 것과 동일하다.
 
(참고로, genderTeller() 만 호출했을 때 undefined 가 나타난 것은 해당 메서드가 호출될 때 컨텍스트가 window 로 잡혔기 때문에 window.gender 는 존재하지 않으므로 undefined 가 나타난 것이다.)

```javascript
genderTeller.call(person1); //alerts 'Male'
```  
  
[call](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)  
[apply](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)  

## 상속
```javascript
// define the Person Class
function Person() {}

Person.prototype.walk = function(){
  alert ('I am walking!');
};
Person.prototype.sayHello = function(){
  alert ('hello');
};

// define the Student class
function Student() {
  // Call the parent constructor
  Person.call(this);
}

// inherit Person
Student.prototype = new Person();

// correct the constructor pointer because it points to Person
Student.prototype.constructor = Student;
 
// replace the sayHello method
Student.prototype.sayHello = function(){
  alert('hi, I am a student');
}

// add sayGoodBye method
Student.prototype.sayGoodBye = function(){
  alert('goodBye');
}

var student1 = new Student();
student1.sayHello();
student1.walk();
student1.sayGoodBye();

// check inheritance
alert(student1 instanceof Person); // true 
alert(student1 instanceof Student); // true
```

# 프로토타입

[(mdn)상속과 프로토타입](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)  
[(mdn)new 연산자](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)  
[(기발자님 블로그)Property와 Method](https://brunch.co.kr/@brunch92ny/17)


https://opentutorials.org/module/570/5102

https://github.com/zhenos/WebUIProject-3rdSemester/wiki/JavaScript-Chapter-6

http://www.nextree.co.kr/p7323/

