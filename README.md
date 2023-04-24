트랜잭션이란? 완전히 성공하거나 완전히 실패하는 일련의 논리적 작업단위, 둘 중 하나라도 실패 시 전체 프로세스는 실패 

트랜잭션 단위(ex)은행 계좌이체 ): A의 계좌에서 출금하는 금액과 B의 계좌에서 입금하는 금액

트랜잭션에는 데이터를 최종적으로 데이터베이스에 반영하는 COMMIT과 실패했을때 원 상태로 다시 되돌아가는 ROLLBACK이 있다.



public void buy() {

  buyer.send();			//구매자 출금
  seller.receive(); //판매자 입금 
}



만약 send() 까지 실행이 되고 receive() 메소드는 실행이 되지 않을 경우 문제가 발생할 수 있음

이 경우 try-catch문을 통해 아래와 같이 구현할 수도 있지만



public void buy() {

  DefaultTransactionDefinition def = new DefaultTransactionDefinition();
  TransactionStatus status = transactionManager.getTransaction(def);

  try {
      buyer.send();			
      seller.receive();
      
      transactionManager.commit(status);

  } catch(Exception e) {

      transactionManager.rollback(status);

  } 
}



쿼리를 사용하는 메소드에 매번 중복되는 코드를 작성해 주어야 하는 불편함 존재

 @Transactional 어노테이션만 붙여주면 아래와 같이 간결하게 표현 가능

@Transactional
public void buy() {

  buyer.send();			
  seller.receive();

}


-----------------------------------------------------------------------------------------------------------------------


![계좌 이체](https://user-images.githubusercontent.com/40292371/233939966-6a003ea3-a2b1-4736-a46b-59c1cda3f63f.png)




기존의 단순 OOP에서는 계좌이체, 입출금, 이자계산의 서비스가 각각의 OOP로 프로그래밍 되었고, 각각의 OOP 모두 기능 작동을 위해 로깅, 보안, 트랜잭션을 하는 코드가 구현되어있었다.

그런데 계좌이체, 입출금, 이자계산 비즈니스 모두가 공통적으로 갖는 로직이 있는 것을 알 수 있다. 그렇다면 이것을 각각의 OOP 소스코드에서 제거하고 외부로 빼내 하나의 공통 모듈로 만들 수 있다. 이것이 바로 기존의 OOP에 AOP 관점을 더해 발전시킨 기법이다.




