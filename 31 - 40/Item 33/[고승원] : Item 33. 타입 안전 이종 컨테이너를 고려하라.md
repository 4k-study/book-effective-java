# Item 33. 타입 안전 이종 컨테이너를 고려하라

한 컨테이너에서 여러 타입을 다루려면 어떻게 해야할까? 예를들어 Map을 살펴보자

Map을 사용할 때 제네릭 타입으로 Key, Value 두가지를 사용한다.

만약 Key의 타입이 매번 다르다면 어떡해야 할까? Map<Object, V> 로 사용하면 된다.

다만 이 방법은 치명적이 단점이 있는데, 타입 안정성이 현저히 떨어져 런타임 시점에 캐스팅 오류가 많이 일어난다.

이때 와일드 카드를 중첩하여 타입이 안전한 이종 컨테이너를 만들어 사용하면 되는데 자바에선 제네릭을 활용하여 만들 수 있다.

```java
public class TypeSafeContainer {
    private Map<T, Object> items = new HashMap<>();

    public void putItem(Class<T> type, Object value) {
				items.put(Objects.requireNonNull(type), value);
		}

    public <T> T getItem(Class<T> type) {
				return type.cast(items.get(type));
		}
```

1. T가 아닌 Class<T>로 만든 이유
    - 키의 타입을 더 정확하게 추적 / 유지 가능
    - 클래스 타입 관련 작업이 간편해진다. (타입 검사)
2. 리터럴 타입 토큰
    - 컴파일 타입 정보와 런타임 타입 정보를 알아내기 위해 메서드가 주고받는 class 리터럴을 뜻한다.
    - 수퍼 타입 토큰도 있다.
        - 실체 불가 타입(ex. List<>) 같은 경우는 타입 매개변수가 어떻든 List.class가 되기 때문에 수퍼 타입 토큰을 사용하기도 한다.
3. getItem 메서드에서 cast를 해서 반환하는 이유는 타입을 안전하게 만들기 위함이다.
    - 타입을 검사해서 변환하는 방법도 있지만, 이 방법은 타입 검사가 필요 없다.
    - Class 클래스에서 제공하는 메서드이므로 구현할 필요가 없다. (1번의 2번째 이유가 여기 해당한다.)
    - 컴파일만 잘 된다면 여기서 캐스팅 오류가 일어날 일은 없다.

### 주의점

1. 제네릭이 아닌 로타입의 Class 객체를 넣으면 타입 안정성이 깨진다. (`(Class)Integer.class`)
    
    ```java
    public void putItem(Class<T> type, Object value) {
    				items.put(Objects.requireNonNull(type), type.cast(value));
    		}
    ```
    
    다음과 같이 value와 type이 같은지 확인하면 방어할 수 있다.
    
2. 실체화 불가 타입에 사용할 수 없다.
    - 2번의 2번째 불릿에 적은 내용과 같다. 실체 불가 타입(ex. List<>) 같은 경우는 타입 매개변수가 어떻든 List.class가 되기 때문에 수퍼 타입 토큰을 사용하기도 한다.
    - 완벽한 방어법은 없으며, 수퍼 타입 토큰의 단점도 존재한다.
    

* 수퍼 타입 토큰

제네릭은 런타임에 타입이 소거되어 List<String>과 List<Integer>는 같게 된다.

따라서 런타임에 제네릭 타입 정보를 유지하기 위해 수퍼 타입 토큰을 사용한다.

```java
public class TypeLiteral<T> {
    private Class<T> type;
    
    public TypeLiteral(Class<T> type) {
        this.type = type;
    }
    
    public Class<T> getType() {
        return type;
    }
}
```

핸드오프가 당연히 있다.

- 복잡성 증가
- 리플렉션 문제 (오버헤드, 보안)
- 타입 안정성 부족

이 책에선 대안으로 한정적 타입 토큰을 제시한다.

예를들어 어노테이션이 있다.

```java
public Annotation getAnnotation(AnnotationElement element, String annotationType) {
		Class<?> annotationType = null;
		try {
				annotationType = Class.forName(annotationType);
		} catch (Exception e) {
				throw new IllegalArgumentException(e);
		}
		return element.getAnnotation(annotationType.asSubclass(Annotation.class);
```

핵심은 마지막 줄의 asSubclass이다.

asSubclass는 Class가 제공하는 메서드인데, 동적으로 안전하게 형변환을 해준다.
