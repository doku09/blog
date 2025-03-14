## String name = "John" 과 new String("John")의 차이

> Java의 String은 불변하다.

아래 코드처럼 `name`을 "John" 이라는스트링 값으로 초기화한 후 "Jimmy"로 값을 재할당했을때
실제로 `name`이 가르키는 저장공간에서 값이 변경된 것처럼 보이지만 실제로는 `"Jimmy"` 문자열을 담은 새로운 저장공간을 만들어서 참조를 바꾼 것이다.

```java
String name = "John"
name = "Jimmy"
String anotherName = "John"
```

이렇게 String을 리터럴로 작성한 문자열은 자바에서 String Constant Pool에 저장된다.

세번째 라인에 있는 `anotherName` 변수가 다시 `"John"` 문자열 리터럴로 초기화 했는데 이때는 이미 Constant Pool에 "John"이라는 문자열이 저장되어 있기 때문에 새로 생성하지 않고 기존 공유객체를 가르킨다.

즉, String Constant pool에 저장된 문자열은 모두 같은 객체를 재사용 하게 된다.

```java
String name = "JON";
String name2 = "JON";

System.out.println("name == name2: " + (name == name2)); // true
```

같은 저장 공간을 가르키기 때문에 `==` 연산자가 true로 나온다.

new String은 Heap영역에 저장하므로 아예 저장 공간이 다르다.

왜 자바는 이런 방식을 선택했을까

1. 메모리 공간을 절약할 수 있다.
2. 수정이 불가능하기 떄문에 보안성이 좋다.
3. 멀티 스레드로부터 안전하다.
