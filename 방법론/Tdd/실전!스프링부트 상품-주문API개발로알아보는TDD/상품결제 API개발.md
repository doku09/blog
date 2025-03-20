# 주문 결제 API 개발

## POJO 주문 결제 기능 구현하기

아무런 클래스 없이 아래 테스트 코드로 시작하여 필요한 객체를 구현해 나아간다.

```java
@Test
public void 상품주문() {
    PaymentRequest request = PaymentSteps.주문결제요청_생성();
    paymentService.payment(request);
}
```
PaymentRequest는 결제요청을 위한 객체로, 주문번호, 결제금액, 결제수단 등을 담고 있다.

```java
public static PaymentRequest 주문결재요청_생성() {
    final Long orderId = 1L;
    String cardNumber = "234-1234-1234-1234";
    return new PaymentRequest(orderId, cardNumber);
}
```

PaymentService의 payment 메서드를 살펴보자

```java
public void payment(final PaymentRequest request) {
    Order order = paymentPort.getOrder(request.orderId()); 
    
    final Payment payment = new Payment(order, request.cardNumber());
    
    paymentPort.pay(payment.getPrice(), payment.getCardNumber());
    paymentPort.save(payment);
}
```
`payment` 메서드에서는 실제 결제를 처리한다. 넘어온 request객체에서 orderId를 꺼내 주문정보를 찾는다.  
주문정보에는 주문상품,수량,총액 등 정보가 담겨있다.  
주문정보와 고객의 결제카드 넘버를 받아 **결제객체(Payment)** 를 생성한다. 

결제를 처리(pay)하고, 결제 내역을 저장(save)한다.

아래는 PaymentAdapter에서의 pay와 save로직이다.

```java
PaymentAdapter.java

@Override
public void pay(int totalPrice, String cardNumber) {
    paymentGateway.excute(totalPrice, cardNumber);
}

@Override
public void save(Payment payment) {
    paymentRepository.save(payment);
}
```
Pay로직을 보면 paymentGateway의 excute메서드를 실행하여 결제를 진행한다. 
PaymentGateway란 흔히 알고 있는 PG사,전자결제대행사(Payment GateWay)를 의미한다.

예제로 실제 결제를 하진 않지만 구현한 메서드에서는 실제 결제 로직이 들어간다. 

adapter에서는 `repository`를 통해 DB에 결제에 대한 정보를 저장한다.
```java
class PaymentRepository {
	
    private Map<Long, Payment> persistence = new HashMap<>();
    private long sequence = 0;

    public void save(Payment payment) {
        payment.assignId(++sequence);
        persistence.put(payment.getId(), payment);
    }
}
```
POJO로 구현한 결제 로직은 이정도면 충분한 것 같다. 

---
# ⭐NOTICE
## Enum 객체별 메서드 구현하기 
```java
public enum DiscountPolicy {
NONE {
    @Override
    int applyDiscount(int price) {
        return price;
    }
},
  
FIX_1000_AMOUNT {
    @Override
    int applyDiscount(int price) {
      return Math.max(price - 1000, 0);
    }
}
;

  abstract int applyDiscount(final int price);
}
```
enum 타입별로 어떤 값을 변경하거나 제한해야 할때 추상메서드를 추가하고 구현하는 식으로 로직을 추가할 수 있다.

---

