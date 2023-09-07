# Item 25. 톱레벨 클래스는 한 파일에 하나만 담으라

파일 하나에 톱레벨 클래스를 여러 개 선언하지 말아야한다. 컴파일 순서에 따라 클래스를 여러 가지로 정의할 수 있기 때문이다.

```java
// Main.java
public class Main {
	public static void main(String[] args) {
		System.out.prinln(Utensil.NAME + Dessert.NAME);
	}
}

// Utensil.java
class Utensil {
	static final String NAME = "pan";
}

class Dessert {
	static final String NAME = "cake";
}

// Dessert.java
class Utensil {
	static final String NAME = "pot";
}

class Dessert {
	static final String NAME = "pie";
}
```

- javac Main.java / javac Main.java Utensil.java → `pancake`
- javac Dessert.java Main.java → `potpie`
- javac Main.java Dessert.java → `컴파일 오류`

이처럼 실행 결과가 컴파일 순서에 따라 달라질 수 있으니 톱레벨 클래스들은 서로 다른 파일로 분리하자.
이처럼 실행 결과가 컴파일 순서에 따라 달라질 수 있으니 톱레벨 클래스들은 서로 다른 파일로 분리하자.
