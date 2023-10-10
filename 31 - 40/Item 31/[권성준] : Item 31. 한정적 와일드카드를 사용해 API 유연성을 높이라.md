# Item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

## 개요

매개변수화 타입은 불공변이다. 아래 코드처럼 때론 불공변 방식보다 유연한 방식이 필요하다. 

```java
public class Stack<E> {
	public Stack();
	public void push(E e);
	public E pop();
	public boolean isEmpty();
}

public void pushAll(Iterable<E> src) {
	for (E e : src) {
			push(e);
	}
}
```

위 코드는 컴파일되지만 완벽하지 않은 코드다. Stack<Number> 로 선언하고, pushAll 에 Integer 타입을 넣으면 논리적으로는 동작해야하지만 동작하지 않는다.

이럴 때 사용할 수 있는 방법을 알아보자.

## 한정적 와일드카드

- PECS : producer - extends, consumer - super
    - 매개변수화 타입 T 가 생산자라면 <? extends T> 를 사용
    - 매개변수화 타입 T 가 소비자라면 <? super T> 를 사용
- 메서드 반환 값에 한정적 와일드카드를 사용하지 말아야 함
    - 클라이언트 코드에 영향을 주기 때문
