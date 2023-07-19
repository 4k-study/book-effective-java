# Item 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴은 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

싱글턴 클래스는 이를 사용하는 클라이언트 테스트가 어려워진다. Mock 인스턴스로 대체할 수 없기 때문이다.

싱글턴은 하나의 인스턴스만 사용하기에 리소스를 절약하는 장점이 있지만, 상태를 가지는 경우 여러 스레드에서 상태를 변경할 수 있어 상태를 갖지 않는 경우에만 사용해야한다.

### 싱글턴을 구현하는 방법

public static final 필드

```java
public class Test {
	public static final Test INSTANCE = new Test();
	private Test() { ... }
}
```

이 경우 reflection을 사용해 생성자를 호출할 수 있다.

정적 팩토리

```java
public class Test {
	private static final Test INSTANCE = new Test();
	private Test() { ... }
	public static Test getInstance() { return INSTANCE; }
}
```

이 방법도 reflection을 사용해 생성자를 호출 할 수 있다. 하지만, 싱글턴이 아니게 바꿀 수 있고, 정적 팩토리를 제너릭 싱글톤 메서드로 변경할 수 있으며, 메서드를 Suppplier로 만들 수 있어 유연하다.

하지만 이 방법들은 역직렬화할 때 새로운 인스턴스가 만들어져 readResolve 메서드를 제공해야한다.

이넘 타입

```java
public enum Test {
	INSTANCE;
}
```

public 필드와 비슷하지만, 더 간결하고 reflection, 역직렬화에 대해 자유롭다.

### 마무리

싱글턴 패턴이 안티패턴임에도 사용되는 이유는 특정한 상황에선 정말 좋은 방법이기 때문이라고 생각한다.

보통 db커넥션, 로깅, config 등에서 쓰인다고 생각하는데 스프링을 사용하며 모두 지원해주기 때문에 직접 구현할 일이 있을까 생각이 들기도 하고, 제약을 가져가면서 reflection 까지 방어해야할 필요가 있을까라는 생각도 든다.
