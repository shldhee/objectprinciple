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