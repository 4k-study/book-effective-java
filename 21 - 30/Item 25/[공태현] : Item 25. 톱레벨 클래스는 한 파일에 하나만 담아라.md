다음과 같이 한 파일에 여러 클래스를 선언할 수 있다.

```java
class Utesil {
	static final String NAME = "pan";
}

class Dessert {
	static final String NAME = "cake";
}
```

→ 어느 소스 파일을 먼저 컴파일하냐에 따라 한 클래스를 여러 가지로 정의할 수 있기 때문에 사용하지 말자

해결책은 톱레벨 클래스들을 서로 다른 소스 파일로 분리한다. 굳이 여러 톱레벨 클래스를 한 파일에 담고 싶다면 정적 멤버 클래스를 사용하는 방법을 고민해보자.
