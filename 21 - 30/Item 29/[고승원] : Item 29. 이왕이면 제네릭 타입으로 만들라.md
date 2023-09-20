# Item 29. 이왕이면 제네릭 타입으로 만들라

제네릭 타입을 사용하는건 쉽지만 만드는건 신경써야 하는 부분이 있다.

제네릭은 실체화 불가 타입이라 클래스를 작성할때 타입 반환해야 한다. 이 책에서는 Stack을 예시로 들었다.

### 1. 삽입시 형변환

```java
@SuppressWarnings("unchecked")
public Stack() {
	elements = (E[]) new Object[];
}
```

어? 이러면 타입 안정성이 떨어지지 않는가?

→ 그렇지 않다. elements는 외부에 반환되거나, 다른 메서드에서 사용되지 않기 때문이다.

하지만 경고 메시지는 있기 때문에 `@SuppressWarnings` 을 붙여야 한다.

### 2. 반환시 형변환

```java
public E pop() {
	if (size = 0) 
		throw new EmptyStackException();

	@SuppressWarnings("uncheked")
	E result = (E) elements[--size];

	elements[size] = null;
	return result;
}
```

생성자에서 형변환을 하지 않고, 원소를 형변환 하는 방법이다.

1번 방법과 동일하게 타입 안전성은 컴파일 시점에 보장되어 ClassCastException으로부터 자유롭다. 하지만 경고가 발생하기 때문에 `@SuppressWarnings` 을 붙인 모습이다.

### 마무리

두가지 방법 모두 경고를 피할 수 없는 방법이다.

두가지 방법의 공통점은 그 경고를 지우는 `@SuppressWarnings` 어노테이션을 가능한 작은 범위에 사용했다는 점이 있다.

차이점으로는 1번 방법은 생성자에만 쓰면 되기 때문에 간결하고, 직관적이지만 힙 오염이 야기된다.

반면에 2번 방법은 여기선 한가지 메서드지만, API로 원소를 주고 받는 경우마다 형변환이 필요해 보인다.

*  힙 오염

ex List<String> list 를 Object object로 업캐스팅하고, List<Integer> list2로 다운캐스팅 하는 경우
