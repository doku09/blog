

프로그램을 실행하면 프로세스가 된다.
프로세스 안에 스레드가 존재 , 여러스레드가 있을수있다(멀티스레드)

스레드안에 스택이 존재
스레드들 끼리는 힙영역을 공유한다. 

CPU는 하나당 스케줄링에 의해  스레드 하나를 동작함 
* <span style="color:cornflowerblue;">프로세스를 동작하는게 아니였음!</span>

컨텍스트 스위칭 - CPU가 작업하는 스레드를 스위치하는 일.
스레드를 생성하는 방법
1. Thread 클래스를 상속
2. Runnable 함수형 인터페이스를 구현 후 Thread 클래스에 주입

스레드 실행하는 방법
스레드를 기다려준다.
thread.join(); > 다른 스레드가 끝날때까지 기다려준다
interrupt() 
interrupted()
join(시간초); 

```java
public static void main(String[] args) {
		MyTask task = new MyTask();
		Thread t = new Thread(task, "work");
		log("runFlag = " + task.runFlag);
		t.start();

		sleep(1000);
		log("runFlag를 false로 변경 시도");
		task.runFlag = false;
		log("runFlag = " + task.runFlag);
		log("main 종료");
	}

	static class MyTask implements Runnable {

//		boolean runFlag = true;
		volatile boolean runFlag = true;

		@Override
		public void run() {
			log("task 시작");

			while(runFlag) {
				// runFlag가 false로 변하면 탈출
			}
			log("task 종료");
		}
	}
```

문제) 힙 영역에 있는 runFlag를 false로 변경했는데 task스레드가 빠져나오지 못한다.
> 왜?) CPU는 캐시메모리라는 것을 사용한다. CPU 코어마다 빠르게 동작하기 위한 캐시메모리 하나가 존재한다. runFlag값이 캐시메모리에 올라온다. 메인메모리(RAM)에 있는 runFlag값을 바꾸면 메인 코어의 캐시메모리에 있는 값만 바뀌기 때문에 task스레드는 계속 실행된다.

- task스레드를 동작하는 CPU의 캐시메모리에는 언제 반영할까? 모른다. CPU 설계방식과 실행 환경에 따라 다를수 있다.  

## 메모리 가시성
멀티스레드 환경에서 한 스레드가 변경한 값이 다른 스레드에서 언제 보이는지에 대한 문제를 **메모리 가시성** 이라고한다. 이름 그대로 메모리에 변경한 값이 보이는가, 보이지 않는가의 문제

### Java Memory Model
여러 스레드들의 작업 순서를 보장하는 happens-before 관계에 대한 정의 
### happens-before
스레드간의 작업 순서를 정의하는 개념이다.
- 한 동작이 다른 동작보다 먼저 발생함을 보장한다.
- 스레드간의 메모리 가시성을 보장하는 규칙
- happens-before 관계가 성립하면, 한 스레드의 작업을 다른 스레드에서 볼수 있게된다.

### valotile
runFlag에 대해서는 캐시메모리의 값을 사용하지 말고 메인메모리에 바로 직접접근해라!
<span style="color:pink">성능이 느려지기때문에</style> 꼭! 필요한 곳에서만 사용하길 권장한다.

## syncronized
- 멀티스레드를 사용할떄 가장 주의해야할점 - 여러 스레드가 동시에 접근할때 발생하는 동시성문제
- 여러 스레드가 접근하는 자원을 동시자원이라고 한다.
- 
### 은행문제
1000원을 가지고 있는 통장에서 스레드1 스레드2가 동시에 800원을 출금하면  
스레드1에서 출금이 이뤄지기 전에 스레드2에서 로직이 실행되면 잔액은 1000원인데 두개의 스레드에서 800원씩 1600원을 출금하게되어 -600원이 되어버린다.
두 스레드가 정말 동시에 실행되면 200원이 남을수도 있다. 계산결과가 똑같이 200원이므로

앞선 문제가 발생하는것은,여러 스레드가 함꼐 사용하는 공유자원을 여러단계로 나누어 사용하기 때문이다.
- 검증 - 잔액 확인
- 출금 - 잔액 감소
검증과 출금은 한번에 하나의 스레드만 실행되어야한다.

## 임계영역
크리티컬 섹션이라고 한다.
여러 스레드가 동시에 접근할때 예상치 못한 동작이 발생할수있는 위험하고 또 중요한 코드부분을 뜻한다.
**검증~출금 단계** 가 임계영역이라고 할 수 있다
어떻게 임계영역을 한번에 하나의 스레드만 실행할 수 있게할수 있을까? 
`syncronized` 키워드를 통해 아주 간단하게 임계영역을 보호할 수 있다.

```java
public synchronized boolean withdraw(int amount) {
    log("거래 시작: " + getClass().getSimpleName());
      ... 이하 생략
```

### synchronized
모든 객체는 내부에 자신만의 락을 가지고 있다. 100개를 만들면 100개의 락을 가지고있음. 
- 모니터락 이라고도 부른다.
스레드가 synchronized 키워드가 있는 메서드에 진입하려면 반드시 해당 인스턴스의 락이 있어야한다.

스레드 t1이 BackAccount 인스턴스의 락을 획득한다.
t2는 락을 가지고있지 않기때문에 BLOCKED 상태로 락을 대기하면서 아무것도 하지않는다
한번에 하나의 스레드만 실행하는 안전한 임계 영역구간을 편리하게 만들 수 있다. 

여러 스레드가 동시에 실행하지 못하기때문에 전체적으로 보면 성능이 떨어진다 꼭! 필요한 곳으로 한정해야한다.
가능한 최소한의 범위를 지정해야한다. **정말 필요한 부분**에서 코드블록 단위로 지정할수있다.

- 단점
  - 무한대기
  - 공정성 : 스레드가 오랜기간 락을 획득하지 못할수있음.

```java
	public boolean withdraw(int amount) {
	log("거래 시작: " + getClass().getSimpleName());

	synchronized (this) {
		// 잔고가 출금액보다 많으면, 진행
		log("[검증 완료] 출금액: " + amount + ", 잔액: " + balance);
		sleep(1000); //출금에 걸리는 시간으로 가정
		balance = balance - amount;
		log("[출금 완료] 출금액: " + amount + ", 잔액: " + balance);
          ...이하 생략
	}
}
			
```
이런 동기화를 사용하면 다음 문제를 해결할 수 있다.
- 경합조건(Race condition) : 두개 이상의 스레드가 경쟁적으로 동일한 자원을 수정할떄 발생하는 문제
- 데이터 일관성: 여러 스레드가 동시에 읽고 쓰는 데이터의 일관성을 유지

## LockSupport
- 스레드를 WAITING 상태로 변경한다.
- synchronized의 무한대기 단점을 개선한 자바 1.5이후의 라이브러리

LockSupport 의 대표적인 기능은 가능과 같다.
park() : 스레드를 WAITING 상태로 변경한다.
스레드를 대기 상태로 둔다. 참고로 park 의 뜻이 "주차하다", "두다"라는 뜻이다.
parkNanos(nanos) : 스레드를 나노초 동안만 TIMED_WAITING 상태로 변경한다.
지정한 나노초가 지나면 TIMED_WAITING 상태에서 빠져나오고 RUNNABLE 상태로 변경된다.
unpark(thread) : WAITING 상태의 대상 스레드를 RUNNABLE 상태로 변경한다.

WATING 상태에 잇는 나를 내가 꺠우진 못한다. 

- RUNNABLE -> park() -> WAITING -> unpark() -> RUNNABLE
> 무한 대기하지 않는 락 기능을 만들수 있다. 
## BLOCKED vs WAITING
- BLOCKED 상태는 인터럽트 걸려도 대기상태를 빠져나오지 못함. BLOCKED 상태임 (특별한 대기상태)

## ReentrantLock = LockSupport + synchronized
synchronized보다 훨씬 유연하게 다룰 수 있다. 
- void lock() >락이 풀릴때까지 대기한다. 인터럽트 응답하지 않는다.
  - 맛집에 한번줄을 서면 끝까지 기다린다.
- void lockInterruptibly() 
  - 맛집에 줄을 서는데 다른맛집을 찾으면 중간에 포기하고 나간다.
- boolean tryLock() 
  - 락을 획득하면 들어간다.(true) 대기줄이 있으면 바로 포기하고 나간다.(false)
- boolean tryLock(long time, TimeUnit unit)
  - 특정 시간만큼만 기다린다. 다른맛집을 찾았다고 연락이 와도 포기한다. 
- void unlock() 
  - 락을 획득한 스레드가 호출해야한다.


비공정모드
- 락획득 속도빠름
- 선점
- 특정스레드가 오래 락을 획득하지 못할수도 있다.

공정모드
- 락을 순서대로 스레드가 락을 획득
- 모든 스레드가 언젠가 락을 획득 
- 락을 획득하는 속도가 느려질수 있음. 

**ReenterantLock은 모니터락을 사용하지 않는다. 내부에서 Lock 기능을 제공함.**

## 언제 ReentrantLock을 고려해야 할까?
- 락을 걸거나 해제하는 시점을 명확히 컨트롤해야 할 때
- 특정 시간 동안만 대기하고 실패하고 싶을 때 → tryLock(timeout)
- 락 상태 확인이나 공정성 설정이 필요할 때?
- 복잡한 조건 동기화가 필요할 때 → Condition 사용


