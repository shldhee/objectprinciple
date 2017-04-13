# 객체의 이해

### 프로퍼티 정의

#### 객체 생성 방법
* Object 생성자 이용
* 객체 리터럴 사용

```
var person1 = {
    name: "LEE"
};

var person2 = new Object();
person2.name = "LEE";

person1.age = "Redatced";
person2.age = "Redatced";

person1.name = "DUCKHEE";
perons1.name = "BINHEE";
```

person1과 person2에는 둘 다 name이라는 프로퍼티가 있다.
두 객체에 age라는 프로퍼티를 추가(이 작업은 객체를 정의한 직후나, 한참이 지난 뒤에 언제든 할 수 있다. *객체를 일부러 막지 않은 이상*)
마지막 부분에서는 각 객체 name 프로퍼티 값을 수정했다.

자바스크립트는 프로퍼티를 처음 객체에 추가할 때 객체에 있는 **\[[Put]]이라는 내부 메소드를 호출**한다.
**\[[Put]]**메소드는 저장 공간을 생성한다. 이 동작은 수행하면 초기값과 물론 프로퍼티의 속성도 설정한다.
따라서 name, age 프로퍼티 처음 정의시 **\[[Put]]** 내부 메소드가 호출된다.

**\[[Put]]**을 호출하면 객체에 ***고유 프로퍼티(own property)가 만들어진다.
고유 프로퍼티는 객체의 특정 인스턴스에 속해있으며 인스턴스에 바로 저장된다. 또한 프로퍼티에 동작을 수행하려면 소유 객체를 거쳐야 한다.

기존 프로터에 새 값을 할당할 때는 **\[[Set]]**이 호출된다.
**\[[Set]]**은 프로퍼티의 현재 값을 새 값으로 교체한다.

앞 코드에서 두 번째로 name에 값을 할당할 때 **\[[Set]]**이 호출된다.

---
### 프로퍼티 탐지

프로퍼티는 언제든 추가할 수 있기 때문에 객체에 프로퍼티가 있는지 확인할 때도 있다. 자바스크립트에 익숙하지 않은 개발자들은 다음과 같이 객체 프로퍼티를 확인한다.

```
// 쓰지 말자 정확하지 않다.
if (person1.age) {
    // age를 사용해 뭔가 한다.
}
```
이 방식의 문제점은 **자바스크립트의 타입 강제변환(coercion)이 결과에 양항을 끼친다.**

#### if 조건문은 주어진 값이 참스러운 값(trurhty value)이면 true로 취급
* 객체
* 비어있지 않은 문자열
* 0이 아닌 숫자
* true

#### 주어진 값이 거짓스러운 값(falsy value)이면 false로 취급
* null
* undefined
* 0
* false
* NaN
* 빈 문자열

예를 들어 person1.age의 값이 0이라면 프로퍼티가 있어도 if 조건문은 *거짓스러운 값 false로 취급*하여 실행되지 않을 것이다.

따라서 객체 프로퍼티의 존재 여부를 확인하려면 in 연산자를 사용하는 것이 좋다.
in 연산자는 특정 객체에 주어진 이름의 프로퍼티가 존재하는 확인하고 존재하면 true를 반환한다.
```
console.log("name" in person1);
console.log("age" in person1);
console.log("title" in person1);
```

메소드의 존재 여부도 같은 방식으로 확인이 가능하다.
```
var person1 = {
    name: "LEE",
    sayName: function() {
        console.log(this.name);
    }
};

console.log("sayName" in person1);
```

**in 연산자는 프로퍼티의 값을 확인하지 않는 이점이 있다.**
 프로퍼티의 값을 확인하는 과정에서 성능 문제가 생기거나 에러가 발생할 수 있다.

###### 때로는 프로퍼티의 존재 여부를 확인하는 것에 그치지 않고 해당 프로퍼티가 객체의 고유프로퍼티인지도 확인해야 한다.

고유프로퍼티 (현재 만든 객체에만 존재)

```
var person1 = {
    name: "LEE",
    sayName: function() {
        console.log("name :",this.name);
    }
};

console.log(person1.sayName());

console.log("name" in person1); // true;
console.log(person1.hasOwnProperty("name")); // true;

console.log("toString" in person1); / true;
console.log(person1.hasOwnProperty("toString")); // false
```

name은 고유프로퍼티이므로 hasOwnProperty()는 true 반환
toString은 프로토타입 프로퍼티이므로 false를 반환한다.

---
#### 프로퍼티 제거

객체는 언제든 프로퍼티를 추가할 수 있고 언제든 삭제가 가능하다.
프로퍼티의 값을 null로 바꾸는 것만으로 프로퍼티가 객체에서 완전히 삭제 되지 않는다.
값을 null로 바꾸는건 \[[Set]] 내부 메소드를 null과 함께 실행하여 값만 변환된다.
객체에서 프로퍼티 값을 삭제하려면 delete 연산자를 사용해야 한다.

delete 연산자를 사용시 \[[Delete]] 내부 메소드가 호출된다.
deleted연산자는 무사히 실행을 마쳤을 때 true를 반환한다.

```
var person1 = {
    name: "LEE"
};

console.log("name" in person1); // true

delete person1.name; // true - 출력되지는 않음

console.log("name" in person1); // false
console.log(person1.name); // undefined

```

---
#### 열거

객체에 추가하는 프로퍼티는 기본적으로 **열거(enumerable)**가 가능하다.
즉 for-in 반목문을 사용해 훑을 수 있다. 열거 가능 프로퍼티에는 \[[Enumerbale]]이라는 내부 속성이 true로 설정되어 있다.
for-in 반복문을 실행하면 객체에 있는 프로퍼티 중 열거 가능한 것을 훑는데 이때 프로퍼티의 이름을 변수에 할당한다.

```
var property;

for (property in object) {
    console.log("이름 : " + property);
    console.log("값: " + object[property]);
}
```

위 예제는 각괄호 표기법을 사용해야 할 대표적인 사례이다.

객체 프로퍼티의 목록을 가져와야 한다면 ECMASCript 5에서 도입된 Object.keys() 메소드가 유용하다. 이 메소드를 실행하면 다음에서 보듯 열거 가능한 객체의 이름을 배열로 구성하여 반환한다.

```

var properties = Object.keys(object);

var i, len;

for ( i = 0, len = properties.length; i < len; i+=1 ) {
    console.log("name:" , properties[i]);
    console.log("value", object(properties[i]));
}

```

일반적으로 프로퍼티 이름 목록을 배열 형태로 다루고 싶을때는 Object.keys()를 사용하고
굳이 배열이 필요 없을 때는 for-in를 사용한다.

for-in은 열거 가능한 프로토타입도 반환한다.
Object.keys()는 고유 프로퍼티만 반환한다.
4장에서 알아보자

모든 프로퍼티를 열거할 수 있는 것은 아니다.
사실 객체의 네이티브 메소드는 대부분 \[[Enumerbale]] 속성이 false로 설정되어 있다.

프로퍼티가 열거 가능한지 확인하는 메소드는 propertyIsEnumerable()를 사용한다.
이 메소드는 모든 객체에 포함되어 있다.

```

var person1 = {
    name: "LEE"
};

console.log("name" in person1); // true
console.log(person1.propertyIsEnumerable("name")); // true

var properties = Object.keys(person1);

console.log("length" in properties); // true; 배열로 반환
console.log(properties.propertyIsEnumerable("length")); // false;

```

length 프로퍼티는 Array.prototype의 내장 프로퍼티이므로 열거 가능하지 않다.

---
#### 프로퍼티 종류

프로퍼티는 **데이터 프로퍼티**와 **접근자 프로퍼티**로 구분한다.
데이터 프로퍼티(Data property)는 앞에서 살펴본 name프로퍼티처럼 값을 포함하고 있다.
\[[Put]] 메소드의 기본 동작을 통해 생성된다.
접근자 프로퍼티(accessor property)는 값을 포함하지 않는 대신 프로퍼티를 읽었을 때 호출할 함수(게터 getter)와 값을 설정할 때 호출함 함수(세터 setter)를 정의한다.

접근파 프로퍼티는 1개만 정의할 수도 있고 2개 다 사용할 수 있다.

```

var person1 = {
    _name: "LEE",

    get name() {
        console.log("name 읽는중");
        return this_name;
    },

    set name(value) {
        console.log("name의 값을 %s로 설정하는 중", value);
        this._name = value;
    }
};

console.log(person1.name); // name 읽는중 출력 후 "LEE" 반환

person1.name = "Oh";

console.log(person1.name); // name 의 값을 블라 블라 출력 후 "Oh" 반환
```

name이라는 접근자 프로퍼티를 정의하고 있다.
접근자 프로퍼티에서 사용하는 실제 값은 _name이라는 데이터 프로퍼티에 저장한다.

name의 게터와 세터를 정의할 때는 function키워드가 없다는 점만 제외하면 함수를 정의하는 문법과 비슷하다.

게터는 값을 반환해야 하고
세터는 프로퍼티에 할당할 값을 인수로 전달 받을 수 있다.

단순히 다른 프로퍼티에 데이터를 저장했다가 가져오는 정도만 수행한다면 대게는 접근자 프로퍼티 대신 해당 프로퍼티에 직접 데이터를 저장한다.
***접근자 프로퍼티는 값을 할당할 때, 어떤 동작을 추가로 더 수행하고자 할떄, 또는 값을 읽을 때 추가적인 계산을 통해 반환 값을 만들어야 할 때 유용하다.***

게터와 세터 둘다 정의할 필요 없으며 둘 중 하나만 정의해도 상관없다.
'use strict' 모드에선 게터만 정의하고 값을 할당할려면 에러가 발생한다. (세터도 마찬가지)
