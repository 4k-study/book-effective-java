# Item 52. 다중 정의는 신중히 사용하라

오버로딩은 컴파일 타임에 어떤 메서드를 사용할지 정해지기 때문에 개발자가 의도한바와 다르게 처리될 수 있다.

반면에 오버라이딩은 런타임에 정적으로 선택되기 때문에 의도와 크게 달라지지 않는다.

여기서 문제점은 오버로딩된 메서드의 매개변수 타입이 같은 경우에 조심해야 한다. (상속, 구현 하는 관계도 포함)

이렇게 예측하기 어려운 상태의 메서드를 사용하지 않는 것이 좋은데 이를 피하기 위해선 매개변수별로 메서드를 분리하는 것도 하나의 방법이다. (ex. methodString, methodInt ..)

생성자는 이름을 변경할 수 없기 때문에 정적 팩터리 메서드등을 활용하자.

오버로딩할 때 헷갈리지 않으려면 매개변수가 관계가 없어야 한다 (어떠한 방법으로 형변환이 불가능한 경우)

메서드 참조를 사용할때도 주의해야 하는데, 참조와 같은 인수 표현식은 타입이 선택되기 전에는 그 의미가 정해지지 않기 때문이다.

이게 무슨 말이냐면 다른 함수형 인터페이스를 사용하더라도, 인수의 위치가 같으면 혼란이 생긴다는 뜻이다.

여기 또한 관계 없는 클래스는 괜찮다.

### 마무리

- 다중 정의된 메서드는 직접 작성한 코드더라도, 모두 이해하고 사용하기 어렵다. 오버로딩은 자제하자.
- 오버로딩은 같은 기능은 괜찮지만, 기능이 달라지는 경우 지침을 어기게 된다.
- 같은 기능이면 오버로딩할 필요가 없다.
