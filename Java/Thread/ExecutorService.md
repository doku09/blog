## ExecutorService란?
- 병렬 작업 시 여러 개의 작업을 효율적으로 처리하기 위해 제공되는 JAVA 라이브러리이다.

> ExecutorService가 없었다면? 
> 각기 다른 Thread를 생성해서 작업을 처리하고, 처리가 완료되면 해당 Thread를 제거하는 작업을 손수 진행해야하는 것을 ExecutorService 클래스를 이용하면 쉽게 처리가능하다.


```java
for (int i = 0; i < threadCount; i++) {
    futures.add(executorService.submit(() -> pointService.charge(userId,chargeAmount)));
}
```






> 출처: https://simyeju.tistory.com/119 [남기고 싶은 것들:티스토리]