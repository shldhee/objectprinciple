# chapter2 함수

함수는 객체이다.
다른 객체에는 없는 함수만의 특성을 꼽으라면 \[[Call]\] 이라는 내부 속성을 들 수 있다.
**내부 속성**은 코드로 접근할 수는 없지만 코드의 동자을 정의한다.


### 선언 vs 표현식
함수에는 두 가지 리터럴 형태가 있다.

첫 번째는 function 키워드와 바로 뒤에 적는 함수 이름을 사용하는 **함수 선언(function delcaration)**이다.

```
function add(num1, num2) {
    return num1 + num2 ;
}
```

두 번째는 function 키워드 다음에 이름을 적지 않아도 되느 **함수 표현식(function expression)**이다. 함수 객체 자체에 이름이 없어 **익명 함수(anonymous function)**라고도 한다.
함수 표현식은 다음과 같이 변수나 속성에 참조 된다.
```
var add = function (num1, num2) {
    return num1 + num2;
}
```

함수값을 add라는 변수에 할당. 함수 표현식은 이름이 없고 끝에 세미콜론이 있다는 점만 제외하면 함수 선언과 거의 동일하다.

***차이점은 함수 선언은 코드가 실행될 때 컨텍스트(선언된 함수를 포함하고 있는 함수 스코프 또는 전역 스코프) 상단에 끌어올려진다.*** 이것이 바로 **호이스팅(hoisting)**이다.

다시 말해 함수를 호출하는 코드가 함수를 선언한 코드보다 앞에 있어도 에러가 발생하지 않는다는 뜻이다.

```
var result = add(5, 5);

functino add(num1, num2) {
    return num1 + num2;
}
```

이 코드를 실행하면 에러가 발생할 것 같지만 잘 작동한다. 자바스크립트 엔진이 함수 선언을 끌어올려서 마치 다음과 같이 작성된 코드처럼 실행하기 때문이다.

```
function add(num1, num2) {
    return num1 + num2;
}

var result = add(5, 5);
```

_함수 호이스팅은 함수 이름을 먼저 처리하기 때문에 일어나는 현상이므로 함수 선언에만 적용된다._

_함수 표현식은 변수를 통해서만 함수를 참조하기 때문에 호이스팅이 되지 않는다._

```
var result = add(5, 5);

var add = function(num1, num2) {
    return num1 + num2;
};
```
위 코드는 실행하면 에러가 발생한다.

---
#### 값으로서의 함수

*자바스크립트에는 일급 함수가 있기 때문에 함수를 다른 객체처럼 다룰 수 있다.*
즉 함수를 변수에 할당할 수도 있고 객체에 추가할 수도 있으며 다른 함수에 인수로 전달하거나 함수에서 함수를 반활할 수도 있다.

```
function sayHi() {
    console.log("Hi");
}

sayHi(); // "Hi" 출력한다

var sayHi2 = sayHi;

sayHi2(); // "Hi" 출력한다
```
1. sayHi라는 함수를 만드는 함수 선이 있다.
2. sayHi2 변수에 sayHi의 값을 할당했다.
3. sayHi와 sayHi2는 같은 함수를 가리킨다.


위의 코드를 아래와 같이 작성했다.
```
var sayHi = new Function("console.log(\"Hi\");");

sayHi();

var sayHi2 = sayHi;

sayHi2();
```

Function 생성자를 사용한 덕분에 다른 객체가 전달되듯 sayHi에 함수 값이 전달되었음을 알 수 있다. 함수도 *객체*라는 사실을 잊지 말자.

예를 들면 함수를 다른 함수에 인수로 전달할 수 있다. 배열의 sort() 메소드에는 비교 함수를 인자로 전달할 수 있다.
만약 첫번째 값이 두 번째 값보다 작으면 비교 함수에서 음수 반환
반면 첫번째 값이 두 번째 값보다 크면 비교 함수에서 양수 반환하고 같을 경우 0을 반환한다.

비교 함수를 전달하지 않으면 sort()는 배열에 있는 원소를 모두 문자열로 변환한 후 문자열을 비교한다. 따라서 항상 비교 함수를 전달해야 한다.

```
var numbers = [1,11,2,22,5,55,3,33];
numbers.sort(function(first, second) {
        return first - second;
    });

    console.log(numbers); // 숫자 정렬 1, 2, 3 ...

    numbers.sort();
    console.log(numbers); // 문자 정렬  1 , 11  , 2 ..
```

위 코드에서 sort()에 전달된 비교 함수는 사실 함수 표현식이다. 이 함수에는 이름이 없으며 다른 함수에 전달된 참조로서만 존재한다.(따라서 **익명 함수**이기도 하다.)


#### 인수
자바스크립트 함수의 다른 특성 중 하나는 인수를 몇 개 전달하든 에러가 발생하지 않는다는 것이다.
_이는 함수의 인수가 실제로는 배열과 비슷한 arguments라는 구조체에 저장되기 때문이다._
* arguments에 담을 수 있는 값은 개수 제한이 없다.(배열과 같다)
* 저장된 인수는 숫자 인덱스로 접근이 가능하다.
* length 속성을 사용하면 저장된 인수의 개수를 알 수 있다.

함수 안에서는 arguments 객체가 자동으로 만들어진다.
다시 말해 이름이 있는 인수는 편의상 둔 것일 뿐 함수에 전달할 수 있는 인수의 개수는 제한이 없다.
***arguments 객체는 Array의 인스턴스가 아니기 때문에 배열 메소를 사용할 수 없다.***

함수의 length 프로퍼티에 접근하면 함수에 전달될 것이라고 기대하는 인수의 개수를 알 수 있다. ***함수도 객체이다.*** 따라서 프로퍼티를 가질 수 있다.
length 프로퍼티는 함수에서 선언한 인수의 **개수(arity)**이다.
자바스크립트에서는 함수에 전달된 인수가 기대했던 것보다 많거나 적어도 에러가 발생하지 않기 때문에 원래 예상했던 인수의 개수를 알아두는 것도 중요하다.


아래 코드는 arguments와 선언된 인수 개수를 사용한 예제이다. 함수에 전달된 인수의 숫자와 인수 개수는 아무런 상관이 없다.

```
function reflect(value) {
    return value;
}

console.log(reflect("Hi!")); // "Hi!""
console.log(reflect("Hi!", 25)); // "Hi!"
console.log(reflect.length); // 1

reflect = function() {
    return arguments[0];
};

console.log(reflect("Hi!")); // "Hi!"
console.log(reflect("Hi!", 25)); // "Hi!"
console.log(reflect.length) // 0
```
 함수 선언
 * 먼저 이름 있는 인수가 하나인 reflect() 함수 정의
 * 그러나 함수에 두 번째 인수를 전달해도 에러가 발생하지 않는다.
 * 이름 있는 인수는 한개이므로 length 프로퍼티 값은 1

 함수 표현식
 * 인수 없는 reflect() 함수 정의
 * arguments[0]을 사용하면 함수의 전달된 첫 번째 인수를 알 수 있다.
 * 새로 정의한 함수는 앞서 만들었던 함수와 똑같이 동작하지만 length의 값은 0이다.

 ***이름 있는 인수가 없는 함수는 인수가 사용되었는지 다소 헷갈릴 수 있으며 확인하려면 함수 내부 코드를 읽어야 한다. 이 떄문에 꼭 필요할 때가 아니면 arguments의 사용을 꺼리는 개발자가 많다.***

 하지만 가끔 이름 있는 인수를 사용할 때보다 arguments를 사용하는 것이 더 효율적일 때도 있다. 전달한 숫자 개수와 상관없이 합을 구해서 반환하는 함수이다. 이 함수에는 숫자가 몇 개 전달될지 모르기 때문에 이름 있는 인수를 사용할 수 없다. 따라서 이때는 arguments를 사용하는것 이 가장 좋은 방법이다.

 ```
 function sum() {

    var result = 0,
         i      = 0,
         len    = arguments.length;

    while ( i < len ) {
        result += arguments[i];
        i++;
    }

    return result;

 }

 console.log(sum(1, 2)); // 3
 console.log(sum(3, 4, 5, 6)); // 18
 console.log(sum(50)); // 50
 console.log(sum()); // 0
 ```

 위 코드는 sum() 함수는 인수를 몇 개든지 전달받을 수 있으며 while 반복문을 사용해 arguments에 있는 값을 모두 더한다. 이 부분은 숫자 배열에 있는 원소를 모두 더할 때와 완전히 똑같다. 이 함수는 result의 값을 0으로 초기화했으므로 전달된 인수가 전혀 없을 때도 동작한다.

 ---
 #### 오버로딩

 대부분 객체지향 언어는 한 함수에 여러 **시그니쳐(signatures)**를 두는 **함수 오버로딩(function overloading)**을 지원한다.
 함수 시그니처는 함수의 이름과 예상 인수 개수, 인수의 타입으로 이루어진다.
 따라서 같은 함수라 해도 문자열 인수 한개를 받는 시그니처와
 숫자 인수 두개를 받는 시그니처를 가질 수 있다.
 프로그래밍 언어는 전달되는 인수에 따라 호출할 함수를 선택한다.

 하지만 자바스크립트 함수는 인수의 개수 제한이 없으며 인수의 타입은 아예 정하지 않는다.
 다시 말해 자바스크립트 함수에는 시그니쳐가 없다. 시그니쳐가 없으므로 함수 오버로딩도 없다.
 아래 코드는 함수 2개를 같은 이름으로 정의하려고 할 때 발생하는 일을 보여준다.
 ```
 function sayMessage(message) {
    console.log(message)
 }

 function sayMessage() {
    console.log("Default message");
 }

 sayMessage("Hello!"); // "Default message" 출력
 ```

 다른 프로그래밍 언어였다면 sayMessage("Hello!")는 "Hello!"라는 문자열을 출력했을것이다. _하지만 자바스크립트에서는 같은 이름을 가진 함수를 여러 개 정의하려고 할때 **마지막에 정의된 함수**가 이긴다._

 앞 코드를 객체를 사용해 쉽게 이해해 보자

 ```
 var sayMessage = new Function("message", "console.log(message);");

 sayMessage = new Function("console.log(\"Default Message\");");

 sayMessage('Hello!'); "Default message" 출력
 ```

 sayMessage 변수에 함수 객체가 연이어 두 번 할당되었으므로 첫 번째 함수는 당연히 사라진다.

 함수에 시그니쳐가 없는 자바스크립트에서도 함수 오버로딩을 흉내 낼 수 있는 방법은 있다.
 arguments 객체를 사용해 함수에 전달된 인수의 개수를 구하고 이를 사용해 수행할 동작을 정하면 된다.

 ```
 function sayMessage(message) {
     if(arguments.length === 0) {
         message = "Default message";
     }

     console.log(message);
 }

 sayMessage("Hello!"); // "Hello!" 출력
 ```
 위 코드에서 sayMessage() 함수는 전달된 인수의 개수에 따라 다르게 동작한다.

 ***전달된 인수가 없다면(arguments.length === 0) "Default Message"라는 기본 값이 사용***

 ***전달된 인수가 있을 때는 첫 번째 인수가 메시지로 사용***

 *타입까지 확인하고 싶다면 typeof 연산자와 instanceof 연산자를 사용하면 된다.*

 > 실제로는 이름있는 인수가 undedfined인지 확인하는 방법이 arguments.length보다 더 많이 사용된다. ( message === undedfined )


---
#### 객체 메소드
객체의 프로퍼티는 언제든 추가하거나 제거할 수 있다.
프로퍼티의 값이 함수라면 해당 프로퍼티는 **메소드**라고 볼 수 있다.
객체에 프로퍼티를 추가하듯 같은 방식으로 메소드도 추가 할 수 있다.
아래 코드에서 person 변수에는 name이라는 속성과 sayName이라는 메소드가 있는 객체 리터럴이 할당되어 있다.

```
var person = {
	name: "LEE",
	sayName: function() {
		console.log(person.name);
	}
};

person.sayName(); // "LEE" 출력
```

데이터 프로퍼티의 문법과 메소드의 문법은 이름 뒤에 클론과 값을 쓰는 부분이 완전히 똑같다.
단지 sayName 메소드에서는 값이 함수 일 뿐이다.
정의 후에는 person.sayName("LEE")처럼 객체에서 메소드를 바로 호출할 수 있다.

---
#### this객체
위에 코드 sayName() 메소드는 person.name을 직접 참고하고 있어 객체와 메소드 간에 강한 결합(tight coupling)이 만들어졌는데, 이는 문제의 소지가 많다.
1. 변수 이름을 바꿀 때는 반드시 메소드 안제 있는 참조 코드도 바꿔줘야 한다.
2. 이런 식으로 강한 결합이 이루어지면 같은 함수를 여러 객체에 사용하기 어렵다.
*다행이 자바스크립트에서 해결할 수 있는 방법이 있다.*

자바스크립트의 모든 스코프에는 함수를 호출하는 객체를 뜻하는 ***this*** 객체가 있다.
전역 스코프에선 this는 전역 객체(웹 브라우저에서는 window)을 참조한다.
객체에 붙어있는 함수(메소드)를 호출하면 this의 값은 해당 객체가 된다.
***메소드 안에서라면 객체를 직접 참조하는 대신 this를 참조할 수 있다.***

````
var person = {
	name: "LEE",
	sayName: fucnction() {
		console.log(this.name);
	}
};

person.sayName(); // "LEE 출력
````

sayName() 에서 person 대신 this를 참조했다.
이젠 변수의 이름도 쉽게 바꿀 수 있으며 sayName 함수를 다른 객체에 재사용하기도 쉬워졌다.

```
function sayNameForAll() {
	console.log(this.name);
}

var person1 = {
	name: "LEE",
	sayName: sayNameForAll
};

var person2 = {
	name: "KIM",
	sayName: sayNameForAll
};

var name = "Oh";

person1.sayName(); // "LEE"
person2.sayName(); // "KIM"

sayNameForAll(); // "Oh"
```

1. sayName 함수 먼저 정의
2. 두 객체 리터럴을 만들면서 sayName에 sayNameForAll 함수를 할당
3. 함수는 참조 값이므로 객체가 몇개든 상관없이 객체의 속성으로 함수를 할당할 수 있다.
4. person1 객체에서 sayName()함수를 호출 할때는 "LEE", person2 객체에선 "KIM" 이는 **함수가 호출 될때 this가 설정되고 그 후 this.name의 값을 가져왔기 때문이다.**

마지박 부분에서는 name이라는 전역 변수를 정의했다.
전역 변수는 전역 객체의 속성이나 마찬가지이므로 sayNameForAll() 함수를 바로 호출하면 전역 변수 name의 값을 가져오는 것이다.

#### this값 변경

자바스크립트 객체지향 프로그래밍을 잘 할려면 함수의 this 값을 잘 다루고 사용할 수 있어야 한다. 일반적으로 this의 값은 자동으로 할당되지만 목적에 따라 바꿀 수도 있다.
자바스크립트에서 this의 값을 바꿀 때 사용할 수 있는 메소드는 세 종류가 있다.

##### call() 메소드
