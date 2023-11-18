# Item 50. 적시에 방어적 복사본을 만들라

자바는 비교적 안정적인 언어이다. 그럼에도 항상 불변을 보장하진 않아서 방어적 프로그래밍을 해야한다.

```sql
public final class Period {
	private final Date start;
	private final Date end;

	public Period(Date start, Date end) {
		this.start=start;
		this.end=end;
	}
```

위 클래스는 불변을 보장할까? 언뜻 보기엔 보장해 보이지만, 그렇지 않다.

Date클래스가 가변이기 때문이다.

Date를 사용하지 않는게 가장 좋은 방법이지만, 어쩔수 없다면 어떻게 방어해야 할까?

1. 생성시 방어적 복사 (생성자에서 clone() 메서드를 사용하는 것은 위험할 수 있다.)

```sql
public final class Period {
	private final Date start;
	private final Date end;

	public Period(Date start, Date end) {
		this.start=new Date(start.getTime());
		this.end=new Date(end.getTime());
	}
```

1. 수정시 방어적 복사

```sql
public Date start() {
	return new Date(start.getTime());
}

public Date end() {
	return new Date(end.getTime());
}
```

### 불변 객체가 필요할 때

- 자료구조에 저장한 뒤 잠재적으로 변경될 여지가 있을때
- 내부에서 사용하는 배열을 반환할 때

### 불변 객체를 사용하지 않을 때

- 컴포넌트 내부를 수정하지 않을거라 확신할 때(+성능 저하가 야기될때)
- 클라이언트와 상호 신뢰가 있을 때
