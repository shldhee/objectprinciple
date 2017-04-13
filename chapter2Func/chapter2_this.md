# this값 변경
### call() 메소드

**this를 조작할 때 사용할 수 있는 첫 번째 함수 메소드는 call()이다.**

* this의 값을 바꾸고
* 인수도 전달하고
* 함수를 실행한다.


```
function sayNameForAll(label) {
    console.log(label + ":" + this.name);
}

var person1 = {
    name: "LEE"
};

var person2 = {
    name: "Oh"
};

var name = "Kim";

sayNameForAll.call(this, "global"); // "global:Kim"
sayNameForAll.call(person1, "person1"); // "person1:Lee"
sayNameForAll.call(person2, "person2"); // "person2:Oh"
```

* call() 메소드의 첫 번째 인수는 함수를 실행할때 this로서 사용될 값이다.
* 두번째 인수부터는 함수를 실행할 때 그대로 전달된다.(매개변수)

위 코드는 sayNameForAll() 함수에는 출력 문자열에 사용할 인수가 한 개 전달되어야 한다.
함수를 바로 실행하지 않고 마치 객체처럼 접근해서 사용했긴 때문에 함수 이름 바로 뒤에는 괄호가 없다.

1. 처음 호출 : 전역 컨텍스트 this사용, "global"을 인수로 전달
2. 2번째 호출, 3번째 호출 떄에는 person1, person2를 컨텍스트로 사용


---
### apply() 메소드
**this를 조작할 때 사용할 수 있는 두 번째 함수 메소드는 apply()이다.**
apply()메소드는 call()과 완전히 똑같이 동작하지만 *인수를 두 개만 사용한다는 점*이 다르다.

* apply() 메소드의 첫 번째 인수는 this로 사용할 값이고
* 두 번째 인수는 함수에 전달할 인수를 담고 있는 배열 또는 배열과 유사한 객체이다.(arguments 전달 가능)
*따라서 call()을 사용해 각 인수를 일일이 전달하는 대신 apply()를 사용하면 손쉽게 전달할 값의 배열을 두 번째 인수로 전달 할 수 있다. 이 점을 제외하면 call(), apply()는 똑같이 동작한다.*

```
function sayNameForAll(lable) {
    console.log(label + " : " + this.name);
}

var person1 = {
    name: "LEE"
};

var person2 = {
    name: "Oh"
};

var name = "Kim";

sayNameForAll.apply(this,["global"]); // "global : Kim"
sayNameForAll.apply(person1,["person1"]); // "global : LEE"
sayNameForAll.apply(person2,["person2"]); // "global : Oh"
```

*데이터가 배열 형태로 존재한다면 **apply()** 개별 변수로 존재한다면  **call()** 사용하는 편이 좋다.*

---

### bind() 메소드

**this를 바꿀 때 사용할 수 있는 세 번째 함수 메소드는 bind()이다.(ECMAScript 5에서 추가)**

* bind()의 첫 번째 인수는 새 함수에서 this로 사용될 값
* 그 밖의 인수는 새 함수를 실행할 때 항상 전달되는 고정 인수를 의미르
* 물론 고정 인수 외에 다른 인수도 전달 할 수 있다.

```
function sayNameForAll(label) {     인수 아규먼츠
    console.log(label + ":" + this.name);
}

var person1 = {
    name: "Lee"
};

var person2 = {
    name: "Oh"
};

var sayNameForPerson1 = sayNameForAll.bind(person1);
sayNameForPerson1("person1"); // "person1:Lee" 인자

var sayNameForPerson2 = sayNameForAll.bind(person2,"person2");
sayNameForPerson2(); // "person2:Oh"

person2.sayName = sayNameForPerson1;
person2.sayName("person2");

```

위 코드는 bind()의 두 가지 사용법을 보여준다.
1. sayNameForPerson1() 함수는 this값을 person1로 바꾸면서 작성한 함수
sayNameForPerson1() 에는 고정된 인수가 없으므로 실행할 때는 인자를 넘겨줘야 된다.

2. sayNameForPerson2() 함수는 this값을 person2로 바꾸면서 작성한 함수 또한 sayNameForPerson2() **함수의 첫 번째 인수로 항상 "perons2" 전달**
this가 person2로 고정

마지막 부분 sayNameForPerson1()을 person2 객체에 sayName이라는 이름으로 추가 이 ***함수의 this는 이미 고정(person1) sayNamePerson1을 person2에 추가해도 this가 값은 바뀌지 않는다.***
따라서 person2.sayName()은 여전히 person1.name의 값을 출력한다.
