### 공변, 불공변
배열은 공변이라는 특징을 가진다. 공변이란 `Child`가 `Parent`의 하위타입이라면, `Child[]`는 `Parent[]`의 하위타입이 되는 성질을 의미한다. 

따라서 다음 코드는 컴파일 에러를 발생시키지 않는다.
```java
public static void main(String[] args) {  
    Object[] objects = new Long[10];  
    objects[0] = "str";  
}
```
`Obejct`는 `Long`의 부모타입이기 때문에 `Object[]` 타입에 `Long[]` 타입이 들어올 수 있다. 위 코드는 컴파일 단계가 아닌 런타임 단계에 에러가 발생한다.

제네릭은 불공변이라는 특징을 가진다. 공변이란 `Child`가 `Parent`의 하위타입일 때, `Child[]`는 `Parent[]`의 하위타입이 되지 않는 성질을 말한다.

따라서 다음 코드는 컴파일 에러를 발생시킨다.
```java
public static void main(String[] args) {  
    List<Object> list = new ArrayList<Long>();  
}
```

### 실체화
배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다. 따라서 런타임 단계에 타입 에러가 발생할 수 있다. -> 배열을 실체화된다.

반면에, 제네릭은 타입 정보가 런타임에는 소거되고 오로지 컴파일 단계에서만 타임을 검사한다. 

### 배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다.
- 제네릭 타입: `List<E>`
- 매개변수화 타입: `List<String>`
- 타입 매개변수: `E`
즉, 배열은 다음과 같이 사용될 수 없다. `new List<E>[]`, `new List<String>[]`, `new E[]`

다음 예제를 보자.
```java
List<String>[] stringLists = new List<String>[1];  // 1
List<Integer> intList = List.of(10);  // 2
Object[] objects = stringLists;  // 3
objects[0] = intList;  // 4
String s = stringLists[0].get(0);  // 5
```
1. `Object[]` 타입의 변수에 `List<String>[]` 타입의 `stringLists`를 할당한다. 배열은 공변이기 때문에 가능하다.
2. `objects[0]`에 `List<Integer>` 타입의 `intList`를 넣는다. 컴파일 단계에서는 `Object` 타입에 `List<Integer>`이 들어가는 것이기 때문에 문제가 발생하지 않는다. 
	1. `Object[] objects = new List<String>[]` 이더라도, 런타임 단계에서는 제네릭은 타입을 체크하지 않고 소거되기 때문에 런타임 단계에서도 문제가 발생하지 않는다.
3. 이때, `stringList` 배열에 첫 번째 값을 꺼내면 타입은 `new List<String>[]` 이기 때문에 `List<String>`이 나올 것이라 판단하고, 거기서 `get(0)`을 한다면 `String` 타입이 나올 것이라 생각하기 때문에 컴파일 단계에서 문제가 발생하지 않는다.
	1. 하지만 실제로는 `stringLists[0]`에는 `List<Integer>`이 들어있기 때문에 `String` 타입 변수에 들어갈 수 없다. 따라서 런타임 에러가 발생한다. (`ClassCastException`)

이런 문제를 방지하기 위해 1번 부분에서 에러를 발생시켜야 한다.

### 실체화 불가 타입
`E`, `List<E>`, `List<String>`은 실체화 불가 타입이라 한다. 즉, 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입이다.

###  제네릭과 가변인수 메서드
제네릭과 가변인수 메서드를 함께 사용하면 해석하기 어려운 경고 메시지를 받게 된다. 가변인수 메서드를 호출할 때마다 가변인수 매개변수를 담을 배열이 하나 만들어지는데, 이때 그 배열의 원소가 실체화 불가 타입이라면 경고가 발생하는 것이다.
![](https://i.imgur.com/Gug65s0.png)
`@SafeVarargs` 어노테이션으로 대처할 수 있다.

### 핵심 정리
- 배열은 공변, 제네릭은 불공변이라는 특징을 가진다.
- 제네릭은 런타임에서 타입 정보가 소거된다. 
- 배열은 런타임 단계에서는 타입 안전하지만 컴파일 단계에서는 타입이 안전하지 않다.
- 배열과 제네릭을 섞어 쓰다가 컴파일 오류나 경고를 만나면 배열을 리스트로 대체해보자
