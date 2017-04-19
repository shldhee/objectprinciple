# 프로토타입

일부 내장 함수를 제외한 거의 모든 함수에는 새 인스턴스를 생성할 때 사용되는 prototype 프로퍼티가 있다.
해당 함수를 통해 생성된 모든 객체 인스턴스는 함수의 프로토타입을 같이 사용하며 프로토타입의 프로퍼티에도 접근이 가능하다.
예를 들어 hasOwnProperty() 메소드는 Object 프로토타입에 정의되어 있지만 어느 객체에서는 고유 객체처럼 접근할 수 있다.

```js
var book = {
    title: "완벽한 공부법"
};

console.log("title" in book);       // true;
console.log(book.hasOwnProperty("title"));      // true;
console.log("hasownProperty" in book);      // true
console.log(book.hasOwnProperty("hasOwnproperty"));     //  false;
console.log(Object.prototype.hasOwnProperty("hasOwnProperty"))  // true;
```

book 변수에는 hasOwnProperty()가 정의되어 있지 않지만 Object.prorotype에 정의 되어 있기 때문에 book.hasOwnProperty()를 통해 접근이 가능하다.\
***in 연산자는 프로토타입 프로퍼티나 고유 프로퍼티 중 무엣이든 true를 반환한다.***

## 프로포토타입 프로퍼티 확인

```js
function hasPrototypeProperty(object, name) {
    return name in object && !object.hasOwnProperty(name); // hasOwnProperty 생성한 객체에 추가한 프로퍼티 찾기
}

console.log(hasPrototypeProperty(book, "title"));   // false
console.log(hasPrototypeProperty(book, "hasOwnProperty"));   // true

```

1. in 연산자를 이용해 객체에 프로퍼티가 있는지 확인(true반환)
1. hasOwnProperty()를 사용해 false를 반환하면 이 프로퍼티는 프로토타입 프로퍼티로 볼 수 있다.

### [[Prototype]] == \_\_proto\_\_ 프로퍼티

모든 인스턴스는 [[Prototype]]이라는 내부 프로퍼티를 통해 프로토타입의 변화를 추적한다.
이 프로퍼티는 인스턴스가 사용하고 있는 프로토타입 객체를 가리킨다.
new 연산자를 사용해 새 객체를 생성할 때 생성자의 prototype 프로퍼티가 새로 생성된 객체의 [[Prototype]] 프로퍼티에 할당된다.

[[Prototype]] 프로퍼티의 값은 Object.getPrototypeOf() 메소드를 객체에 사용하면 읽을 수 있다.

```js
var object = {};
var prototype = Object.getPrototypeOf(object);

console.log(prototype === Object.prototype); // true
```

***[[Prototype]]은 언제나 Object.prototype을 참조한다.***

모든 객체에서 사용 가능한 isPrototypeOf() 메소드를 사용하면 어떤 객체가 다른 객체의 프로토타입인지 확인 할 수 있따.

```js
var object = {};

console.log(Object.prototype.isPrototypeOf(object));    // true
```

object는 평범한 객체이므로 이 객체의 프로토타입은 Object.prototype이 된다. 따라서 true 반환

객체 프로퍼티 값을 가져 올때 자바스크립트 엔진은

1. 먼저 해당 이름을 가진 고유프로퍼티가 있는지 확인
1. 고유프로퍼티가 없으면 [[Prototype]] 객체어서 해당 프로퍼티 확인
1. [[Prototype]]에도 없으면 undefined 반환

```js
var object = {};

console.log(object.toString()); // 프로토타입 프로퍼티

object.toString = function() {
    return "[object Custom]";
}

console.log(object.toString()); //  고유 프로퍼티

delete object.toString; // 고유 프로퍼티 제거
console.log(object.toString()); // 프로토타입 프로퍼티

delete object.toString; // 변화 없음 프로포토타입 프로퍼티는 삭제 안됨 delete 고유 프로퍼티에만 작동
console.log(object.toString()); // 프로토타입 프로퍼티
```

***인스턴스에서는 프로토타입 프로퍼티에 값을 할당 할 수 없다.***
toString에 값을 할당하면 인스턴스에 새로운 고유 프로퍼티가 만들어지면서 프로토타입에 있는 프로퍼티는 연결되지 않은 상태가 된다.

## 생성자와 프로토타입 함께 사용하기

여러 객체에 공유된다는 프로토타입의 특성을 활용하면 타입이 같은 모든 객체가 같이 사용할 메소드를 한 번만 정의해도 된다.
메소드는 인스턴스에 상관없이 항상 같은 동작을 하므로 굳이 인스턴스마다 따로 메소드를 만들 이유가 없다.

***따라서 메소드를 프로토타입에 두고 this를 사용해 현재 인스턴스에 접근***

```js
function Person(name) {
    this.name = name;
}

Person.prototype.sayName() {
    console.log(this.name);
};

var person1 = new Person("LEE");
var person2 = new Person("OH");

console.log(person1.name); // "LEE"
console.log(person2.name); // "OH"

person1.sayName(); // LEE
person2.sayName(); // OH
```

위 예제는 Person 생성자는 sayName()을 생성자가 아닌 **프로토타입에 정의했다**
이제 sayName()은 고유 프로퍼티가 아닌 **프로토타입 프로퍼티**지만 객체 인스턴스는 앞서 보았던 예제와 똑같이 동작한다.
sayName()을 호출시 person1, person2가 사용개체가 되어 this 의 값은 각각 person1과 person2가 된다.

***프로토타입에는 어떤 종류의 데이터도 저장이 가능하지만 참조 값을 사용할때는 주의해야 한다.***
프로토타입의 저장된 참조값은 여러 인스턴스에 공유되므로 다른 인스턴스에서 수정한 값이 의도치 않게 적용된다.

```js
function Person(name) {
    this.name = name;
};

Person.prototype.sayName = function() {
    console.log(this.name);
};

Person.prototype.favroites = [];

var person1 = new Person("LEE");
var person2 = new Person("OH");

person1.favorites.push("pizza");
person2.favorites.push("coke");

console.log(person1.favorites); // ["pizza","coke"]
console.log(person2.favorites); // ["pizza","coke"]
```

favorites 프로퍼티는 프로토타입에 정의되어 있다.
person1.favorites와 person2.favorites는 둘 다 같은 배열을 참조하고 있다.
따라서 어느 인스턴스에서 favorites 프로퍼티를 수정하면 수정한 값은 프로토타입에 속한 배열(favorites)가 수정된다.

### 프로토타입 리터럴 방식

```js
function Person(name) {
    this.name = name;
};

Person.prototype = {
    sayName = function() {
        console.log(this.name);
    },
    toString = function() {
        return "[Person " + this.name + "]" ;
    }
};
```

프로토타입에 sayName()과 toString() 이라는 메소드 2 개를 정의했다.
이 패턴은 Person.prototype을 여러 번 입력하지 않아도 되므로 많이 이용된다.
하지만 다음과 같은 부작용이 있다.

```js
var person1 = new Person("LEE");

console.log(person1 instanceof Person); // true
console.log(person1.constructor === Person); // false
console.log(person1.constructor === Object); // true
```

* 객체 리터럴 표기법을 사용해 프로토타입을 덮어씌우는 방식은 constructor 프로퍼티도 바꿔버리기 떄문에
* 예제에서 constructor가 Person이 아닌 Object를 참조한다.
* 이 문제는 constructor 프로퍼티는 객체 인스턴스가 아닌 프로토타입에 정의되어 있기 때문에 발생한다.
* constructor 프로퍼티는 함수를 만들 때 함수의 prototype 프로퍼티에 정의되면서 만들어진 함수를 참조한다.
* 앞의 패턴은 프로토타입 객체를 완전히 다시 작성하는 셈이기 떄문에 Person.prototype을 대체하는 (일반) 객체의 constructor가 사용된다.

```js
function Person(name) {
    this.name = name;
};

Person.prototype = {
    constructor: Person,

    sayName = function() {
        console.log(this.name);
    },

    toString = function() {
        return "[Person " + this.name + "]";
    }
};

var person1 = new Person("LEE");
var person2 = new Person("OH");

console.log(person1 instanceof Person); // true
console.log(person1.constructor === Person); // true
console.log(person1.constructor === Object); // false

console.log(person2 instanceof Person); // true
console.log(person2.constructor === Person); // true
console.log(person2.constructor === Object); // false
```

***위 예제는 프로토타입에 저장할 constructor 프로퍼티를 명시적으로 정했다.*** \
***생성자, 프로토타입, 인스턴스 관계에선 생성자와 인스턴스 사이엔 직접적인 연결이 없다.*** \
***하지만 인스턴스와 프로토타입, 프로토타입과 생성자는 서로 직접적으로 연결되어있다.*** \
***위와 같은 관계로 인스턴스와 프로토타입 사이에 연결이 끊어지면 생성자와 인스턴스 간의 연결도 끊어진다.***\

### 프로토타입 체이닝

특정 타입의 인스턴스는 모두 같은 프로토타입을 공유하기 때문에 언제든 한꺼번에 기능을 추가할 수 있다.
[[Prototype]] 프로퍼티는 프로토타입을 참조할 뿐이고 프로토타입의 변화는 프로토타입을 참조하고 있는 모든 인스턴스에 즉시 적용 된다.
이는 프로토타입에 문자 그대로 언제든 새 프로퍼티나 메소드를 추가할 수 있으며 이러한 변화는 이미 만들어진 인스턴스에도 반영된다

```js
function Person(name) {
    this.name = name;
}

Person.prototype ={
    constructor: Person,

    sayName = function() {
        console.log(this.name);
    },

    toString = function() {
        return "[Person " + this.name + "]";
    }
};

var person1 = new Person("LEE");
var person2 = new Person("OH");

console.log("sayHi" in person1);    //false
console.log("sayHi" in person2);    //false

Person.prototype.sayHi = function() {
    console.log("Hi");
};

person1.sayHi();
person2.sayHi();
```

* Person타입엔 있던 메소드는 sayName(), toString() 2개뿐이었다.
* 추가로 sayHi() 메소드를 프로토타입에 추가했다.
* 이 시점에서 두 인스턴스는 모두 sayHi()에 접근할 수 있다.
* 프로퍼티나 메소드를 검색할 때마다 프로토타입에 접근하기 때문에 마치 원래 있던 메소드처럼 자유롭게 사용이 가능하다.

언제든 프로토타입을 수정할 수 있다는 사실은 봉인된 객체와 동결된 객체에 재미있는 영향을 미친다.
객체에 Object.seal(), Object.freeze()를 사용하면 객체 인스턴스와 고유 프로퍼티를 있는 그대로만 사용할 수 있다.
추가, 삭제, 변경 등이 안된다. 동결된 객체의 프로토타입에는 값을 추가 할 수 있다.

```js
var person1 = new Person("LEE");
var person2 = new Person("OH");

Object.freeze(person1);

Person.prototype.sayHi = function() {
    console.log("Hi");
};

person1.sayHi();
person2.sayHi();
```

인스턴스 person1은 동결되었고 인스턴스 person2는 평범한 객체이다.
프로토타입 sayHi() 메소드를 추가하면 person1,person2 객체 모두 새 메소드를 사용할 수 있게 되는데
동결된 person1를 보면 모순된 것처럼 보인다.
***[[Prototype]] 프로퍼티는 인스턴스의 고유 프로퍼티라고 볼 수 있는데 프로퍼티 자체는 동결되어도 프로퍼티의 값(객체)는 동결되지 않는다.***

### 내장 객체의 프로토타입

자바스크립트 엔진에서 기본으로 제공해주는 내장 객체도 수정이 가능하다.
내장 객체는 모두 생성자를 가지고 있으므로 프로토타입을 사용해 변경할 수 있따.

```js
Array.prototype.sum = function() {
    return this.reduce(function(previous, current) {
        return previous + current;
    });
};

var numbers = [1,2,3,4,5,6];
var result = numbers.sum();

console.log(resut); // 21
```

* sum() 메소드를 Array.prototype에 추가
* numbers 배열은 자동으로 프로토타입을 통해 sum() 메소드의 접근
* sum() 메소드의 내부를 살펴보면 this를 통해 Array의 인스턴스인 numbers를 참조
* reduce()같은 다른 배열 메소드처럼 어느 배열에서든 사용가능

문자열, 숫자, 불리언도 원시 값을 객체처럼 사용할 때 임시로 생성하는 내장 원시 래퍼 타입이 있다.
따라서 다음 예제처럼 원시 래퍼 타입의 프로토타입을 수정하면 원시 값에 새로운 기능을 추가하는 셈이 된다.

```js
String.prototype.capitalize = function() {
    return this.charAt(0).toUpperCase() + this.substring(1);
};

var mesage = "hello world!";
console.log(message.capitalize());
```

* 문자열에 capitalize() 메소드 추가
* String 타입은 문자열을 위한 원시 래퍼이므로 String 타입의 프로토타입을 수정하면 자동으로 모든 문자열에 변경사항이 적용된다.
