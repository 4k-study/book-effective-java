## `hashCode()` 관련 규약

- `equals()` 비교에 사용하는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 `hashCode()` 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다.
- `equals()`가 두 객체를 같다고 판단했다면, 두 객체의 `hashCode`는 똑같은 값을 반환해야 한다.
- `equals()`가 두객체를 다르다고 판단했더라도, 두 객체의 `hashCode()`가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

## `hashCode()`의 사용

![image](https://github.com/4k-study/book-effective-java/assets/68289543/1c6a21f8-cd0e-4818-bb33-c78d9197328b)

`hashCode()`는 Java의 `HashMap`, `HashSet` 등의 자료구조에서 객체가 논리적으로 같은지 비교할 때 사용된다. 즉, 서로 다른 `hashCode()`를 가지는 객체는 `Hash` 컬렉션에서 서로 다른 객체로 인정된다.

## 안좋은 `hashCode()`

`Object`에서 기본적으로 제공하는 `hashCode()` 메소드의 경우 객체마다 다른 정수를 반환하도록 정의되어 있다. (일반적으로 객체의 메모리 주소를 반환하도록 구현한다.)

```java
public class Data {

    private String name;
    private int age;

    public Data(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) return true;
        if (!(obj instanceof Data)) return false;
        Data d = (Data) obj;
        return Objects.equals(this.name, d.name) && this.age == d.age;
    }
}
```

`hashCode()`를 재정의하지 않은 위 `Data` 객체의 경우를 보자.

```java
public static void main(String[] args) {
    Data d1 = new Data("Kong", 1);
    Data d2 = new Data("Kong", 1);

    System.out.println(d1.hashCode());
    System.out.println(d2.hashCode());
}
```

서로 다른 2개의 인스턴스를 만들고 `hashCode()` 값을 찍어보면

```java
1324119927
990368553
```

위와 같이 서로 다르게 나온다.

```java
public static void main(String[] args) {
    Data d1 = new Data("Kong", 1);
    Data d2 = new Data("Kong", 1);

    Map<Data, Integer> map = new HashMap<>();
    map.put(d1, 1);
    map.put(d2, 2);

    System.out.println(map);
}
```

따라서 앞서 생성한 2개의 `Data` 인스턴스를 `HashMap`에 키로 넣으면 2개의 값이 들어간다.

```java
{Data{name='Kong', age=1}=1, Data{name='Kong', age=1}=2}
```

논리적으로 `d1`, `d2`는 같은 객체이기 때문에 `HashMap`에 저장할 때 하나의 키로 다뤄지길 원할 것이다.

```java
public class Data {

		// 생략

    @Override
    public int hashCode() {
        return 1;
    }
}
```

이 문제를 해결하기 위해 위와같이 `Data` 클래스에 `hashCode()`를 재정의하고 1을 반환하도록 하여 `Hash` 컬렉션에 넣었을 때 `equals()`만으로 객체의 동치성을 확인하도록 만들 수 있다.

```java
System.out.println(map);
```

위와 같이 구현하고 다시 `map`을 출력하면

```java
{Data{name='Kong', age=1}=2}
```

의도한대로 하나의 인스턴스만 `map`에 저장된다.

하지만 위 방법에는 치명적인 단점이 존재한다. `HashMap`에 존재하는 모든 데이터의 해시값이 같기 때문에 데이터를 삽입하거나 검색할 때 연결리스트처럼 저장된 모든 데이터를 탐색해야하고 이는 `Hash` 자료구조를 사용하는 이점을 상쇄하게 된다.

```java
public static void main(String[] args) {
    Map<Data, Integer> map = new HashMap<>();

    long start = System.currentTimeMillis();

    for (int i = 0; i < 100000; i++) {
        map.put(new Data("Kong", i), i);
    }

    Integer kong = map.get(new Data("Kong", 1));

    long end = System.currentTimeMillis();

    System.out.println(end - start);
}
```

10만개의 데이터를 `HashMap`에 넣고 조회하는 연산을 했을 때 `Data`의 `hashCode()`가 항상 같은 값만 반환하는 경우 시간을 측정해보면

```java
46749
```

시간이 매우 오래 걸렸다.

```java
23
```

객체마다 서로다른 `hashCode()`를 가지게 했을 때 똑같은 코드를 돌려보면 확연히 차이가나는 시간이 걸렸다.

## 좋은 `hashCode()`

1. `int` 변수 `result`를 선언한 후 값 `c`로 초기화한다. 이때, `c`는 해당 객체의 첫 번째 핵심 필드를 단계 2.a 방식으로 계산한 해시코드다.
2. 해당 객체의 나머지 핵심 필드 `f` 각각에 대해 다음 작업을 수행한다.
    1. 해당 필드의 해시코드 `c`를 계산한다.
        1. 기본 타입 필드라면, `Type.hashCode(f)`를 수행한다. `Type`은 기본 타입의 박싱 클래스이다.
        2. 참조 타입 필드면서 이 클래스의 `equals()` 메소드가 이 필드의 `equals()`를 재귀적으로 호출해 비교한다면, 이 필드의 `hashCode()`를 재귀적으로 호출한다. 계산이 더 복잡해질 것 같으면, 이 필드의 표준형을 만들어 그 표준형의 `hashCode()`를 호출한다. 필드의 값이 `null`이면 0을 사용한다.
        3. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룬다. 이상의 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산한 다음, 단계 2.b 방식으로 갱신한다. 배열에 핵심 원소가 하나도 없다면 단순히 상수를 사용한다. 모든 원소가 핵심 원소라면 `Arrays.hashCode()`를 사용한다.
    2. 단계 2.a에서 계산한 해시코드 `c`로 `result`를 갱신한다. 
        1. `result = 31 * result + c;`
3. `result`를 반환한다.

- 이때, `equals()` 비교에 사용되지 않은 필드는 반드시 제외해야 한다.
- 단계 2.b의 곱셈 31 * `result`는 필드를 곱하는 순서에 따라 `result` 값이 달라지게 한다.
    - 그 결과 클래스에 비슷한 필드가 여러 개일 때 해시 효과를 크게 높여준다.
- `Objects.hash()` 메소드를 사용할 수도 있지만 성능이 조금 느리기 때문에 성능이 중요한 환경에서는 사용을 고려해야 한다.
- 불변 객체라면 객체가 생성될 때 해시값이 계산되고, 이를 캐싱해서 사용하여 성능을 향상 시킬 수 있다.
- 성능때문에 해시코드를 계산할 때 핵심 필드를 생략해서는 안 된다.
    - 해시 품질이 나빠져 해시테이블으 성능을 떨어뜨릴 수 있다.
