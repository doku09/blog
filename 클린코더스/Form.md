# Forms

## 🎢Coding Standards
프로그래머는 필요해서가 아니라 해야하므로 주석을 작성함. 하지만 이런 주석은 무의미하다. 사람들이 무시한다.
> - 양치기 소년이 될 수 있다. 중요한 주석도 안보게됨.
> - comments shoud be rare > 반드시 필요할때만 사용.

### Comments are Failures
모든 주석은 당신의 코드가 expressive하지 못하다는 것을 나타내는 상징이다.

### Good Comments
1. LegalComments
2. Informative Comments
``` text
kk:mm:ss EEE ,MMM dd yyy 

시간이 오래걸리니까 여유있을때 실행해라
```
3. Warning of Consequeces
4. TODO Comments
5. Public API Document

### Bad Comments
- Mumbling: 중얼중얼 ~해서,~하니
- Redundant Explanations: 중복적인 설명
- Journal Comments
- Noways Comments
- Big Banner Comments
- Closing Brace Comments: 끝을 나타내는 주석
`// end of if, // end of for`
- Attribution Comments
` // add by 유동환`
- Non-Local Information: this.port에 대한 주석을 필드에 작성하지 않고 메서드에 작성
```text
    /**
    * port on which serer run.
    */
    public void setServerPort(int port) {
        this.port = port
    }
```

### Vertical Comments 
- 공란을 함부로 사용하지 말라
- 메서드 사이
- private 변수들과 public 변수들 사이
- 메서드내 변수 선언과 메서드 실행의 나머지 부분 사이
- if/while 블록과 다른 코드 사이
  
### 클래스
- 객체의 상태를 외부에서 사용할 수 있도록하는 Getter/Setter 등을 제공하는 것은 Bad Design
- Tell, Don't Ask
- getter/setter가 없을 순 없다. 최소화해라 


어떤 필드가 있는지 외부에서 모르게 Getter이름을 달리해라
``` java
class Car {
    private Gas gallonsofGas

    getGallonsOfGas() // X
    getPercentFuelRemaining() // O
}
```

``` mermaid
classDiagram
    class Car {
        -gallonsOfGas
        +getPercentFuelRemaining()
    }

    class DieselCar {
        +getPercentFuelRemaining()
    }

    class ElectricCar {
        +getPercentFuelRemaining()
    }

    class NuclearCar {
        +getPercentFuelRemaining()
    }

    class CarDriver

    Car <|-- DieselCar
    Car <|-- ElectricCar
    Car <|-- NuclearCar

    CarDriver --> Car : uses
```
객체지향의 핵심은 IOC를 통해 High Level Policy(CarDriver)를 Low Level Detail(DiselCar,ElectricCar,NuclearCar)로 부터 보호하는 것

### Data Structure
객체지향은 타입추가는 자유롭지만, 기능추가에는 취약하지만,
기능 추가는 굉장히 자유롭지만, 타입추가는 취약하다
> 비지터 패턴은 객체지향에서 기능추가를 쉽게할수있다.