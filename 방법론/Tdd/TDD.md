# 테스트 주도 개발(Test-Driven Development)
테스트로부터 시작하는 개발 방식
1. (실패하는) 테스트 코드 작성
2. 테스트를 통과시킬 만큼 구현
3. 코드 정리(리팩토링)

> 테스트(red) > 코딩(green) > 리팩토링(blue)

## TDD를 시작할때 어려운점
- 어디서부터 테스트 코드를 작성해야하나?
- 쉬움/예외 -> 어려움/정상
쉬운거부터 시작해서 또는 예외적인거 부터 시작해서 어려운것, 정상인것 순으로 테스트 코드를 만들기

어려운거 먼저하면 시간이 오래걸린다. 
예외적인 상황을 뒤에서 하면 이미 구현한 코드구조에 영향을 준다.

예시)
- 회원가입: 동일 이메일이 이미존재하는 경우 vs 동일 이메일이 존재하지 않는경우
- 만료일 계산
    - 1월 31일에서 한달뒤 vs 1월 25일에서 한달뒤
    - 매월 말일은 다르기때문에 1월 31일이 더 어렵다. 
- 회원 주소변경
    - 회원이 없는경우 vs 회원이 있는 경우

## TDD시 중요한점
- 완급조절
    - TDD로 통과시키는 과정
        - 정해진 값을 리턴
        - 값 비교를 이용해서 정해진 값을 리턴
        - 다양한 테스트를 추가하면서 구현을 일반화
    - 구현이 생각나면 빠르게 구현할때는
        - 단 테스트를 통과시킬 만큼만
        - 앞서 가지말것!
    - 구현이 막히면 다시 돌아와서 천천히 진행

## TDD 설계와 기능
- 기능의 구성: 입력, 결과
- 기능 명세 -> 설계로 연결
  
  # 테스트를 만들려면
  - 테스트할 기능실행
    - 클래스,메서드,함수 이름
    - 파라미터
  - 결과를 검증
    - 리턴 값

