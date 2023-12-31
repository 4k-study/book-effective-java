# Item 10. equals는 일반 규약을 지켜 재정의하라

equals 메서드는 재정의 하지 않는 것이 최선이다. 하지만, 논리적 동치성을 비교하고자 재정의 하는 경우 Override를 염두하고 만들어졌기에 일반 규약을 지켜서 재정의 해야한다. 그렇지 않다면, 다른 클래스를 오동작하게 할 수 있다.

equals 메서드의 일반 규약

- 반사성 - null이 아닌 참조값 x 에 대해 x.equals(x) 는 항상 true
- 대칭성 - x.equals(y)면 y.equals(x)
- 추이성 - x.equals(y), y.equals(z)가 true 면 z.equals(x) 도 true
- 일관성 - 같은 메서드를 반복해도 항상 같은 결과를 반환한다.
- null 아님 - null이 아닌 모든 값에 x.equals(null)은 false다.

equals 메서드의 규약을 지켜가면서 재정의 하려면 어떻게 해야할까?

1. == 연산자로 자신 참조인지 확인
2. instanceof로 타입 확인
3. 형변환
4. 핵심 “필드”가  일치하는지 확인

이때 핵심 필드는 변경될 수 있거나 외부 자원이 아니어야 한다.

최근에는 IDE에서나 AutoValue같은 프레임워크가 잘되어있어 도움을 받는 것도 좋은 선택이다.

### 결론

equals 메서드의 재정의가 필요하지 않은 경우에는 하지 않아도 된다.

재정의가 필요한 경우에는 일반 규약을 지켜야 한다.

실수 확률이 없는 외부 프레임워크를 사용하는 것도 좋다.

### 마무리

equals 메서드의 규약 자체가 있는지 모르고 사용해 왔지만, 신기하게도 책에서 말하는 조건들을 모두 지키고 사용하고 있었다. 최근에는 record 클래스를 사용하기 때문에 equals를 사용할 일이 더더욱 사라졌다.

앞으로 재정의할일이 있을텐데 그때도 규약에 맞게 재정의하자.
