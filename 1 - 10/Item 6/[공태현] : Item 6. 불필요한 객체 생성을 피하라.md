### 똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하자

```java
String s = new String("Kong");
```

위 코드는 호출될 때마다 매 번 새로운 String 객체를 생성한다.

```java
String s = "Kong";
```

위와 같이 사용하여 하나의 인스턴스만 생성하도록 하자

생성자 대신 정적 팩터리 메서드를 사용하면 불필요한 객체 생성을 피할 수 있다. 생성자는 호출할 때마다 매 번 새로운 인스턴스를 만들지만 정적 팩터리 메서드를 사용함으로써 의도적으로 이를 막을 수 있다.

```java
Boolean b = new Boolean(true);
```

위 코드는 호출할 때마다 매 번 새로운 Boolean 인스턴스를 만든다.

```java
Boolean b = Boolean.valueOf(true);
```

하지만 위 코드는 호출할 때마다 이미 생성된 Boolean 인스턴스를 반환한다.

```java
public final class Boolean implements java.io.Serializable,
                                      Comparable<Boolean>, Constable
{
    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);

		// ...
		@IntrinsicCandidate
		public static Boolean valueOf(boolean b) {
		    return (b ? TRUE : FALSE);
		}
}
```

### 생성 비용이 비싼 객체는 캐싱하여 재사용하자

주어진 정규표현식에 포함이 되는 문자열인지 확인하는 메소드는 다음과 같이 작성할 수 있다.

```java
boolean isCorrect(String s) {
	return s.matches("정규표현식");
}
```

하지만 `String.matches()` 메소드가 내부에서 만드는 정규표현식용 `Pattern` 인스턴스는 한 번 쓰고 버려저서 가비지 컬렉션의 대상이 된다. 

```java
public boolean matches(String regex) {
    return Pattern.matches(regex, this);
}

public static boolean matches(String regex, CharSequence input) {
    Pattern p = Pattern.compile(regex);
    Matcher m = p.matcher(input);
    return m.matches();
}
```

코드를 확인해보면 `String.matches()` 메소드는 `Pattern.compile()` 메소드를 호출하여 해당되는 정규식에 대한 `Pattern` 인스턴스를 생성하고 해당 인스턴스와 문자열을 비교한다. 

```java
public class XXX {
	private static final Pattern PATTERN = Pattern.compile("정규표현식");

	static boolean isCorrect(String s) {
		return PATTERN.matcher(s).matches();
	}
}
```

따라서 위와 같이 코드를 수정하면 미리 만들어놓은 `Pattern` 인스턴스를 재사용함으로써 불필요한 객체 생성 비용을 줄일 수 있다.

### 오토박싱, 오토언박싱

오토박싱이란 원시 타입을 참조 타입으로 자동으로 변환해주는 것을 말한다.

```java
Integer i = 1;  // 오토 박싱
```

위 코드는 1이라는 정수형을 Integer라는 래퍼 클래스로 자동으로 변환해준다.

오토언박싱이란 반대로 래퍼 클래스를 원시 타입으로 자동으로 변환해주는 것을 말한다.

```java
Integer i = 1;

int a = i; // 오토 언박싱
```

다음 메서드를 보자

```java
Long sum = 0L;
for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i;
}
```

sum은 `Long` 타입이기 때문에 for문에서 i와 연산을 할 때마다 `long` 타입으로 언박싱되고, 더해진 결과를 다시 `Long` 타입으로 박싱한다. 따라서 성능적으로 좋지 않다. 또한 `Long`의 값은 불변이기 때문에 위 코드에서 `for`문을 다 돌면 `Integer.MAX_VALUE` 수 만큼 `Long` 인스턴스가 생성된다. 

→ 래퍼 클래스보다는 원시 타입을 사용하자 (의도치 않은 오토박싱에 주의하자)
