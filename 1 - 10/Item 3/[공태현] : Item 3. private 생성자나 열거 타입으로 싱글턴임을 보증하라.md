## 싱글턴

- 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.
    - 싱글턴 인스턴스를 mock 구현할 수 없기 때문

### 구현 방식

1. **`public static final` 필드 방식**

```java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() {...}
}
```

- 생성자가 `private`이기 때문에 `Elvis` 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.
- 예외는 리플렉션 API를 사용해서 `private` 생성자를 호출할 수 있다.
    - 이는 생성자에서 예외처리를 통해 해결해야 한다.
- 해당 클래스가 싱글턴임이 API에 명백히 들어난다.
    - `public static` 필드가 `final`이니 절대로 다른 객체를 참조할 수 없다.
- 간결하다.

2. **정적 팩토리 방식**

```java
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() {...}
	public static Elvis getInstance() { return INSTANCE; }
}
```

- 1번 방식과 리플렉션에 의한 예외처리는 같음
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 장점이 있음
    - 메서드를 수정해주면 됨
- 원한다면 정적 팩토리를 제네릭 싱글톤 팩토리로 만들 수 있다.
    - 제네릭 메소드를 사용하여 타입 안정성을 더할 수 있음
- 팩토리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다.
    - `Elvis::getInstance`로 사용 가능

3. **원소가 하나인 열거 타입을 선언 - 바람직한 방법**

```java
public enum Elvis {
	INSTANCE;
}
```

- 가장 간결하고 추가 노력 없이 직렬화할 수 있음
- 리플렉션에도 문제 없다.
- 대부분에 상황에서 가장 좋은 방식임
- 만들려는 싱글턴이 `Enum`외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없음

### 직렬화

- 싱글톤 클래스를 직렬화 하기 위해서는 `Serializable`을 구현한 것만으로는 부족하다.
- 모든 인스턴스 필드를 일시적이라고 선언하고 `readResolve` 메소드를 제공해야 한다.
    - 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.
- 열거 타입 선언 방식에는 해당되지 않는다.

```java
private Object reaResolve() {
	return INSTANCE;
}
```
