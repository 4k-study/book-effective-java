### 타입 제한

- 추상 클래스의 타입에 포함되기 위해서는 반드시 추상 클래스를 구현한 하위 클래스여야 함
- 인터페이스는 다중 상속이 가능하기 때문에 해당 인터페이스의 구현 규칙을 지키기만 한다면 어떤 클래스라도 인터페이스의 타입에 포함될 수 있음

### 기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.

- 서로 다른 두 클래스 A, B가 있을 때, 이 두 클래스가 모두 C를 상속받길 원한다면 C는 A 와 B 모두의 공통 조상이어야 한다.
    - 새로 추가된 추상 클래스의 모든 자손이 이를 상속하게 된다. → (인터페이스도 마찬가지 아닌가??)
- 추상클래스는 기존 클래스에 새로운 추상클래스를 상속받게 하는게 어려움
    - 기존 클래스가 이미 다른 클래스를 상속받고 있다면?
- 인터페이스는 기존 클래스에 손쉽게 새로운 인터페이스를 구현하기 쉬움
    - 구현하려는 인터페이스에서 요구하는 메소드만 추가하면 됨

### 인터페이스는 믹스인 정의에 안성맞춤이다.

<aside>
💡 믹스인이란, 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 주된 타입 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.

</aside>

- 추상 클래스는 믹스인을 정의하기 힘들다. 왜냐하면 다중 상속이 불가능하기 때문에, 자신의 부모 타입 외에 다른 타입을 가질 수 없기 때문이다.
- 인터페이스는 믹스인을 정의하기 쉽다. 왜냐하면 다중 상속이 가능하기 때문에 하위 클래스는 단순히 인터페이스를 구현하기만 하면 해당 인터페이스의 타입을 얻을 수 있기 때문이다.

### 계층 구조가 없는 타입 프레임워크

- 계층 구조를 엄격하게 구분하기 어려운 여러 상황이 존재한다.

```java
public interface Singer {
	AudioClip sing(Song s);
}

public interface Songwriter {
	Song compose(int chartPosition);
}
```

위와 같이, 가수를 의미하는 `Singer` 인터페이스와 작곡가를 의미하는 `Songwriter` 인터페이스가 있다.

```java
public class Musician implements Singer, Songwriter { ... }
```

`Singer`도 가능하고, `Songwriter`도 가능한 클래스가 있는 경우, 인터페이스를 사용하면 위와 같이 간단하게 처리할 수 있다.

```java
public abstract class Singer {
	AudioClip sing(Song s);
}

public abstract class Songwriter {
	Song compose(int chartPosition);
}
```

반면에 추상클래스로 구현한다면

```java
public abstract class SingerSongwriter {
	AudioClip sing(Song s);
	Song compose(int chartPosition);
}
```

위와 같이 `SingerSongwriter`라는 새로운 클래스를 정의해줘야 한다(이것도 다중 상속이 안되기 때문). 이는 많은 기능을 포함하는 뚱뚱한 클래스가 만들어질 우려가 있다.

### 인터페이스 추상 골격 클래스

```java
public interface Phone {
  void booting();
  void greeting();
  void shutdown();
  void process();
}
```

```java
public class IPhone implements Phone {

  @Override
  public void booting() {
    System.out.println("booting ...");
  }

  @Override
  public void greeting() {
    System.out.println("I am iPhone");
  }

  @Override
  public void shutdown() {
    System.out.println("shut down ...");
  }

  @Override
  public void process() {
      booting();
      greeting();
      shutdown();
  }
}
```

```java
public class GalaxyPhone implements Phone {
  @Override
  public void booting() {
    System.out.println("booting ...");
  }

  @Override
  public void greeting() {
    System.out.println("I am galaxy phone");
  }

  @Override
  public void shutdown() {
    System.out.println("shut down ...");
  }

  @Override
  public void process() {
    booting();
    greeting();
    shutdown();
  }
}
```

`Phone`을 구현한 `IPhone`클래스와 `GalaxyPhone` 클래스를 보면 `booting()`, `shutdown()` 메소드가 모두 같다.

이 경우 다음과 같이 인터페이스 추상 골격 클래스를 통해 중복을 제거할 수 있다.

```java
// 추상골격 구현 클래스
public abstract class AbstractPhone implements Phone {

	// 공통 메소드 구현
  @Override
  public void booting() {
    System.out.println("booting ...");
  }

  @Override
  public void shutdown() {
    System.out.println("shut down ...");
  }

  @Override
  public void process() {
    booting();
    greeting();
    shutdown();
  }
}
```

중복되었던 `booting()`, `shutdown()` 메소드를 추상 골격 구현 클래스에 정의한다. 

```java
public class IPhone extends AbstractPhone implements Phone {

  @Override
  public void greeting() {
    System.out.println("I am iPhone");
  }
}
```

그리고 이를 상속받은 `IPhone`은 `AbstractPhone`만 구현하면 중복을 제거할 수 있다.
