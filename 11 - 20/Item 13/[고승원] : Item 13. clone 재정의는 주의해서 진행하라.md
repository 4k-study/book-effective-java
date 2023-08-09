# Item 13. clone 재정의는 주의해서 진행하라

Cloneable은 복제해도 되는 클래스임을 나타낸다. Cloneable은 인터페이스지만, Object에 clone 메서드가 있다는 점이 웃프다.

이렇게 실제 기능상의 목적이 아닌, Clone이 가능하다는 식으로 알려주기 위해 사용하는 인터페이스를 marker interface라고 한다.

clone 메서드의 일반 규약은 정말 간단하다.

- 객체의 복사본을 생성해 반환한다.
    - x.clone() ≠ x
    - x.clone().getClass() == x.getClass()
    - x.clone().getClass() == x.getClass()
    - x.clone().equals(x) == true (필수는 아니다.)

여기서 생기는 문제는 다음과 같다.

```java
class A {
	clone() { ... }
	...
}

class B extends A {
	clone() { super.clone() }
	...
}

public void example() {
	B b = new B();
	B cloneB = (B) b.clone()
```

이 상황에서 A와 B의 모든 필드가 불변하다면 괜찮지만 아니라면 cloneB는 B가 아닌 A가 되어 문제가 생긴다.

가변객체의 경우 같은 값을 참조하기 때문에 정합성이 맞지 않는다. 따라서 객체의 모든 부분을 복사해줘야 한다.

모든 부분을 복사하기 위해 여러가지 방법이 있으니 알아서 하면 된다.

### 결론

- Cloneable을 구현한 클래스는 clone 메서드를 구현해야한다.
- 부모 타입에 대한 방어가 있어야 하고, 자신과 동일한 반환 타입이 있어야 한다.
- 기본타입과 불변객체로만 이루어지지 않은 클래스는 가변객체를 신경써야 한다.
- 복사팩터리나 복사생성자를 사용하면 더 편할 수 있다.

### 마무리

Cloneable이라는 인터페이스를 처음 알았는데 marker interface라는거도 처음 알게 되었다. 왜 있는걸까? 잘못된 설계를 만회하려는 건가

clone 메서드의 규약은 간단하지만, equals, hashCode보다 더 오버라이드하기 까다로운 것 같다.
