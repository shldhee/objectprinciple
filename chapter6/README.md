# 6장 객체 패턴

> 자바스크립트에서는 여러 패턴으로 객체를 생성할 수 있으며 대개 같은 작업도 여러 가지 방식으로 수행할 수 있다. 자신만의 타입을 작성하거나 원한다면 언제든 일반 객체도 만들 수 있다. 상속이나 믹스인 등의 기술을 사용해 두 객체가 같은 메소드를 공유하다록 만들 수 있다. 또한 객체의 구조가 변경되지 않기를 원한다면 고급 기능을 사용해 변경을 막을 수도 있다.

## 1. 비공개 멤버와 특권 멤버

* 자바스크립트에서 객체 프로터니느 모두 공개(public)되어 있으며 객체 외부에서 특정 프로퍼티에 접근할 수 없도록 설정할 명시적인 방법이 없다. **즉 외부에서 접근 가능**
* 데이터를 공개하지 않아야 할때도 있다. 예를 들어 객체에 어떤 값이 있는데 이 값을 통해 임의의 상태를 결정한다고 생각해보자. 만약 그 값을 객체 모르게 변경한다면 상태 관리는 엉망이 되어버릴 것이다. 이런 문제 피할 방법 중 하나는 *명명 규칙(naming conventions)*을 사용하는 것
  * 프로퍼티 이름 앞에 언더스코어 문자를 접두어로 사용하는 방법 `this._name`


명명 규칙에만 기대지 않고 더 안전하게 비공개 데이터의 수정을 방지할 방법도 몇가지 존재한다.

### 1-1) 모듈 패턴

**모듈 패턴(module pattern)**은 비공개 데이터를 가진 싱글톤(singeton) [^1] 객체를 만들떄 사용하는 객체 생성 패턴이다.

이 방식은 객체를 반환하는 ***즉시 실행 함수 표현식(immdediately invoked function expression, IIFE)을 사용한다.***

* `IIFE`는 함수를 생성하자마자 즉시 호출하여 결과를 내는 함수 표현식으로서 이 함수 바깥에서는 접근할 수 없는 지역 변수를 함수 안에 몇개든 정의할 수 있다.
* 반환된 객체는 이 함수 안에서 정의되었기 떄문에 객체의 메소드는 함수의 지역 변수에 접근할 수 있다.(`IIFE`안에서 정의된 모든 객체는 함수 안의 지역 변수에 접근할 권한을 가진다.)
* 이런 방식을 통해 비공개 데이터에 접근하는 메소드를 가리켜 **특권(privileged)** 메소드라 한다.

다음은 모듈 패턴의 기본 형식이다.

``` js
var yourObject = (function() {
  // 비공개 데이터 변수

  return { // 리턴되어 밖으로 노출
    // 공개 메소드 및 프로퍼티
  };
}());
```

* 이 패턴은 익명 함수가 만들어지는 즉시 실행된다.(함수를 감싼 괄호는 함수의 양 끝에 있어야 한다는 점에 중의하자 이 문법을 사용하면 익명 함수를 즉시 실행 할 수 있다.)
* **즉 익명 함수는 잠깐만 존재했다가 실행되고 사라진다.**

모듈 패턴을 사용하면 평범한 변수를 개체의 비공개 프로퍼티처럼 사용할 수 있는데, 이렇게 쓰려면 객체 메소드를 `클로저(closure)`함수로 작성해야 한다. 클로저는 쉽게 말해 클로저 함수 스코프 바깥의 변수에 접근할 수 있는 함수를 말한다. 예를 들어 함수 안에서 웹 브라우저의 `window`와 같은 전역 객체에 접근하면 이 함수는 함수 스코프 바깥의 변수에 접근한 것이다. 모듈 패턴이 조금 다른 부분은 접근하는 외부 변수가 `IIFE` 안에 정의되어 있으며  `IIFE`안에서 정의된 함수가 이 변수에 접근한다는 것이다. 다음 예제를 보자.

``` js
var person = (function(){
  var age = 25;

  return {
    name: "LEE",

    getAge: function() {
      return age;
    },

    growOlder: function() {
      age++;
    }
  };
}());

console.log(person.name); // "LEE"
console.log(person.getAge()); // 25

person.age = 100;
console.log(person.getAge()); // 25

person.growOlder();
console.log(person.getAge()); // 26
```

이 코드는 모듈 패턴을 사용해 person 객체를 생성했다.

* 이 객체의 `age` 변수는 마치 비공개 프로퍼티처럼 동작한다.
* 객체 외부에서는 `age`변수에 접근할 수 없지만 객체의 메소드에서는 자유롭게 사용할 수 있다.
* 이 객체는 특권메소드가 2개 있는데, 하나는 `age`변수의 값을 읽는 `getAge()`이고 다른 하나는 `age`값을 증가 시키는 `growOlder()`이다.
* `age`변수는 두 메소드가 정의된 외부 함수에 선언되어 있으므로 두 메소드는 모두 `age`변수에 접근할 수 있다.

#### 모듈 노출 패턴(revealing module pattern)

모든 변수와 메소드를 `IIFE` 상단에 정의해두고 반환하는 객체에 정의했떤 변수와 메소드를 할당한다.

``` js
var person = (function() {

  var age = 25;

  function getAge() {
    return age;
  }

  function growOlder() {
    age++;
  }

  return {
    name: "LEE",
    getAge: getAge,
    growOlder: growOlder
  };

}());
```

* 모듈 노출 패턴에서 `age, getAge(), growOlder()`는 모두 `IIFE` 안에 지역 함수로 정의되어 있다.
* 이후 객체를 반활할 때 `getAge(), growOlder()` 함수를 객체에 할당하여, 이 함수들을 `IIFE` 바깥으로 **노출(revealing)**시켰다.
* 이 코드는 일반적인 모듈 패턴을 사용한 예제와 기본적으로 같지만 변수와 함수를 같이 선언한다는 점 때문에 이 패턴을 더 선호하는 사람도 있다.

## 1-2) 생성자의 비공개 멤버

모듈 패턴은 비공개 프로퍼터를 가진 개별 객체를 선언하기에는 좋지만 비공개 프로퍼티가 필요합 타입에도 적용할 수 있을까? 정답은 ***그렇다***이다. 생성자 함수 내부에 모듈 패턴과 비슷한 패턴을 사용하면 인스턴스마다 비공개 데이터를 만들어 줄 수 있다. 다음 예제를 보자.

``` js
function Person(name) {

  // Person 생성자 안에서만 접근할 수 있는 변수 선언
  var age = 25;

  this.name = name;

  this.getAge = function() {
    return age;
  };

  this.growOlder = function() {
    age++;
  };
}

var person = new Person("LEE");

console.log(person.name); // "LEE";
console.log(person.getAge()); // 25

person.age = 100;
console.log(person.getAge()); // 25

person.growOlder();
console.log(person.getAge()); // 26
```

* 이 코드에서 `Person` 생성자에는 `age`라는 지역 변수가 있다.
* 이 변수는 `getAge()와 growOlder()` 메소드에서 사용된다.
* `Person`의 인스턴스를 생성할 때 인스턴스는 자신만의 `age, getAge(), growOlder()`를 만든다.
* 이 방식은 생성자가 지역 범위를 만들고 `this`객체를 반환하는 등 여러면에서 모듈 패턴과 비슷하다.
* 객체 인스턴스의 메소드를 바꾸는 것은 프로토타입에 정의하는 것보단 비효율적이지만, 인스턴스의 비공개 고유 프로퍼티는 이 방법으로만 작성할 수 있다.

프로토타입에 추가한 프로퍼티처럼 모든 인스턴스가 공유하는 비공개 데이터를 만들고 싶다면 모듈 패턴과 비슷하지만 생성자를 이용한 혼합 방식을 사용하면 된다.

``` js
var Person = (function(){

  // everyone shares the same age
  var age = 25;

  function InnerPerson(name) {
    this.name = name;
  }

  InnerPerson.prototype.getAge = function() {
    return age;
  };

  InnerPerson.prototype.growOlder = function() {
    age++;
  }

  return InnerPerson;

}());
var person1 = new Person("LEE");
var person2 = new Person("OH");

console.log(person1.name); // "LEE"
console.log(person1.getAge()); // 25

console.log(person2.name); // "OH"
console.log(person1.getAge()); // 25

person1.growOlder();
console.log(person1.getAge()); // 26
console.log(person2.getAge()); // 26
```

* 이 코드에서 `InnerPerson`생성자는 `IIFE` 안에 정의되어 있다.
* `age`변수는 생성자 바깥에 정의되어 있지만 객체의 프로토타입 메소드에서 이 값을 사용한다.
* `InnerPerson` 생성자는 반환된 후에 전역 범위의 `Person` 생성자가 된다.
* `Person`이 모든 인스턴스는 `age`변수를 공유하므로 한 인스턴스에서 이 값을 변경하면 다른 인스턴스에서도 값이 변경된 효과가 난다.


[^1]: 해당 클래스의 인스턴스가 하나만 만들어지고, 어디서든지 그 인스턴스에 접근할 수 있도록 하기 위한 패턴.
