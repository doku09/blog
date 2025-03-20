# DDD (Domain Driven Design) - 도메인 주도 설계
각각의 기능적인 문제를 정의하는 도메인과 그 도메인을 사용하는 비즈니스 로직을 중심으로 설계하는 방식

## 특징
1. 데이터 중심이 아닌 도메인의 모델과 로직에 집중
2. 동일한 표현과 단어로된 단일화된 언어체계 사용 
    - 언어Ubiquitous Language, 보편적 언어 사용
    - 단일화된 업무용어를 사용
3. Software Entity와 Domain간 개념의 일치

## 왜 DDD?
레거시 시스템 전환은 한번에 수정할 수 없다. TDD나 BDD는 한번에 뒤집기가 어려움. 
> 필요한 기능들을 구별하고 필터링하여 점진적인 향상 

## DDD 적용에 필요한 것들
중요한 개념 3가지 
- Bounded Context(범위를 구분해놓은 하위 도메인 개념)
- Context Map(Bounded Context 관계를 보여줌)
- Aggregate(lifecycle이 같은 도메인들을 한군데 모아둔 집합)
  
## DDD의 아키텍처
- Layered 
- Clean
- Hexagonal (Port And Adapter)

## 장점
- 보편적인 언어 사용에 따른 빠른 커뮤니케이션
- 도메인간 관계가 복잡한 경우 큰 틀에서 정리가 가능
- 도메인의 분리에 따른 유지보수에 대한 편의성
- 새로운 기능 및 요구 사항에 대한 유연성
- Encapsualtion
- 낮은 결합도, 높은 응집도
- Domain Logic의 분리로 Business Logic에 집중
- 코드 가독성 업
  
## 단점
- 개발난이도가 높음
- 각 도메인에 대한 높은 이해도가 필요
- 아키텍처 구현에서 생성되는 생각보다 많은 코드 필요

     


