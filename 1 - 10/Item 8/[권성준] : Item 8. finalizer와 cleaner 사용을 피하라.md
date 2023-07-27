# Item 8. finalizer와 cleaner 사용을 피하라

## 자바의 두 가지 객체 소멸자

finalizer 와 cleaner 모두 에측할 수 없고, 느려 일반적으로 불필요하다. 

- finalizer
    - GC 가 사용되지 않는 객체를 제거할 때 호출되는 메서드
    - Java 9 에서 deprecated 됨
- cleaner
    - finalizer 의 대체로 등장

## 예측 불가능

- 즉시 수행된다는 보장이 없으므로 제때 실행되어야 하는 작업은 할 수 없다
    - ex) 파일 닫기
- 수행 여부조차 보장하지 않으므로 상태를 영구히 수정하는 작업 시 절대 사용하면 안된다
    - ex) 락 해제

## 성능 문제

- AutoCloseable 객체를 try-with-resources 로 닫는 방식 보다 몇 배나 느리다

## 보안 문제

- finalizer 사용한 클래스는 finalizer 공격에 노출 가능
    - ex) 생성자나 직렬화 과정에서 예외 발생 시, 생성되다 만 객체의 하위 클래스에서 finalizer 가 수행될 수 있게 한다. 이 finalizer 의 정적 필드에 자기 자신을 참조하여 GC 대상이 안되도록 한다.

## 해결책

- AutoCloseable 을 구현하고 close 메서드를 호출하면 된다
    - 일반적으로 try-with-resources 사용하여 예외 발생 시에도 닫도록 해준다

## finalizer 와 cleaner 사용하는 곳

- close 메서드를 사용하지 않는 것에 대한 안전망
- 네이티브 피어와 연결된 객체
