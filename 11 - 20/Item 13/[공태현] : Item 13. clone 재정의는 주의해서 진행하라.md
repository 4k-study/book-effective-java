## `clone()`이란 ?

<aside>
💡 해당하는 객체의 모든 필드를 복사하여 새로운 객체에 넣어 반환하는 동작을 수행한다.

</aside>

```java
protected native Object clone() throws CloneNotSupportedException;
```

`Object.clone()` 메소드는 위와 같이 네이티브 메소드로 정의되어 있다. 

참고로, `native` 키워드는 네이티브 메소드를 선언하는 키워드이다. Java 프로그램에서 C, C++과 같은 다른 언어로 작성된 메소드를 호출할 수 있게 해준다. 

## `Cloneable`

```java
public interface Cloneable {}
```

`Cloneable` 인터페이스는 위와 같이 아무 메소드도 정의되어 있지 않은데, `Object.clone()` 메소드를 재정의 하기 위해서는 `Cloneable` 인터페이스를 꼭 구현해줘야 한다. 

```java
public class Data implements Cloneable {

    private String name;
    private int age;

    public Data(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

`Cloneable`을 구현한 `Data` 클래스에서 `clone()` 메소드를 재정의하였다.

```java
public static void main(String[] args) {
    Data data = new Data("Kong", 21);

    Object clone = null;
    try {
        clone = data.clone();
    } catch (CloneNotSupportedException e) {
        System.out.println("CloneNotSupportedException 발생");
    }

    System.out.println(clone);
}
```

```java
Data{name='Kong', age=21}
```

그리고 메소드를 호출하면 정상적으로 동작한다.

```java
public class Data {

    private String name;
    private int age;

    public Data(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

`Cloneable`을 재정의하지 않고 동일하게 호출한다면

```java
CloneNotSupportedException 발생
null
```

`CloneNotSupportedException`이 발생한다.

### 특징

- `Object.clone()` 메소드의 동작 방식을 결정한다.
- `Cloneable`을 구현한 클래스의 인스턴스에서 `clone()`을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환한다.
    - `Cloneable`을 구현하지 않으면 앞서 보았듯이 `CloneNotSupportedException`이 발생한다.

## 일반 규약

다음은 `Object` 명세에 명시되어 있는 `clone()` 메소드의 일반 규약이다.

- `x.clone() != x`
- `x.clone().getClass() == x.getClass()`
- `x.clone().eqausl(x)`
- 반환된 객체와 원본 객체는 독립적이어야 한다.

### 예시

```java
public class Data implements Cloneable {

    private String name;
    private int age;
    private List<Integer> list;

    public Data(String name, int age) {
        this.name = name;
        this.age = age;
        list = new ArrayList<>();
    }

    public void addToList(int v) {
        list.add(v);
    }

    @Override
    protected Data clone() {
        try {
            return (Data) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError("CloneNotSuppoertedException 발생");
        }
    }
}
```

위와 같이 `Data` 클래스가 있다. `Data` 클래스는 상위 클래스의 `clone()` 메소드를 그대로 사용한다.

```java
public static void main(String[] args) {
    Data data = new Data("Kong", 21);
    Data clone = data.clone();

    data.addToList(10);

    System.out.println(data);
    System.out.println(clone);
}
```

`data`와 `data`를 `clone()`한 `clone`이 있을 때 `data`의 `list`에 10이라는 값을 넣는 메소드를 호출한다.

```java
Data{name='Kong', age=21, list=[10]}
Data{name='Kong', age=21, list=[10]}
```

그러면 위와 같이 `clone`의 `list`에도 값이 들어간다.

즉, `Object.clone()` 메소드를 호출했을 때 참조타입 필드는 원본 객체와 복제된 객체가 서로 같은 필드 객체를 참조하게 된다.

이 문제를 해결하기 위해 다음과 같이 `clone()` 메소드를 재정의할 수 있다.

```java
@Override
protected Data clone() {
    try {
        Data result = (Data) super.clone();
        result.list = (List<Integer>)((ArrayList) this.list).clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError("CloneNotSuppoertedException 발생");
    }
}
```

```java
Data{name='Kong', age=21, list=[10]}
Data{name='Kong', age=21, list=[]}
```

위와 같이 `clone()` 메소드를 재정의하고 같은 메소드를 실행했을 때 원하던 결과를 얻을 수 있다.
