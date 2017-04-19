# 생성자와 프로토타입

```js
    function Person() {
        // 내용
    }

    var person1 = new Person;
    var person2 = new Person;

    console.log(person1 instanceof Person);  // true;
    console.log(person2 instanceof Person);  // true;
```

person1과 person2는 Person 생성자를 사용해 생성되었기 떄문에 이 객체가 Person 타입인지 instanceof를 통해 확인하면 ***true를 반환한다.***

1. 모든 객체 인스턴스에는 자동으로 추가되는 constructor라는 프로퍼티가 있다.
1. 이 프로퍼티는 인스턴스를 생성할 때 사용했던 생성자 함수를 참조
1. 기본 객체(Object생성자 사용해 생성한 객체 인스턴스)의 경우 constructor는 Object를 참조
1. 직접 만든 생성자를 사용해 객체를 생성했다면 constructor는 그 생성자 함수를 가리키게 될 것이다.
1. 다음 코드에선 person1과 person2의 contstructor 프로퍼티는 Person을 가리킨다.

```js
console.log(person1.constructor === Person);    //true
console.log(person2.constructor === Person);    //true
```

객체의 constructor 프로퍼티는 덮어쓸 수 있기 때문에 instanceof 연산자를 사용하는편이 더 좋다.

***생성자 함수의 중요한 기능은 동일한 프로퍼티와 메소드를 가진 객체를 쉽게 대량으로 만들 수 있다는 것이다.***

```js
function Person(name) {
    this.name = name;
    this.sayName = function() {
        console.log(this.name);
    };
}
```

this 객체는 생성자를 호출할 때 new 연산자가 자동으로 만들어내는 값인데 생성자 타입의 인스턴스를 참조한다.(여기서 this는 Person의 인스턴스이다.)

Person생성자를 사용해 객체를 생성하면서 name프로퍼티 초기화 할 수 있다.

```js
var person1 = new Person("LEE");
var person2 = new Person("OH");

console.log(person1.name);  // "LEE"
console.log(person2.name);  // "OH"

person1.sayName();  // "LEE"
person1.sayName();  // "OH"
```

* 생성자 내부에서 명시적으로 return 호출이 가능
* 반환되는 값이 객체값이면 새로 만들어진 객체 인스턴스 대신 return 객체 값 반환
* 반환되는 값이 값이면 새로 생성된 객체가 반환되며 생성자 내부에서 반환한 원시 값은 무시된다.

생성자를 호출할 때는 반드시 new연산자를 사용해야 한다.

그렇지 않으면 객체가 생성되는 대신 전역 컨텍스트 객체(global object)가 의도치 않게 수정되는 일이 발생한다.

```js
var person1 = Person("LEE");

console.log(person1 instanceof Person); // false
console.log(typeof person1); // undefined
console.log(name); // "LEE"
```

* Person을 new 연산자 없이 함수처럼 호출하면 생성자 내부의 this는 전역 this 객체와 같다.
* new가 없으면 Person은 그저 return문이 없는 함수
* Person에 전달된 name 인수의 값을 this.name에 할당하면 실제로는 전역 변수 name에 할당하는 꼴이다.

***new 연산자 없이 생상자 함수를 호출하면 엄격한 모드("use strict")에서는 this가 전역 객체를 참조할 수 없기 때문에 에러가 발생한다.***

생성자를 사용하면 앞에 본 예제를 보면 sayName() 메소드의 내용이 완전히 똑같은데 각 인스턴스마다 자신만의 sayName() 메소드를 가지고 있다.
이 보다 모든 인스턴스가 한 메소드를 공유하도록 만드는 것이 훨씬 효율적이다.
메소드 안에서는 this.name을 사용해 적절한 데이터에 접근하도록 만들면 된다.
이를 위해 등장한 것이 **프로토타입(prototype)**이다.

## 프로토타입

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

book 변수에는 hasOwnProperty()가 정의되어 있지 않지만 Object.prorotype에 정의 되어 있기 때문에 book.hasOwnProperty()를 통해 접근할 수 있따.
in 연산자는 프로토타입, 고유 프로퍼티 중 무엇에든 true를 반환

### 프로토토아비 프로퍼티 확인

```js
function hasPrototypeProperty(object, name) {
    return name in object && !object.hasOwnProperty(name); // hasOwnProperty 생성한 객체에 추가한 프로퍼티 찾기
}

console.log(hasPrototypeProperty(book, "title"));   // false
console.log(hasPrototypeProperty(book, "hasOwnProperty"));   // true

```

1. in 연산자를 이용해 객체에 프로퍼티가 있는지 확인(true반환)
1. hasOwnProperty()를 사용해 false를 반환하면 이 프로퍼티는 프로토타입 프로퍼티로 볼 수 있다.