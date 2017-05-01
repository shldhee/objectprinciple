# 5장 상속

## prototype chaining과 Object.prototype

자바스크립트 상속 방법
* 프로토타입 체이닝
* 프로토타입 상속

프로토타입에 정의된 프로퍼티는 자동으로 모든 객체에서 사용할 수 있게 되는데 이형태가 상속과 유사

객체 인스턴스는 프로토타입으로 프로퍼티를 상속받는다.

프로토타입 또한 하나의 객체이므로 프로토타입에도 자신만의 프로토타입이 있으며 프로토타입으로부터 프로퍼티를 상속받는다.

이러한 특성을 ***프로토타입 체인*** 이라고 부른다.

모든 객체는 별도로 지정하지 않으면 Object를 상속받는다. 엄밀히 말하면 Object.prototype을 상속 받는다. 객체 리터럴을 사용해 정의된 객체의 \[[Prototype]] 프로퍼티는 모두 Object.prototype을 가리킨다.(prototype 정의시 constructor 할당할때!)

```js
var book = { title: "Study" };
var prototype = Object.getPrototypeOf(book);
console.log(prototype === Object.prototype); // true
```

>c.prototype은 new c()로 생성된 객체의 프로토타입을 만드는데 사용된다.
Object.getPrototypeOf(obj) 는 obj의 프로토타입을 객체를 가져오기 위한 표준 ES5 매커니즘이다.
obj.__proto__는 obj의 프로토타입 객체를 가져오는 비표준 매커니즘이다.
결론 __proto__ 보다는 getPrototypeOf를 사용하는게 좋고
__proto__ 는 절대 수정하면 안된다. 이유는
첫째로 성능때문이고
두번째는 예측가능한 동작을 위해서이다.

## Object.prototype에서 메소드 상속

* hasOwnProperty() : 고유 프로포터 존재 확인
* propertyIsEnumerable() : 고유 프로퍼티 열거 가능
* isPrototypeOf() : 객체가 다른 객체의 프로토타입인지 확인
* valueOf() : 객체를 표현하는 값을 반환
* toString() : 객체를 표현하는 문자열을 반환

### valueOf()

객체에 연산자를 사용할 때 호출
연산자를 사용하면 비교를 수행하기 전에 각 객체의 valueOf() 메소드가 호출

### toString()

valueOf()가 원시 값이 아닌 참조 값을 반환할 때 대비책으로 호출
하지만 참조 값이라면 valueOf()가 먼저 호출

```js
var book = { title : "자바스크립트"};

var message = "Book = " + book;
console.log('message', message); // "Book = [object Object]"
```

디폴트 toString() 메소드 호출 이 메소드는 Object.prototype에서 상속받은 것이며 대부분의 자바스크립트 엔진에서는 "[object Object]"를 반환

책의 제목을 출력시엔
```js
var book = {
    title: "객체지향 자바스크립트",
    toString: function() {
        return "[Book = " + this.title + ']'
    }
}

var message = "Book = " + book;

console.log(message);
```

이 코드는 상속받은 것보다 더유용한 정보를 반환하는 toString() 메소드를 book객체에 정의
보통은 toString() 메소드를 작성할 필요가 없지만 필요할 때 이렇게 사용할 수 있다.

## Object.prototype 수정

모든 객체는 Object.prototype을 상속받으므로 Object.prototype을 수정하면 모든 객체에 반영 !하지만

```js
Object.prototype.add = function(value) {
    return this + value;
};

var book = { title: "자바스크립트" };

console.log('book.add(5)', book.add(5));
console.log('title.add("end")',"title".add("end"));

console.log('document.add(true)', document.add(true));
console.log('window.add(5)', window.add(5));
```

Object.prototype.add()를 추가하면 모든 객체에 add() 메서드가 추가 된다.
또 다른 문제는 열거 가능한 프로퍼티를 Object.prototype에 추가할 때 발생
Object.prototype.add() 앞에 예제에서 열거 가능한 프로퍼티다. for-in으로 나타낼수 있다.


```js
Object.prototype.add = function(value) {
    return this + value;
};

var empty = {};
for (var property in empty) {
        console.log('property', property);
}
// empty 객체의 프로토타입 add 프로퍼티가 있고 이 프로퍼티는 열거 가능하므로 "add"출력 Object.prototype에 열거 가능한 프로퍼티가 많을 경우 코드에 영향을 미침

var empty = {};
for (var property in empty) {
    if (empty.hasOwnProperty(property)) {
        console.log('property', property);
    }
}
// 프로토타입을 제외한 고유프로퍼티만 사용
```

***따라서 Object.prototype은 수정하지 않는게 가장 좋다.***

## 객체 상속

새 객체에 \[[Prototype]] 으로 사용할 객체만 설정해주면 상속이 이루어진다.
객체 리터럴은 기본적으로 Object.prototype을 \[[Prototype]]으로 설정하지만 Object.create() 메소드를 사용해 \[[Prototype]]을 명시적으로 해줄 수 있다.

Object.create()
* 첫번째 인수 : 새 객체의 \[[Prototype]]으로 사용할 객체 전달
* 두번째 인수 : Object.defineProperteis()에서 사용할 프로퍼티 서술자 객체(생략 가능)

``` js
var book = {
	title: "객체지향 자바스크립트의 원리"
};

// 위 코드는 아래와 같다.

var book1 = Object.create(Object.prototype, {
	title: {
		value: "객체지향 자바스크립트의 원리",
		configurable: true,
		enumerable: true,
		writable: true
	}
});
```

* 위 방식와 아래 방식의 실행 결과는 같다.
* 첫 번째는 객체 리터를 사용해 title 프로퍼티를 가진 객체 정의
* 이 객체는 자동으로 Object.prototype을 상속하고 title 프로퍼티를 기본 값인 설정 가능, 열거 가능, 쓰기 가능한 상태로 설정
* 두 번째는 Object.create()를 명시적으로 사용
* Object.prototype은 자동으로 상속되기 때문에 이런식으로 사용되지 않고 다른 객체를 상복받을 때 유용하다.

``` js
var person1 = {
	name: "LEE",
	sayName: function() {
		console.log(this.name);
	}
};

var person2 = Object.create(person1, {
	name: {
		configurable: true,
		enumerable: true,
		value: "Oh",
		writable: true
	}
});

person1.sayName(); // "LEE"
person2.sayName(); // "OH"

console.log('person1.hasOwnProperty("sayName")', person1.hasOwnProperty("sayName")); // true
console.log('person1.isPrototypeOf(person2)', person1.isPrototypeOf(person2)); // true person2 프로토타입 체인에 person1 이 포함되어 있는지 없는지
console.log('person2.hasOwnProperty("sayName")', person2.hasOwnProperty("sayName"));  // false
```


| person2  |   |
|:---:|:---:|
| \[[Prototype]]  | -> person1  |
| name  | "LEE"  |

| person1  |   |
|:---:|:---:|
| \[[Prototype]]  | -> Object.prototype  |
| name  | "Oh"  |
| sayName  | (함수)  |

| Object.prototype  |   |
|:---:|:---:|
| \[[Prototype]]  | -> null  |
| hasOwnProperty  | (함수)  |
| propertyisenumerable  | (함수)  |
| isPrototypeOf  | (함수)  |
| toString  | (함수)  |
| valueOf  | (함수)  |

***person2의 프로토타입 체인에는 person1과 Object.prototype이 연결되어 있다.***

객체의 프로퍼티에 접근할 때 자바스크립트 엔진은 검색과정을 거친다. 프로퍼티가 처음 접근한 인스턴스에 있으면(즉, 고유프로퍼티라면) 그 프로퍼티의 값이 사용된다. 프로퍼티가 해당 인스턴스에 없으면 이어 \[[Prototype]]을 탐색한다. 여기서도 못찾으면 인스턴스의 \[[Prorotype]]의 \[[Prototype]] 에서 계속 검색하고 이 과정을 체인이 끝날 때까지 계속한다.
보통 체인의 끝은 Object.prototype인데 이 객체의 \[[Prototype]]은 null이다.

isPrototypeOf : 참조하고 있는 프로포토타입 확인

``` js
var Person = function () {};
var Programer = function () {};

Programer.prototype = new Person();
var test2 = new Programer();

console.log(Person.prototype.isPrototypeOf(test2)); // true
console.log(Programer.prototype.isPrototypeOf(test2)); // true
console.log(Object.prototype.isPrototypeOf(test2)); // true
```

## 생성자 상속

모든 함수에는 prototype 프로퍼티가 있으며 이 프로퍼티는 수정하거나 교체될 수 있다.
prototype 프로퍼티는 따로 설정하지 않으면 기본적으로 Object.prototype을 상속받는 일반 객체 인스턴스가 되며 이 인스턴스는 constructor라는 고유 프로퍼티를 가진다.

```js
// 작성 코드
function YourConstrcutor() {

}

// 실제 자바스크립트 엔진이 하는일
YourConstrcutor.prototype = Object.create(Object.prototype, {
	constructor: {
		configurable: true,
		enumerable: true,
		value: YourConstrcutor,
		writable: true
	}
});

``` js
function Rectangle(length, width) {
	this.length = length;
	this.width  width;
}

Rectangle.prototype.getArea = function() {
	return this.length * this.width;
}

function Square(size) {
	this.length = size;
	this.width = size;
}
// 1
Square.prototype = new Rectangle();
Square.constructor = Square;

// 2
Square.prototype = Object.create(Rectangle.prototype, {
	constructor : {
		configurable: true,
		enumerable: true,
		value: Square,
		writable: true
	}
});
```

## 생성자 훔치기

상속은 프로토타입을 통해 이루어지기 때문에 객체의 상위타입 생성자를 호출하지 않아도 된다. 상위타입의 생성자를 하위타입의 생성자에서 호출할려면 자바스크립트의 동작 방식에 대해 이해하고 이를 활용 해야한다.

하위타입 생성자에서 call(), apply()를 사용하면 새로 생성된 객체를 인수로 전달하면 상위타입 생성자를 호출할 수 있다.

``` js
function Rectangle(length, width) {
	this.length = length;
	this.width  width;
}

Rectangle.prototype.getArea = function() {
	return this.length * this.width;
}

function Square(size) { // 이 방법을 사용하면 상속받을 생성자에서 정의한 프로퍼티를 다시 정의하는 것을 방지할 수 있다.
	Rectangle.call(this, size, size);
	//  새 프로퍼티 설정 및 기존 프로퍼티 다시 작성
}

Square.prototype = Object.create(Rectangle.prototype, {
	constructor : {
		configurable: true,
		enumerable: true,
		value: Square,
		writable: true
	}
});
```
