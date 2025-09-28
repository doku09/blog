## Builder 패턴

---

### 작성계기

롬복에서 제공하는 @Builder 애노테이션의 무지성 사용을 방지. 

### Builder 패턴이란?
객체 생성을 가독성 있게 도와주는 패턴으로, 생성자에 많은 인자가 필요할 때 유용하다.

### @Builder 내부 로직
```java
public class Person {
    private String name;
    private int age;
    
    private class PersonBuilder {
        private String name;
        private int age;
        
        public PersonBuilder name(String name) {
            this.name = name;
            return this;
        }
        
        public PersonBuilder age(int age) {
            this.age = age;
            return this;
        }
        
        public Person build() {
            // 여기는 실제 생성자활용.
            return new Person(name, age);
        }
    }
}
```
### 주의점
@RequestBody로 받는 RequestDto 객체에 Jackson라이브러리는 기본생성자를 필요로한다.   
-> @Builder가 있는 채로 기본생성자를 선언하면 **API 요청 시** 에러남.
-> Builder는 전체필드 생성자로 생성하는데 클래스에 생성자선언이 안되어있으면 내부적으로 모든필드에 대해 생성자를 만든다. 
-> 그러나, 명시적으로 기본생성자를 생성하면 내부적으로 만든 생성자를 사용하지 않고, 사용자가 만든 생성자를 사용하게됨.
-> 근데 기본생성자만 명시적으로 선언되어있고 전체생성자가 없으면 에러가 발생한다.

