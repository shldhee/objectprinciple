#### 프로퍼티 속성

ECMAScript 5전에는 프로퍼티의 내부 속성에 접근할 방법이 없었다.
5에서 프로퍼티 속성을 바로 다룰 수 있는 방법이 몇 가지 추가되었고 여러 기능을 지원할 수 있는 속성도 추가 되었다.

이제 자바스크립트의 네이티브 프로퍼티와 똑같이 동작하는 프로퍼티도 작성할 수 있다.

데이터 프로퍼티와 접근자 프로퍼티의 속성에 대해 자세히 알아보고 두 프로퍼티가 공통적으로 가진 속성에 대해 살펴보자.

---
##### 공통속성

데이트 프로퍼티와 접근자 프로퍼티의 공통 속성은 두 가지이다.
* \[[Enumerable]] 열거 가능한지 정하는 속성
* \[[Configurable]] 속성이 있는 설정 가능 프로퍼티(configurable property)
    delete 연산자를 사용해 언제든 제거할 수 있고 프로퍼티의 속성도 언제든 변경할 수 있다.
    다시 말해 데이트 프로퍼티를 접근자 프로퍼티로 만들 수 있고 접근자 프로퍼티를 데이터 프로퍼티로 만들 수 있다.

    모든 객체는 따로 설정하지 않은 한 열거 가능하고 설정 가능하다.

프로퍼티 속성 변경시에는 Object.defineProperty() 메소드를 사용한다.
3개의 인수 전달
1. 첫 번째 인수는 프로퍼티를 소유하고 있는 객체
2. 두 번째 인수는 프로퍼티 이름
3. 세 번째 인수는 설정할 프로퍼티 속성 값을 포함하고 있는 프로퍼티 서술사(property descriptor) 객체

서술자 객체에는 설정할 내부 속성의 이름을 각괄호 없이 사용하면 된다.

```js

var person1 = {
    name: "LEE"
};

Object.defineProperty(person1, "name", {
        enumerable: false
    });

console.log("name" in person1); // true
console.log(person1.propertyIsEnumerable("name")); //false

var properties = Object.keys(person1);
console.log(properties.length); // 0


Object.defineProperty(person1, "name", {
        configurable: false
    });

// 프로퍼티 제거 시도
delete person1.name;
console.log("name" in person1);  // true
console.log(person1.name); // "LEE"

Object.defineProperty(person1, "name", {
    configurable: true
    });
```

\[[enumerbale]] 속성을 이용 false 설정 시 Object.keys로 배열로 만들어도 length에 포함 안됨
\[[Configurable]] 속성을 false로 설정하면 Object.definedProperty 로도 속성을 변경할 수 없다.

따라서 name은 person1의 프로퍼티로 사실상 고정되어버린 셈이다.

---
#### 데이터 프로퍼티 속성

데이터 프로퍼티에는 접근자 프로퍼티에 없는 두 종류의 속성이 있다.
* \[[Value]] : 프로퍼티의 값을 저장. 객체에 프로퍼티를 만들면 자동으로 이 속성에 값이 저장된다.
* \[[Writable]] 프로퍼티에 값을 저장할 수 있는 정의하는 불리언 값 설정하지 않으면 모든 프로퍼티는 프로퍼티에 값을 저장 할 수 있도록 설정된다.

위에 2개 속성과 Object.defineProperty()를 사용하면 데이터 프로퍼티를 정의할 수 있다.
이 방식은 프로퍼티가 실제로 존재하지 않은 경우에도 사용할 수 있다.
```

var person1 = {
    name: "LEE"
};

// 위 코드는 person1이라는 새로운 객체를 만들면서 name 프로퍼티를 정의하고 프로퍼티의 값을 설정한다.


var person1 = {};

Object.defineProperty(person1,"name", {
        value: "LEE",
        enumerable: true,
        configurable: true,
        writable: true
    });

// 첫번째 코드와 실행 결과가 똑같지만 조금 더 복잡해 보이는 코드이다.
```

* Object.defineProperty()를 실행하면 해당 프로퍼티가 있는지 확인
* 프로퍼티가 없으면 새 프로퍼티를 추가하고 서술 객체에서 정의한 대로 속성 설정
* 이 예제는 name은 person1의 프로퍼티가 아니었기 때문에 새로 작성

*Object.definedProperty()*를 사용해 새 프로퍼티를 정의 할 때 서술자에 없는 속성은 모두 false로 설정된다.
따라서 필요한 속성은 반드시 서술자에 포함시켜라.

다음 예제는 Object.defineProperty()를 호출할 때 명시적으로 true라고 설정한 속성이 없는 예제

```js

var person1 = {};

Object.defineProperty(person1, "name", {
    value: "LEE"
});

console.log("name" in person1); // true
console.log(person1.propertyIsEnumerable("name")); // false; 서술자에 없다.

delete person1.name; // 지워지지 않는다 configurable이 false이므로
console.log("name" in person1);

person1.name = "Oh";
console.log(person1.name); // "Oh"

```

위 코드는 name 프로퍼티는 값을 읽어는 것만 할 수 있다. 다른 작업들은 모두 차단
이미 존재하는 프로퍼티는 미리 수정할 수 있도록 설정해 둔 프로퍼티만 바꿀 수 있다.(configurable true)

---
#### 접근자 프로퍼티 속성

접근자 프로퍼티에만 필요한 속성도 두 가지가 있다.
접근자 프로퍼티는 저장할 값이 없으므로 \[[Value]]나 \[[Writable]] 속성은 필요 없는 대신
**각각 게터 함수와 세터 함수를 나타내는 \[[Get]]과 \[[Set]] 속성이 필요하다.** 두 속성 중 하나만 정의해도 상관 없다.
단, *한 프로퍼티에 데이터 프로퍼티 속성과 접근자 프로퍼티 속성을 둘 다 설정하려고 하면 에러가 발생한다.*

접근자 프로퍼티는 객체 리터럴 형식 대신 **접근자 프로퍼티 속성**을 사용하면 기존에 있던 객체에도 프로퍼티를 추가할 수 있다는 장점이 있다.

접근자 프로퍼티를 정의할 때 객체 리터럴 형식을 사용하려면 객체를 생성할 때 프로퍼티도 같이 만드는 수 밖에 없다.

데이터 프로퍼티와 마찬가지로 접근자 프로퍼티도 설정 가능 여부와 열거 가능 여부 설정이 가능하다.

```js
var person1 = {
	_name: 'LEE',

	get name() {
		console.log('name 읽는중', name 읽는중);
		return this._name;
	},

	set name() {
		console.log('name의 값을 %s로 설정하는중', value);
		this._name = value;;
	}
};
```
위 코드를 아래와 같이 작성할 수 있다.
```js
var person1 = {
	_name: 'LEE'
};

Object.defineProperty(person1, "name", {
	get: function() {
		console.log("name 읽는중");
		return this._name;
	},
	set: function() {
		consle.log("name의 값을 %s로 설정하는 중", value);
		this._name = value;
	},
	enumerable: true,
	configurable: true
});
```

**Object.defineProperty()에 전달된 객체에 있는 get키와 set키는 값이 함수인 데이터 프로퍼티이다.**
이 객체에서는 리터럴 형태의 접근자를 사용할 수 없다.

접근자 프로퍼티도 데이터 프로퍼티처럼 \[[Enumerable]]이나 \[[Configurable]]과 같은 속성을 설정할 수 있다.

---
#### 여러 프로퍼티 정의하기
**`Object.defineProperties()`**를 사용하면 동시에 여러 프로퍼티를 설정할 수 있다.
* 첫 번째 인수는 대상 객체(프로퍼티를 추가할 객체)
* 두 번재 인수는 정의할 프로퍼티의 정보를 포함하고 있는 객체

```js
var person1 = {};

Object.definedProperties(person1, {
	//데이터를 정의할 프로퍼티
	_name: {
		value: "LEE",
		enumerbale: true,
		configurable: true,
		writable:  true
	},

	//접근자 프로퍼티
	name: {
		get: function() {
			console.log("name 읽는중");
			return this._name;
		},
		set: function(value) {
			console.log("name의 값을 %s로 설정하는중", value);
			this._name = value;
		},
		enumerable: true,
		configurable: true
	}
});
```
_name이라는 데이터 프로퍼티와 name이라는 접근자 프로퍼티를 정의한다.

---
프로퍼티 속성 가져오기
프로퍼티 속성을 가져오고 싶을 때는 **`Object.getOwnPropertyDescriptor()`**메소드를 사용한다.
* 고유 프로퍼티에만 사용 가능
* 이 메소드에 전달하는 인수는 2개
* 첫 번째 인수는 대상 객체
* 두 번째 인수는 정보를 가져올 프로퍼티의 이름
* 인수로 이름을 전달한 프로퍼티가 존재하면 프로퍼티의 속성저보를 포함한 객체가 반환된다.
* 이 객체에는 configurable, enumerable을 비롯한 네 종류의 키가 설정
* 나머지 두개는 프로퍼티 종류에 따라 달라짐

```js
var person1 = {
	name: "LEE"
};

var descriptor = Object.getOwnPropertyDescriptor(person1, "name");

console.log(descriptor.enumerable);  // true
console.log(descriptor.configurable); // true
console.log(descriptor.writable); // true
console.log(descriptor.value); //"LEE"

descriptor
//Object {value: "LEE", writable: true, enumerable: true, configurable: true}
```

---
#### 객체 수정 방지

객체에도 프로퍼티와 마찬가지로 객체의 동작을 제어하는 내부 속성이 있다. 속성 중 하나인 \[[Extensible]]은 객체 자체의 수정 가능 여부를 가리키는 불리언 값이다.

기본적으로 모든 객체는 이 속성이 켜져 있어 확장 가능한 상태이다. 이 속성을 false로 설정하면 수정 할 수 없다.
객체를 수정하지 못하는 방법은 3가지가 있다.

* ##### 확장방지
  Object.preventExtensions()을 사용하면 확장 불가능한 객체를 만들 수 있다.
  * 첫 번째 인수는 확장 불가능하게 만들 객체

  Object.isExtensible()을 사용하면 \[[Extensible]]의 값을 확인할 수있다.
```js
var person1 = {
	name: "LEE"
};

console.log(Object.isExtensible(person1)); // true
Object.preventExtenstion(person1);
console.log(Object.isExtensible(person1)); // false
person1.sayName = function() {
	console.log(this.name);
};

console.log("sayName" in person1); // false
```
엄격하지 않은 모드에선 확장 불가능한 객체에 프로퍼티를 추가하려고 해도 에러가 발생하지 않으므로 **엄격한 모드**에서 사용하는것이 좋다.

---
#### 객체봉인
확장 불가능한 객체를 만드는 두 번째 방법은 객체를 봉인(seal)하는 것이다.
봉인된 객체는 확장 불가능하며 이 객체의 모든 프로퍼티는 설정 불가능한 상태가 된다.
즉 읽고 쓰는 것만 가능하다.

객체 봉인시 **`Object.seal()`**메소드를 원하는 객체에 사용하면 된다.
이 메소드 실행시 \[[Extensible]]속성이 false로 설정되고
모든 프로퍼티는 \[[Configurable]]속성이 false로 설정된다.
봉인 확인은 Object.isSealed()를 사용한다.

```js
var person1 = {
	name: "LEE"
};

console.log(Object.isExtensible(person1)); // true
console.log(Object.isSealed(person1)); // false

Object.seal(person1);
console.log(Object.isExtensible(person1)); // false
console.log(Object.isSealed(person1)); // true

person1.sayName = function() {
	console.log(this.name);
};

console.log("sayName" in person1); // false

person1.name = "Oh"; // 쓰는거
console.log(person1.name); // "Oh"  읽고

delete person1.name;
console.log("name" in person1); // true
console.log(person1.name); // "Oh"

var descriptor = Object.getOwnPropertyDescriptor(person1, "name");
console.og(descriptor.configurable); // false
```
perons1 객체를 봉인해서 프로퍼티를 추가하거나 제거할 수 없게 만든다.
**객체를 잘못 사용했을 때 알아챌 수 있도록 봉인된 객체를 다룰 떄는 엄격한 모드를 사용하는 것이 좋다.**

---
#### 객체 동결
확장 불가능한 객체를 만드는 마지막 방법은 객체를 **동결(freeze)**시키는 것이다.
동결된 객체는 프로퍼티를 추가, 삭제, 저장이 불가능하다.
즉, 읽기 전용이다.
동결된 객체는 다시 해제할 수 없다.
객체를 동결 시킬 때는 Object.freeze()로, 동결되었는지 확인은 Object.isFrozen()을 사용

```js
var person1 = {
	name: "LEE"
};

console.log(Object.isExtensible(person1)); // true
console.log(Object.isSealed(person1)); // false
console.log(Object.isFrozen(person1)); // false

Object.freeze(person1);
console.log(Object.isExtensible(person1)); // false
console.log(Object.isSealed(person1)); // true
console.log(Object.isFrozen(person1)); // true

person1.sayName = function() {  // 프로퍼티 추가
	console.log(this.name);
}

console.log("sayName" in person1); // false 프로퍼티 추가 안됨

person1.name = "Oh"; // 프로퍼티 저장 쓰기
console.log(person1.name); // "LEE" 저장 쓰기 안됨

delete person1.name; // 프로퍼티 제거
console.log("name" in person1); // true 프로퍼티 제거 안됨
console.log(person1.name); // "LEE"

var descriptor = Object.getOwnPropertyDescriptor(person1, "name");
console.log(descriptor.configurable); // false
console.log(descriptor.writable); // false
```

person1은 동결된 객체이다.
