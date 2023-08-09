# Item 14. Comparable을 구현할지 고려하라

Comparable 인터페이스의 유일한 메서드 compareTo이다. 이 점을 제외한다면 equals와 매우 유사하다.

compareTo 일반규약. (객체가 비교대상보다 작으면 음수, 같으면 0, 크면 양수를 반환)

- sgn(x.compareTo(y)) == -sgn(y.compareTo(x))
- x.compareTo(y) > 0 && y.compareTo(z) > 0 이면 x.compareTo(z)
- x.compareTo(y) == 0 이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z))

equals와 매우 유사하다고 느껴지는데, Class가 다르면 Exception을 던지면 되서 조금 더 편하다.

compareTo 메서드를 재정의할 때 <, >과 같은 비교연산자를 사용하지 말아야 한다. compare 메서드나 Comparator 인터페이스가 제공하는 메서드를 사용하자.

### 마무리

바로 직전에 Cloneable을 보다가 Comparable처럼 함수형 인터페이스는 상당히 직관적이고 람다도 사용할 수 있어서 활용성이 좋다는 생각이 든다.
