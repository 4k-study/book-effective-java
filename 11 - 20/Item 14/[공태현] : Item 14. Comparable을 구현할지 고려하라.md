## `Comparable`이란?

<aside>
💡 `compareTo()` 추상메소드가 정의되어 있는 인터페이스로 객체를 비교할 수 있게 해준다.

</aside>

- `Comparable`을 구현한 객체는 정렬 컬렉션이나 제네릭 알고리즘의 기능을 제공받을 수 있다.
- 객체 간의 비교가 필요하다면 `Comparable`을 구현하자

### 일반 규약

- 호출한 객체가 인자로 들어온 객체보다 작으면 음의 정수를, 같으면 0을, 크면 양의 정수를 반환한다.
- 이 객체와 비교할 수 없는 객체가 인자로 들어오면 `ClassCastException`을 던진다.
- `x.compareTo(y)`가 예외를 던진다면 `y.comapreTo(x)`도 예외를 던진다.
- `x.compareTo(y) > 0`이고, `y.compareTo(z) > 0`이라면 `x.compareTo(z) > 0`이다.
- `x.compareTo(y) == 0`이면, `x.compareTo(z) == y.compareTo(z)`이다.
- `x.compareTo(y) == 0`이면, `x.equals(y) == true`여야 한다.
    - 필수는 아니지만 권고사항이다.

### 특징

- `equals()` 재정의와 마찬가지로 기존 클래스를 확장한 구체 클래스에서 새로운 값 컴포넌트를 추가했다면 `compareTo()` 규약을 지킬 수 없다.
- 정렬 클래스(`TreeSet`, `TreeMap`)는 `compareTo()`로 비교하지만 `Hash`클래스는 `equals()`로 비교한다.
- `Comparable`은 타입을 인수로 받는 제네릭 인터페이스이기 때문에 `compareTo()` 메소드의 인수 타입은 컴파일타임에 정해진다. → 형변환할 필요가 없음
- 정수 기본 타입을 비교할 때 `compare()` 메소드를 사용하자
- 핵심 필드를 먼저 비교하자
