# Item 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

### public 클래스에 public 멤버를 사용

캡슐화의 이점을 사용할 수 없다.

- 불변성을 보장하지 못한다.
- API 수정 없이 내부 표현을 바꿀 수 없다.
- 외부 필드에서 부수작업을 수행할 수 없다.

→ 필드를 private하게 변경하고 getter/setter를 사용하자.

이를 어기고 있는 자바의 라이브러리에도 있다.

Point, Dimension 클래스인데 심각한 성능 문제가 발견되었다.

### public 클래스의 public final 멤버

위에서 말한 단점이 조금 줄어들지만 여전히 좋은 설계는 아니다.

- API를 변경해야 한다.
- 부수 작업을 수행할 수 없다.

### package-private 클래스 또는 private 중첩 클래스(이너클래스)

필드를 노출해도 위와 같은 문제가 생기지 않는다.

위의 방법보다 훨씬 깔끔하지만, 이 방법도 단점이 수반된다.

- 클라이언트 코드가 클래스 내부에 묶인다.
    - 클라이언트도 이 클래스의 포함하는 패키지에 포함되기 때문에 패키지 바깥으로 변경사항이 빠져나가지 않고 있다.

### 마무리

객체의 불변성은 어플리케이션의 품질, 최적화와 매우 밀접하다.

전 아이템과 비슷하지만 접근제한자를 선택할때 생각없이 하지 말고, 사용처에 따라 분류해야겠다.
