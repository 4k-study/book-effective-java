# Item 87. 커스텀 직렬화 형태를 고려해보라.

개발 일정이 타이트한 경우에는 동작만 하도록 하고, 다음 릴리즈에 리팩토링 하는 방법을 사용해보자. 이때 클래스가 Serializable를 사용하면, 영원히 발이 묶일 수 있다. (BigInteger)

따라서, 먼저 고민해보고 괜찮다고 판단이 되는 경우에만 Serializable를 사용하고, 그렇지 않다면 직접 설계하더라도 커스텀 Serializable를 사용해야한다.

단, 객체의 물리적 표현과 논리적 내용이 같다면 기본 직렬화 형태라도 무방하다.

```sql
public class Name implements Serializable {
	/** 
	*  문서화는 이렇게
	*  이름. null이 아니어야 한다.
	*  @serial
	**/
	private final String lastName;
	private final String firstName;
	private final String middleName;
}
```

- 물리적 표현 : 논리적으로 이름, 성, 중간 이름으로 구성.
- 논리적 표현 : 논리적 요소를 그대로 반영
- readObject 메서드 : 불변식 보장과 보안을 위해 제공할 때가 있다.

만일 객체의 물리적 표현과 논리적 내용이 다른데 기본 직렬화를 사용하면 어떤 문제가 있을까?

- public API가 현재 내부 표현 방식에 영구 종속된다.
- 너무 많은 디스크 공간을 차지할 수 있다. → StackOverflow 야기
- 그래프 순회로 시간이 오래걸릴 수 있다.

### 주의점

- 객체 직렬화 사용 여부와 상관없이 객체 전체 상태를 읽는 메서드에 동기화 메커니즘은 직렬화에도 적용해야 한다.
- 직렬화 되는 클래스에 직렬 버전 UID를 명시적으로 부여하자.
- 구 버전과 호환성을 끊을게 아니면 직렬 버전 UID는 수정하지 말자.
