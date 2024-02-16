# Item 88. readObject 메서드는 방어적으로 작성하라.

불변 클래스를 만드는데 가변 필드를 만들면 불변을 유지하기 위해 방어적 복사 코드를 작성해야 한다.

예시 코드와 함께 보자

```java
public final class Period {
	private final Date start;
	private final Date end;

	/**
	* @param start : 시작 시각
	* @param end : 종료 시각 / 시작 시각보다 뒤여야 한다.
	* @throws IllegalArgumentException : 시작 시각이 종료 시각보다 늦을 때 발생 한다.
	* @throws NullPointerException : start나 end가 null이면 발생 한다.
	**/
	public Period(Date start, Date end) {
		this.start = new Date(start.getTime());
		this.end = new Date(end.getTime());
		if(this.start.compareTo(this.end) > 0)
			throw new IllegalArgumentException (
				start + "가 " + end + "보다 늦다.");
	}

	public Date start() {return new Date(start.getTime()); }
	public Date end() {return new Date(end.getTime()); }
	public String toString() {return start + " - " + end;}
```

방어적 코드를 작성한 클래스에 Serializable을 추가하면 불변이 깨지게 된다.

왜냐하면, readObject 메서드가 실직적으로 다른 public 생성자이기 때문이다. readObject 메서드에서도 생성자 처럼 방어적 코드를 작성해야 한다.

직렬화 형태를 객체로 만들어 반환해야 하고, 공격자로부터 인스턴스를 생성하는 일을 막고, 인스턴스의 내부를 수정하는 행위를 막아야 한다. 또한 객체를 역직렬화 할 때 클라이언트가 소유하면 안되는 객체는 방어적 복사를 해야한다.

### 주의점

- 직렬화 형태를 객체로 만들어 반환해야 한다.
- 공격자로부터 인스턴스를 생성하는 일을 막고, 인스턴스의 내부를 수정하는 행위를 막아야 한다.
- 객체를 역직렬화 할 때 클라이언트가 소유하면 안되는 객체는 방어적 복사를 해야한다.
    - final 필드는 방어적 복사가 불가능 하다.
- 기본 readObject 메서드는 trasient 필드를 제외한 모든 필드 값을 매개변수로 받아 유효성 검사 없이 사용 가능한 경우이다.
- final이 아닌 직렬화 클래스는 readObject 메서드를 직접적이든 간접적이든 호출해서 안된다.
