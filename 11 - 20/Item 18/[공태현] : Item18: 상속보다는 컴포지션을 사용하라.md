## 다른 패키지의 구체 클래스를 상속하는 것은 위험하다.

- 상속은 캡슐화를 깨뜨린다.
    - 상위 클래스의 변경이 하위 클래스의 동작에 영향을 미친다.
        - 하위 클래스에서 상위 클래스의 메소드를 사용하여 원하는 기능을 구현했는데, 상위 클래스의 메소드가 변경됨
        - 하위 클래스에서 상위 클래스의 메소드를 재정의했는데, 새로운 릴리즈에서 상위 클래스에 똑같은 메소드 시그니처를 가졌지만 반환타입이 다른 메소드가 추가됨 → 컴파일 오류
        - 상위 클래스의 특정 기능 수행하는 메소드를 하위 클래스에서 재정의 하여 제어했는데, 새로운 릴리즈에서 특정 기능을 수행하는 새로운 메소드가 추가됨 → 하위 클래스에서 또 제어해줘야 함

```java
public class MySet<E> extends HashSet<E> {

    private int count = 0;

    @Override
    public boolean add(E e) {
        System.out.println("add 호출");
        count++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        System.out.println("addAll 호출");
        count += c.size();
        return super.addAll(c);
    }

    public int getCount() {
        return count;
    }
}
```

`MySet`은 `HashSet`을 상속받은 클래스이다. 재정의한 `add()` 메소드는 호출 시 `count` 값을 1씩 증가시키고, `addAll()`은 호출 시 `count` 값을 인자로 들어온 컬렉션의 원소 수만큼 증가시킨다.

```java
public class Main {
    public static void main(String[] args) {
        MySet<Integer> set = new MySet<>();

        set.add(1);

        set.addAll(List.of(1, 2));

        System.out.println(set.getCount());
    }
}
```

이 상태에서 위 코드를 실행하면 결과는 의도치 않게 나온다.

```java
add 호출
addAll 호출
add 호출
add 호출
5
```

왜냐하면 `HashSet`의 `addAll()` 메소드는 내부적으로 `add()` 메소드를 실행하기 때문이다.

```java
public boolean addAll(Collection<? extends E> c) {
    boolean modified = false;
    for (E e : c)
        if (add(e))
            modified = true;
    return modified;
}
```

즉, 하위 클래스가 상위 클래스의 구현에 크게 의존하고 있다.

## 컴포지션을 사용하자

### 컴포지션

<aside>
💡 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 `private` 필드로 기존 클래스의 인스턴스를 참조하는 방식

</aside>

### 사용 이유

- 컴포지션을 사용하면 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향을 받지 않는다.

```java
public class MySet<E> {

    private HashSet<E> set;
    private int count = 0;

    public MySet(HashSet<E> set) {
        this.set = set;
    }

    public boolean add(E e) {
        System.out.println("add 호출");
        count++;
				return set.add(e);
    }

    public boolean addAll(Collection<? extends E> c) {
        System.out.println("addAll 호출");
        count += c.size();
        return set.addAll(c);
    }

    public int getCount() {
        return count;
    }
}
```

`MySet`을 상속 대신 컴포지션을 사용하도록 변경했다.

```java
public class Main {
    public static void main(String[] args) {
        MySet<Integer> set = new MySet<>(new HashSet<>());

        set.add(1);

        set.addAll(List.of(1, 2));

        System.out.println(set.getCount());
    }
}
```

그리고 같은 코드를 실행했다.

```java
add 호출
addAll 호출
3
```

결과는 의도한대로 동작했다.

즉, 상속대신 컴포지션을 사용함으로써 `HashSet`의 기능을 사용하는 하위 클래스에서 `HashSet`의 내부 구현에 영향을 받지 않는다.

## 상속을 사용해야 하는 경우

### 다형성을 이용해야 하는 경우에 사용한다.

- 클래스 B가 클래스 A와 is-a 관계일 때만 A를 상속해야 한다.
