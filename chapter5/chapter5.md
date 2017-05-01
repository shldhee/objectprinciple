# 5장 상속

## prototype chaining과 Object.prototype

자바스크립트 상속 방법
* 프로토타입 체이닝
* 프로토타입 상속

프로토타입에 정의된 프로퍼티는 자동으로 모든 객체에서 사용할 수 있게 되는데 이형태가 상속과 유사

객체 인스턴스는 프로토타입으로 프로퍼티를 상속받는다.

프로토타입 또한 하나의 객체이므로 프로토타입에도 자신만의 프로토타입이 있으며 프로토타입으로부터 프로퍼티를 상속받는다.

이러한 특성을 ***프로토타입 체인***이라고 부른다.

모든 객체는 별도로 지정하지 않ㅇ면 Object를 상속받는다. 엄밀히 말하녀 Object.prototype을 상속 받는다. 객체 리터럴을 사용해 정의된 객체의 \[[Prototype]] 프로퍼티는 모두 Object.prototype을 가리킨다.(prototype 정의시 constructor 할당할때!)

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
