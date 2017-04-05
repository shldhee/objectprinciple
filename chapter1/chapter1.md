# chapter1 원시 타입과 참조 타입

단순한 데이터 저장하는 **원시타입**
객체로서 저장되는 **참조 타입**(사실 메모리상의 주소를 가리킴)

자바스크립트에서는 **변수 객체(variable object)**의 스코프를 따라 변수를 추적한다.
원시 타입의 원시 값은 바로 변수 객체에 저장되지만 참조 타입에서 변수 객체에 저장되는 참조값은 메모리에 있는 실제 객체를 가리키는 포인터(pointer)이다.

### 원시타입

* Boolean : true 또는 false
* Number : 정수 또는 부동소수점 실수 값
* String : 한 쌍의 작은 따옴표 또는 큰 따옴표로 감싼 일련의 문자(자바스크립트에는 문자 타입이 따로 없다.)
* Null : null이라는 값만 있는 원시 타입
* Undefined : Undefined라는 값만 있는 타입 (undefined는 아무런 값도 할당되지 않은 변수에 할당되는 값이다.)

모든 원시 타입에는 값을 표현하는 리터럴(literal) 형식이 있다.
**리터럴(literal)**이란 코드에 직접 입력된 이름이나 가격처럼 변수에 저장되지 않은 값을 의미한다.

````
// 문자열
var name = "duckhee";
var selection = "a";

// 숫자
var count = 25;
var cost = 1.51;

// 불린
var found = true;

// null
var object = null;

// undefined
var flag = undefined;
var ref; // 자동으로 undefined 할당
````

원시 값을 변수에 할당하면 값이 변수로 복사된다.
다시 말해 등호 기호를 사용해 어떤 변수를 다른 변수에 할당하면 두 변수 모두 자신만의 데이터를 가지게 된다는 뜻이다.

````
var color1 = "red";
var color2 = color1;
````

color1 변수의 값을 바꿔도 color2에는 아무런 영향이 없다.

###### 변수객체
|변수 이름| 변수 값 |
|-|-|
| color1 | "red" |
| color2 | "red" |

````
var color1 = "red";
var color2 = color1;

console.log(color1); // "red"
console.log(color2); // "red"

color1 = "blue";

console.log(color1); // "blue"
console.log(color2); // "red"
````
---
#### 원시 타입 종류 확인
원시 타입 종류는 typeof 연산자를 사용해 확인 가능
**typeof 연산자**를 변수에 사용하면 변수에 저장된 데이터의 타입을 문자열로 반환한다.

````
typeof "duckhee"; // "string"
typeof 10 // "number"
typeof 1.2 // "number"
typeof false // "boolean"
typeof undefined // "undefined"
````

__*null은 조금 다르다.*__

typeof null; // "object"

자바스크립트 특이점(실수)
이 같은 특성 떄문에 null인지 아닌지를 확인할 때는 다음과 같이 null과 직접 비교하는 것이 좋다.

`console.log(value === null);`

---
#### 원시 메소드
문자열, 숫자, 불린에는 메소드가 존재(null, undefined 타입에는 메소드가 없다.)

````
var name = "Duckhee";
var lowercaseName = name.toLowerCase(); // 소문자로 변환.
var firstLetter = name.charAt(0); // 첫 번째 문자 가져오기
var middleOfName = name.substring(2, 5); // 두번째부터 네번째 문자까지 가져오기 ("ckh")

var count = 10;
var fixedCount = count.toFixed(2); // "10.00" 으로 변환
var hexCount = count.toString(16); // "a"로 변환

var flag = true;
var stringFlag = flag.toString(); // "true"으로 반환
````

---
### 참조타입
참조 타입은 자바스크립트 객체를 나타내며 클래스가 없는 자바스크립트라는 언어에서 클래스와 가장 가까운 개념이다.
참조 값은 참조 타입의 **인스턴스(instance)**이며 객체와 같은 말이다.
객체는 순서가 없는 **프로퍼티(property)**로 이루어져 있으며 프로퍼티는 이름(문자열)과 값으로 구성되어있다.
프로퍼티의 값이 함수일 떄 이 프로퍼티를 가리켜 **메소드(method)**라고 부른다.

---
#### 객체 생성

###### 객체
|객체 이름| 객체 값 |
|-|-|
| 이름 | 값 |
| 이름 | 값 |

객체를 만드는 것을 가리켜 **인스턴스화(instantiate)**한다고 하는데 자바스크립트에서 인스턴스화 방법은 두 가지가 있다.


* new 연사자와 **생성자(constructor)**를 사용
(생성자는 사실 new 연산자와 함께 사용하여 객체를 만들 수 있는 함수일 뿐이다. 어떤 함수든 생성자가 될 수 있다. 관례상 자바스크립트에서 생성자 함수에 첫 글자를 대문자로 시작하여 일반 함수와 구분한다.) <br />
다음 코드는 일반 객체(generic object)를 하나 작성하고 객체의 참조를 object 변수에 저장한다.
`var object = new Object();`<br />
참조 타입은 할당된 변수에 값을 직접 저장하지 않으므로 이 예제에서 object 변수에 저장된 값은 사실 객체 인스턴스가 아니라 __*객체가 있는 메모리상의 위치를 가리키는 포인터(또는 참조)이다.*__ 이것이 객체와 원시 값의 큰 차이점인데 원시 값은 변수에 직접 값 자체가 저장된다.<br />
객체를 변수에 할당하는 일은 사실 객체의 포인터를 할당한 셈이다.
어떤 변수를 다른 변수에 할당하면 두 변수는 모두 포인터의 복사본만 저장하고 있으므로 메모리상에서 똑같은 객체를 참조하게 된다.
    ````
    var object1 = new Object();
    var object2 = object1;
    ````
    위 코드는 먼저
    1. 새로운 객체 생성(new 연산자 사용)
    2. object1 변수에 객체의 참조를 저장
    3. object2에 object1의 값을 할당

    이를 통해 두 변수 모두 첫 번째 줄에서 생성한 객체의 인스턴스를 가리키게 된다.

---
#### 객체 참조 제거
자바스크립트는 **가바지 컬렉션(garbage collection)** 기능이 있는 언어이므로 참조 타입을 사용할 떄 메모리 할당에 대해 고민하지 않아도 된다.
하지만 가비지 컬렉터가 메모리를 해제할 수 있도록 사용하지 않는 객체에 대해서 **참조 제거(dereference)**를 해두는 편이 좋다.
참조 제거를 할 때에는 객체 변수에 null을 할당하는 방식이 가장 좋다.

````
var object1 = new Object();

// 실행할 코드

object1 = null; // 참조 제거
````

이 코드는 object1 변수를 만들고 사용하다가 변수의 값에 null을 할당했다.
가비지 컬렉터는 메모리상의 객체 중 전혀 참조되어 있지 않은 것이 있으면 다른 용도로 메모를 사용할 수 있도록 객체가 차지하고 있는 메모리를 없앤다.

---
#### 프로퍼티 추가 및 제거
자바스크립트는 언제나 프로퍼티를 추가 또는 제거 할 수 있다.
````
var object1 = new Object();
var object2 = object1;

object1.myCustomProperty = "good";
console.log(object2.myCustomProperty);
````
이 코드에서 myCustomProperty는 object1에 "good"이라는 값과 함께 추가 되었다.
object1과 object2는 같은 객체를 가리키고 있으므로 이 프로퍼티는 object2에서도 접근이 가능하다.

---
### 내장 타입 인스턴스화
자바스크립트에는 Object 타입 외에도 언어에서 기본적으로 제공하는 유용한 내장 참조 타입이 몇 가지 더 있다. 다른 내장 타입은 조금 더 특수한 용도로 사용 할 수 있으며 언제든 인스턴스로 만들 수 있다.

* Array : 숫자 인덱스 값을 가진 순차 목록
* Date : 날짜와 시간
* Error : 실행 중 발생하는 에러 (에러의 종류에 따라 더 구체적인 하위 에러 타입이 존재한다.)
* Function : 함수
* Object :일반적인 객체
* RegExp : 정규 표현식

내장 참조 타입은 다음과 같이 new 연산자를 사용해 인스턴스로 만들 수 있다.
````
var items = new Array();
var now = new Date();
var error = new Error("에러 발생");
var func = new Function("console.log('Hi');");
var object = new Object();
var re = new RegExp("\\d+");
````

---
#### 리터럴 형식
일부 내장 참조 타입은 리터럴 형식도 사용할 수 있다.
리터럴(literal)은 new 연산자와 생성자를 사용하여 명시적으로 객체를 만들지 않고도 참조 값을 만들 수 있는 문법이다.

---
#### 객체 및 배열 리터럴
객체는 중괄호({}) 안에 새로 만들 객체의 프로퍼티를 정의하는 **객체 리터럴(object literal)** 문법을 사용해 만들 수 있다. 프로퍼티는 이름, 콜론, 값으로 이루어지며 프로퍼티 사이는 쉼표로 구분한다.
````
var book = {
    name: "객체지향 자바스크립트의 원리",
    year: 2014
};
````

프로퍼티 이름에는 문자열 리터럴도 사용할 수 있는데 문자열 리터럴을 사용하면 공백과 같은 특수 문자도 프로퍼티 이름에 포함할 수 있다.
````
var book = {
    "name": "객체지향 자바스크립트의 원리",
    "year": 2014
};
````

앞에 두 예제는 논리적으로 다음 코드와 같다.
````
var book = new Object();
book.name = "객체지향 자바스크립트의 원리";
book.year = 2014;
````

_객체 리터럴을 사용하면 실제로도 new Object()를 호출하지 않는다._

**배열 리터럴(array literal)**을 정의하는 방법도 비슷하다. 각 괄호 안에 여러 값을 추가하는데 각 값은 쉼표로 구분한다.

````
var colors = ["red", "blue", "green"];
console.log(colors[0]); // "red"
````
이 코드는 다음 코드와 동일하다.
````
var colors = new Array("red", "blue", "green");
console.log(colors[0]); // "red"
````

---
#### 함수 리터럴
함수는 보통 리터럴 형식을 사용해 정의된다.
코드 문자열은 실제 코드에 비해 유지보수, 가독성, 디버그 등 여러 면에서 불리하다.
따라서 Fucntion 생성자보단 리터럴 형식을 많이 사용한다.

````
function reflect(value) {
    return value;
}

// 위코드는 다음과 같다.

var reflect = new Function("value", "return value;");
````

이 코드는 reflect()를 함수를 정의하고 이 함수는 전달받은 값을 그대로 반환한다.

---
#### 정규 표현식 리터럴
자바스크립트에는 RegExp 생성자를 사용하지 않고 정규 표현식을 정의할 수 있는 **정규 표현식 리터럴(regular expression literal)**도 있다.
슬래시 두 개 사이에 패턴을 입력하고 추가 설정은 두 번째 슬래시 뒤에 문자 한개로 표현한다.

````
var numbers = /\d+/g;

// 위 코드는 아래 코드와 같다.

var numbers = new RegExp("\\d+", "g");
````

RegExp 생성자를 사용하면 패턴을 문자열 인수로 전달해야 하기 때문에 백슬래시도 이스케이프 해주어야 한다.(이 때문에 리터럴에서는 \d를 사용하지만 생성자에서는 \\d를 사용한다.)
기본적으로 정규표현식 리터럴을 많이 사용하지만 문자열 여러개를 합쳐서 정규 표현식을 만들때는 생성자 형식이 더 즐겨 사용된다.

_Function을 제외하면 내장 타입을 인스턴스로 만드는 방법에는 옳고 그름이 없다._

---
### 프로퍼티 접근
프로퍼티는 객체에 저장된 이름/값 쌍이다.
객체의 프로퍼티에 접근할 떄에는 점(dot) 표기법을 주로 사용하지만 각괄호 표기법([])과 문자열을 사용해서 접근할 수도 있다.

````
var array = [];
array.push(12345); //  점 표기법
array["push"](12345); // 각 괄호 표기법
````

아래 코드 문법은 접근할 프로퍼티를 동적으로 정해야 할 때 유용하다.
````
var array = [];
var method = "push";
array[method](12345);
````
여기서 변수 method의 값이 "push"이므로 배열의 push() 메소드가 호출된다.

점 표기법과 달리 각괄호 표기법에서는 프로퍼티 이름에 특수 문자를 사용할 수 있다는 점이 유일한 차이점이다.

---
### 참조 타입 확인
함수는 typeof 연산자를 사용하면 "function"이라는 문자열이 반환되므로 가장 확인하기 쉬운 참조 타입이다.
````
function reflect(value) {
    return value;
}

console.log(typeof reflect); // "function"
````
다른 참조 타입은 typeof 연산자를 사용하면 "object"만 반환한다.
이 떄 자바스크립트의 instanceof 연산자를 사용하면 참조 타입을 더 쉽게 확인할 수 있다.

instanceof 연산자는 객체, 생성자와 함께 사용된다. 사용한 객체가 사용한 생성자의 인스턴스라면 instanceof 연산자는 true를 반환하고 그렇지 않으면 false를 반환한다.

````
var items = [];
var obj = {};

function reflect(value) {
    return reflect;
}

console.log(items instanceof Array); // true
console.log(obj instanceof Object); // true
console.log(reflect instanceof Function); // true
````
이 예제에서는 여러 값을 instanceof 연산자 및 생성자와 함께 사용했다.
각 참조타입에 instanceof 연산자 및 참조 타입을 나타내는 생성자를 사용하면 인스턴스의 정확한 종류를 알 수 있다.(변수를 만들 때 생성자가 사용되지 않았어도 가능하다.)

instanceof 연산자는 상속된 타입도 확인할 수 있다.
자바스크립트에서 모든 참조 타입은 Object를 상속하므로 사실 모든 객체는 Object의 인스턴스이다.

````
var items = [];
var obj = {};

function reflect(value) {
    return reflect;
}

console.log(items instanceof Array); // true
console.log(items instanceof Object); // true
console.log(obj instanceof Object); // true
console.log(obj instanceof Array); // false
console.log(reflect instanceof Function); // true
console.log(reflect instanceof Object); // true
````

_**각 참조 타입은 모든 참조 타입이 상속한 Object의 인스턴스로 확인되었다.**_
