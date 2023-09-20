## 제네릭이란?

> [!NOTE] 제네릭
>  컴파일 단계에서 메소드, 클래스에서 사용될 타입을 지정해줌으로써 불필요한 타입 검증 로직을 없애고, 런타임 단계에서 발생할 수 있는 타입에 의한 에러도 방지해주는 타입

Java에서 제공하는 `List` 클래스도 제네릭을 지원한다. 따라서 `List<String>`처럼 리스트에 넣을 타입을 제한하고 싶을 때 제네릭을 사용할 수 있다.

### 로 타입을 사용하지 말라
제네릭을 지원하는 클래스여도 사용하지 않을 수 있다.
```java
List list = new ArrayList();
```
하지만 위와 같이 사용하면 제네릭이 주는 안전성과 표현력을 모두 잃게 된다. 

### 매개변수화 타입
매개변수화 타입인 `List<Object>`는 사용해도 된다. `List<Object>`는 모든 타입을 허용하는 리스트라는 뜻이다. 

이 둘의 차이점은 무엇일까?

`List`는 제네릭 타입에서 완전히 발을 뺀 것이고, `List<Object>`는 컴파일러에게 모든 타입을 허용한다는 의미를 전달한 것이다.

### 제네릭과 불공변

제네릭은 불공변이라는 특징을 가지고 있다. 다음 코드를 보자
```java
public void methodA(List<Obejct> list) {}
public void methodB(List list) {}
```
위와 같이 `List<Obejct>`, `List`를 매개변수로 갖는 두 메소드가 있다.
```java
public static viod main(String[] args) {
	List<String> strings = List.of("a", "b", "c");
	
	methodA(strings); // 컴파일 에러
}
```
`methodA`를 호출하면서 매개변수로 `List<String>`을 사용한다면 컴파일 에러가 발생한다.
```java
public static viod main(String[] args) {
	List<String> strings = List.of("a", "b", "c");
	
	methodB(strings);
}
```
반면에 `methodB`를 호출하면서 매개변수로 `List<String>`을 사용한다면 컴파일 에러가 발생하지 않는다.

즉, `List<String>`은 `List`의 하위타입이지만 `List<Object>`의 하위타입은 아니다. 이처럼 A가 B의 하위 타입일 때, T\<A> 가 T\<B>의 하위 타입이 아니면 T는 불공변이라고 한다. (제네릭은 불공변이라는 특징을 갖는다.)

```java
public static void main(String[] args) {  
	List<String> strings = new ArrayList<>();  

	addToList(strings, Integer.valueOf(42));  
	String s = strings.get(0);  
}  

public static void addToList(List list, Object object) {  
	list.add(object);  
}
```
위 코드를 보면 `addToList()`는 `List` 타입을 매개변수로 갖는다. `addToList()`를 호출 할 때 `List`에는 `List<String>`을, `Object`에는 정수값을 넣으면 컴파일 단계에서는 통과하지만 `strings.get(0)`을 통해 값을 꺼낼때 자동형변환이 되면서 `ClassCastException`이 발생한다. (만약 제네릭이 공변이라면 같은 문제가 발생할 것이다.)
-> <mark style="background: #ADCCFFA6;">로우 타입의 `List`를 사용하지 말자.</mark>

### 와일드카드
만약 하위 타입인 제네릭도 매개변수에 포함하고 싶다면 와일드카드를 사용할 수 있다. 와일드카드는 `?`로 표기한다. 
```java
public int method(List<?> list) {...}
```
위 메소드는 모든 타입의 `List<>`를 매개변수로 받을 수 있다.

`Set`과 `Set<?>`의 차이는, `Set`에는 여러 타입의 원소를 넣을 수 있기 때문에 타입 불변식을 훼손하기 쉽다. 
```java
public static void main(String[] args) {  
    List<String> strings = new ArrayList<>();  
    strings.add("!");  
  
    List myList = new ArrayList();  
    myList.add(1);  
    myList.add("!");  
  
    for (Object o : myList) {  
        System.out.println(o);  
    }
}
```
위 코드에서 `myList`에는 `String`타입과 `Integer` 타입의 원소가 모두 들어갔지만 컴파일 에러가 발생하지 않는다.

반면, `Set<?>`는 `null`을 제외한 어떤 원소도 넣을 수 없다.
```java
List<?> myList = new ArrayList();  
myList.add(null);  
myList.add("!");  // 컴파일 에러
```
위 코드에서 `null`을 넣을 때는 문제 없지만 문자열 `!`를 넣을 때는 컴파일 에러가 발생한다.

### 클래스 리터럴에는 로 타입을 써라
```java
List.class // O
List<String>.class // X
```
자바에서는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했다. 

### 정리
- 로 타입을 사용하면 런타임 예외가 발생할 수 있으니 사용하면 안된다.
- 로 타입은 제네릭이 도입되기 전 코드와의 호환성을 위해 제공될 뿐이다.
- `Set<Object>`는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이다.
- `Set<?>`는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다.
- `Set<Object>`와 `Set<?>`는 안전하지만, `Set`은 안전하지 않다.


