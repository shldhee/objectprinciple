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

```

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

```

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
