# BlockingQueue
자바는 생산자 소비자 문제를 해결하기 위해 특별한 멀티스레드 자료구조를 제공한다. 

## 주요메서드
- 데이터 추가 메서드: add(),offer(),put(),offer(타임아웃)
- 데이터 획득 메서드: take(),poll(타임아웃),remove(..)
- 큐를 상속 받았기 때문에 큐의 기능도 사용가능 하다. 

# BlockingQueue 인터페이스의 대표적인 구현체
- ArrayBlockingQueue: 버퍼의 크기를 고정하고싶다. 
- LinkedBlockingQueue : 무한으로 사용하고싶다. (크기 고정도가능함.)

## 큐가 가득찼을때 선택지 4가지
- 예외를 던진다. 예외를 받아서 처리한다.
- 대기하지 않는다. 즉시 false를 반환한다.
- 대기한다.
- 특정 시간 만큼만 대기한다.

## BlockingQueue의 다양한 기능 - 공식문서
- ThrowsException - 대기시 예외
  - add(e)
  - remove()
  - element()
- Special Value - 대기시 즉시 반환
  - offer(e) - 없으면 즉시 false반환, 데이터 버림
  - poll()
  - peek()
- Blocks - 대기
  - put(e)
  - take()
  - Examine(관찰)
- Times Out - 시간 대기
  - offer(e,time,unit)
  - poll(time,unit)
  - Examine(관찰)
