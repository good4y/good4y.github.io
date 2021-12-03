## 오류

자바에서는 에러를 크게 컴파일 시 발생하는 에러인 컴파일 에러와 프로그램 실행 중 나타나는 에러인 런타임 에러 두 종류로 나뉜다.  
여기서 다시, 런타임시 발생하는 에러를 자바는 에러와 예외 두가지로 나눈다.  
에러는 스택오버플로나 메모리 부족 등 발생 시 복구가 불가능한 체크 예외(Check Exception)이고, 
미리 코드를 작성 해 놓음으로써 수습이 가능한 언체크 예외(Uncheck Exception)으로 나뉜다.

## 예외(Exception)

1. Check Exception
체크 예외는 RuntimeException을 상속하지 않은 Exception클래스들이다. 체크 예외는 예외가 발생할 수 있는 메소드를 사용 시에  
반드시 예외를 처리해주는 코드를 함께 작성해야 한다. 이때, 예외를 처리하기 위해 catch문으로 잡거나, throw를 통해 메소드 밖으로  
빠질 수 있다. 예외처리를 해주지 않는다면, 컴파일 에러가 발생한다.

2. Uncheck Exception
언체크 예외는 RuntimeException클래스를 상속한 exception 클래스들이고, 예외처리를 강제하지 않는다.  
RuntimeException은 프로그램에 문제가 생겼을 때 오류가 발생하도록 의도한 것으로, NullPointerExceptuon이나 IllegaArgumenException등이 있다.  
이러한 예외들은 피할 수 있지만, 개발자가 부주의해서 발생할 수 있는 경우를 방지하기 위해 만들어졌다.

## JPA에서의 예외처리
JPA에서는 @Transaction의 기본 롤백 대상이 Unchecked Exception이기 때문에 Checked Exception에서의 롤백은 임의로 설정 해 주어야 한다  
이때, rollbackFor 옵션을 이용하여 간편하게 설정이 가능한데 사용법은 다음과 같다.    

- @Transactional(rollbackFor = {Exception.class})

위와 같은 방법으로 모든 예외에 대해 전부 트랜잭션을 롤백 할 수 있다.
기본적으로 RuntimeException은 디폴트 값이기 때문에 @Transactional 어노테이션만 이용하더라도 Uncheck Exception은 예외처리 시 롤백이 자동으로 된다.  

- HTTP Status
@ResponseStatus
어노테이션을 적용하는것으로 간편하게 예외처리를 할 수 있지만, 같은 예외상황에 다른 메세지를 보내는것이 불가능하다.

@ResponseStatusException
RuntimeException을 상속하는 클래스로 아래와 같은 구성으로 이루어져 있다.

status: HTTP Status
reason: HTTP response Message
cause: ResponseStatusException 을 발생시킨 Exception

- Spring 전역으로 공통 Exception 처리하기
Spring에는 프로젝트 전역에서 발생하는 Exception을 한 곳에서 처리 할 수 있다.

Enum 클래스로 Error코드를 정의하면 Exception 클래스를 매번 생성하지 않아도 된다.

실제 클라이언트에게 날아가는 응답에서 code부분만 확인하면 어떤 에러가 발생했는지 쉽게 파악 가능하다.
