# SOLID Foundation

### Rigidity
시스템의 의존성으로 인해 변경하기 어려워지는 것

### Fragility
한 모듈의 수정이 다른 모듈에 영향을 미칠때

### Immobility
모듈이 쉽게 추출되지 않고 재사용 되지 않는 경우

### Viscosity(점성)
- 빌드/테스트 같은 필수 오퍼레이션들이 오래 걸려 수행이 어렵다면 그 시스템은 역겨운것
    - 여러 레이어를 가로질러 의존성을 갖는 것은 역겨움 
- 항상 같은 역겨움의 원인
    -  무책임한 용인 

### Needless Complexity
- 현재 요구사항에 집중한다.
- 미래까지 생각해서 코딩하면 나중에 나도 못알아본다. 
- 테스트 가능한 코드 

### Code Rot

### Procedural
- High Level depend on Low Level

### OOP
- High Level don't depend on Low Level
- Dependency Inverted
- 런타임의 흐름은 상위→하위, 소스코드의 의존성은 하위→상위
- IOC via Reader/Writer Interface 
  
### What is OO?
- o.f(x) != f(o,x)
- Dynamic polymorphism
- OO는 메시지를 전달하는것
- Dependency Inversion - OO의 정수

### OO의 핵심
- IOC를 통해 상위 레벨의 모듈을 하위 레벨의 모듈로부터 보호하는 것 
- 의존성을 잘 관리하는 것 

### 의존성관리
의존성관리에 대한 중요한 규칙 -SOLID