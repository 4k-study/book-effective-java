```java
public class Stack {
	private Object[] elements;
	private int size = 0;
	private static final int DEFAULT_INITIAL_CAPACITY = 16;

	public Stack() {
		elements = new Object[DEFAULT_INITIAL_CAPACITY];
	}

	public void push(Object o) {
		ensureCapacity();
		elements[size++] = e;
	}

	public Object pop() {
		if (size == 0) {
			throw new EmptyStackException();
		}
		return elements[--size];
	}

	private void ensureCapacity() {
		if (elements.length == size) {
			elements = Arrays.copyOf(elements, 2 * size + 1);
		}
	}
}
```

위 `Stack`을 사용하게되면 사용하지 않는 객체지만 참조를 해제하지 않아 GC가 동작하지 않게된다. `pop()` 메서드를 보면 객체를 반환하고 `Stack`의 사이즈를 감소하지만 `elements` 배열 안에는 여전히 객체가 존재하게된다. 

```java
public Object pop() {
	if (size == 0) {
		throw new EmptyStackException();
	}

	Object result = elements[--size];
	**elements[size] = null;**
	return result;
}
```

따라서 위와 같이 `pop()` 메소드로 인해 반환된 객체에 대한 참조를 `null` 처리 해줌으로써 GC 대상이 되도록 해야 한다.

### 모든 객체를 다 사용하자마자 null 처리?

- 프로그램이 지저분해진다.
- 객체 참조를 `null` 처리하는 것은 예외적인 경우여야 한다.
- 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위 밖으로 밀어내는 것

### 메모리 누수를 주의해야할 경우

- 자기 메모리를 직접 관리하는 클래스
    - 원소를 다 사용한 즉시 그 원소가 참조한 객체들을 다 `null` 처리 해줘야 한다.
- 캐시
    - 객체 참조를 넣어두고 객체를 다 쓴 뒤에도 한참을 그냥 놔둔 경우
    - **해결**
        - `WeakHashMap` 사용 : 외부에서 키를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 경우 사용할 수 있다. (사용된 키의 참조가 해제되면 CG 대상이 된다.)
        - 가치가 떨어지는 엔트리를 제거해주는 방법
            - `ScheduledThreadPoolExecutor`같은 백그라운드 스레드를 활용하거나 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행한다.
                - `LinkedHashMap`같은 경우 `removeEldestEntry()` 메서드를 사용하여 후자의 방법으로 처리한다.
- 리스너 혹은 콜백
    - 클라이언트가 콜백을 등록만 하고 해지하지 않는다면 콜백이 계속 쌓이게된다.
        - 콜백을약한 참조로 저장하여 GC 대상이 되도록한다.
