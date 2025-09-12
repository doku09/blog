# Single Responsibility Principle
모듈은 하나의 변경사유를 가져야한다. 변경사유는 `Actor`

## Responsibility
+ 클래스는 하나의 책임을 가져야한다.
+ 책임은 무엇인가?
+ 같은 부류를 나눠서 책임이 몇개인가. 그렇다면 부류를 어떻게 나눌것인가?  
+ SRP에서는 누가 사용하는가를 본다. 누가 해당 메서드의 변경을 유발하는 사용자인가.
+ SRP는 사용자에 관한것.

### It's About Roles
User들을 그들이 수행하는 Role에 따라 나눠야한다.

### Two Values of SW
+ 지속적으로 변화하는 요구사항을 수용하는것 
+ 대부분의 SW 경우 현재의 요구사항을 잘만족하지만 변경하기 어렵다.


### SRP를 준수하는 방법
1. Inverted Dependencies
+ 클래스를 인터페이스와 클래스로 분리
+ 모든 Actor들이 하나의 인터페이스에 coupled
2. Extract Classes
3개의 책임을 분리하는 방법: 3개의 클래스로 분리
3. Facde - 어디에 구현이 있는지 찾기 쉽게 
4. Interface Segregation
+ Actor들은 완전히 decoupled
+ 어디에 구현되어있는지 찾기 어렵다.
