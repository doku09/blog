# TDD

## Three Three Laws of TDD
1. write NO production code except to pass a failing test
2. write only enough of a test to demonstrate a failure (실패가 일어나는 상황만 작성해라)
3. write only enough production code to pass the test (패스하는 지점까지만 테스트해라)
   >start→write a failing test(red) → write code to make it pass(green) → refactor(blue) → end

### 원칙 & 팁
+ 가장 쉬운것 / 가장 단순한것부터 먼저
+ 최소한의 코드로 돌아갈수있게끔
+ 테스트가 점점 구체화될수록, 프로덕션 코드는 범용해진다. - 모든 케이스를 수용가능해진다.

## TDD의 이점

### Decoupoling

### Courage to Change
+ 테스트를 먼저 작성하면 더 나은 설계를 얻는다.
+ 개발자가 코드를 깨끗하게 리팩토링하는 것을 두려워하면 코드는 썩는다.
+ 테스트가 없으면 코드를 변경하는데 두려움이 생긴다.

### Trust
+ TAD(Test After Development)
   + 테스트를 신뢰할 수 없다.
   + TAD는 지루하다.
   + 이미 수작업으로 테스트를 했기때문에, 이미 코드가 동작하는것을 안다.
   + short cut을 취하게 된다.

**예제**
```java
public class MoviewTest {

   private Movie movie;
   
   @Test
   public void should_return_0_when_just_created() {
      assertThat(movie.averageRating()).isZero();
   }

   @Test
   public void should_return_1_when_1_was_rated() {
      movie.rate(1);
      assertThat(movie.averageRating()).is(1);
   }

   @Test
   public void should_return_4_when_3_and_5_were_rated() {
      movie.rate(3);
      movie.rate(5);

      assertThat(movie.averageRating()).is(4);
   }
}
```
프로덕션 코드
```java
public class Movie {

   private int sumOfRate = 0;
   private int countOfRate = 0;

   public Integer averageRating() {
      return countOfRate === 0? 0: sumOfRate / countOfRate;
   }

   public void rate(int rate) {
      this.sumOfRate += rate;
      countOfRate++;
   }
}

```

