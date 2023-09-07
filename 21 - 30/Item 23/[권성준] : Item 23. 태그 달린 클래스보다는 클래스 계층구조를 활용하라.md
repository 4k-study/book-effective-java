# Item 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라

## 태그 달린 클래스

```java
class Figure {
	enum Shape { RECTANGLE, CIRCLE };
	
	// 태그 필드 - 현재 모양을 나타낸다.
	final Shape shape;

	// 다음 필드들은 모양이 사각형일 때만 쓰인다.
	double length;
	double width;

	// 다음 필드는 모양이 원일 때만 쓰인다.
	double radius;

	// 원용 생성자
	public Figure(double radius) {
		shape = Shape.CIRCLE;
		this.radius = radius;
	}

	// 사각형용 생성자
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

- 열거 타입 선언, 태그 필드, switch 문과 같이 쓸데 없는 코드 많음
- 가독성 낮음
- 서로 다른 구현을 위한 코드가 함께 있어 메모리 낭비됨
- 필드를 final 로 선언하려면 생성자에서 필요 없는 필드도 초기화해야함
- 새로운 의미를 추가하려면 기존 코드를 수정해야함
- 인스턴스 타입만으로 구현체를 알 수 없음

### 계층 구조로 바꾸는 법

- 루트가 되는 추상 클래스 정의
- 태그에 따라 동작이 달라지는 메서드를 추상 메서드로 선언
- 동작이 일정한 메서드는 루트 클래스에 구현
- 공통 사용되는 필드들도 루트 클래스에 선언

```java
abstract class Figure {
	abstract double area();
}

class Circle extends Figure {
	final double radius;
	
	Circle(double radius) { this.radius = radius; }

	@Override
	double area() { return Math.PI * (radius * radius); }
}

class Rectangle extends Figure {
	final double length;
	final double width;

	Rectanble(double length, double width) {
		this.length = length;
		this.width = width;
	}
	
	@Override double area() { return length * width; }
}
```
