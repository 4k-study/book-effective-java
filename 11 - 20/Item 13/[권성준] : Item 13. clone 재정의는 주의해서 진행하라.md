# Item 13. clone 재정의는 주의해서 진행하라

## 개요

Cloneable 인터페이스와 clone 메서드는 아래와 같은 문제가 있다.

- Cloneable 인터페이스는 빈 껍데기이고, clone 메서드가 선언된 곳은 Cloneable 이 아닌 Object 이다
- clone 메서드의 접근 제한자가 protected 다
- 즉, Cloneable 을 구현하는 것만으로는 외부 객체에서 clone 메서드를 호출할 수 없고, Object 의 clone 을 오버라이딩 해주어야 한다

그렇다면, Cloneable 인터페이스의 역할은 뭘까? 

Cloneable 인터페이스를 구현한 객체에서 clone을 호출하면 복사한 객체를 반환하고, 그렇지 않은 클래스는 CloneNotSupportedException을 던진다.

## 문제점 - clone 메서드의 허술한 일반 규약

```
1. x.clone() != x // 복사한 객체는 원본 객체와 다른 인스턴스이다
2. x.clone().getClass() == x.getClass(); // 복사한 객체와 원본 객체는 같은 클래스이다
3. x.clone().equals(x) // 복사한 객체와 원본 객체은 동치다. 관례상, clone() 은 super.clone() 을 통해 객체를 얻어서 반환한다.

// 다만 위 조건들은 필수가 아닌 선택이다.
```

- 생성자 연쇄와 비슷한 방식
- clone 메서드를 super.clone 이 아닌 new 방식으로 복사했다고 해보자. (컴파일 시점에 문제 없음)
- 이 클래스를 상속한 하위 클래스에서 super.clone 을 호출하면 상위 클래스의 타입을 반환하여 제대로 동작하지 않는다
- final 로 상위 클래스를 선언하면 하위 클래스가 없으므로 new 방식으로 해도 된다. 다만 그러면 Cloneable 을 구현하는 이유가 없다.

## 해결 방법

### 불변 객체

- super.clone 으로 객체 복사
- 공변 반환 타이핑을 적용하여 형변환
- 반환

### 가변 객체

super.clone 시 주소를 복사하여 원본 객체에 문제가 생길 수 있다.

clone 은 원본 객체에 영향을 주지 않으면서 복제된 객체의 불변을 보장해야한다.

→ clone 에서 재귀적으로 가변 객체 필드를 복사한다.

```java
@Override
public Stack clone() {
	 try {
			 Stack result = (Stack) super.clone();
       result.elements = elements.clone();
       // 배열의 clone은 런타임 타입과 컴파일 타입 모두가 원본 배열과 같은 배열을 반환
			 return result;
		} catch(CloneNotSupportedException e) {
		    throw new AssertionError();
		}
}
```

**clone() 재귀 호출이 부족하다면?**

배열 안에 가변 참조 객체가 있는 경우를 생각해보자. clone 의 재귀적 호출로는 부족하다. 이럴 때는 deep copy 를 하자.

**복잡한 가변 객체를 복제하는 법**

- super.clone 호출하여 얻은 객체의 모든 필드를 초기 상태로 설정
- 원본 객체의 상태를 다시 생성하는 고수준 메서드들을 호출
- 저수준에서 바로 처리할 때보다는 느리다
- Cloneable 아키텍처의 기초가 되는 필드 단위 객체 복사를 우회하기 때문에 전체 Cloneable 아키텍처와는 어울리지 않는 방식

## clone 메서드 잘 구현하는 법

- 하위 클래스에서 재정의될 수 있는 메서드를 호출하지 않는다
- 접근 제한자는 public 으로, CloneNotSupportedException을 던지지 않는다
- clone 메서드를 적절히 동기화해야한다

## 대안

- 복사 생성자와 복사 팩터리
- 엉성한 규약에 기대지 않고, final 필드 용법과도 충돌하지 않으며, 불필요한 checked 예외를 던지지도 않고, 형변환도 필요하지 않다

## 내 생각 정리

- 배열을 제외하곤 복사 생성자, 복사 팩터리를 사용하자
- getter 에서 원본 객체의 가변 객체를 그대로 넘겨주는 경우가 있었는데, 방어적으로 복사하도록 하자
