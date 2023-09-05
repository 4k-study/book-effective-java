## 태그 달린 클래스

- 하나의 클래스에 두 가지 이상의 의미를 표현하는 클래스
- 현재 표현하는 의미를 태그 값으로 알려주는 클래스

```java
class Figure {
	enum Shape { RECTANGLE, CIRCLE };
	
	// 태그 필드: 현재 모양
	final Shape shape;

	// 사각형 전용 필드
	double length;
	double width;

	// 원 전용 필드
	double radius

	// 원 전용 생성자
	public Figure(double radius) {
		shape = Shape.CIRCLE;
		this.radius = radius;
	}

	// 사각형 전용 생성자
	public Figure(double length, double width) {
		shape = Shape.RECTANGLE;
		this.length = length;
		this.width = width;
	}
	
	double area() {
		switch(shape) {
			case RECTANGLE:
				return length * length;
			case CIRCLE: 
				return Math.PI * (radius * radius);
			default: 
				throw new AssertionError(shape);
		}
	}
}
```

### 단점

- 여러 구현이 한 클래스에 혼합되어 있어서 가독성이 나쁨
    - SRP 위반
- 다른 의미를 위한 코드가 함께 존재하기 때문에 메모리를 많이 차지
- 필드를 `final`로 선언하면, 사용하지 않는 필드까지 한 번에 초기화 해줘야 함
- 엉뚱한 필드를 초기화 했을 경우 런타임 단계에서 확인 가능
- 새로운 의미를 가진 타입을 추가해야 한다면 기존 클래스를 수정해야 함
    - OCP 위반

### 대안

- 상속을 통한 다형성을 이용할 것
- 루트가 될 추상 클래스를 정의하고, 태그 값에 따라 동작이 달라지는 메소드를 루트 클래스의 추상 메소드로 선언한다.
- 태그 값에 상관없이 동작이 일정한 메소드를 루트 클래스에 일반 메소드로 추가한다.
- 공통으로 사용하는 필드도 루트 클래스에 추가한다.
- 태그들은 루트 클래스를 확장한 구체 클래스로 정의한다.

```java
abstract class Figure {
	abstract double area();
}

class Circle extends Figure {
	final double radius;
	
	Circle(double radius) { this.radius = radius; }

	@Override
	double area() { return Math.PI * (radius * radius); }
```

- 각 클래스는 하나의 의미, 책임을 가진다. → SRP 준수
- 새로운 타입이 추가하면 `Figure`를 확장한 새로운 클래스를 만들면 된다. → OCP 준수
