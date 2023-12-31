# Item 25. 톱레벨 클래스는 한 파일에 하나만 담아라

한 파일에 톱레벨 클래스를 여러개 선언하는 행위는 아무런 문제가 없지만, 심각한 위험을 감수해야 한다. 한 클래스를 여러 가지로 정의할 수 있으며, 어느 소스를 먼저 컴파일 하는지에 따라 달라지기 때문이다.

즉 컴파일이 먼저 되는 클래스에 따라 값이 다르게 처리된다는 뜻이다.

![image](https://github.com/4k-study/book-effective-java/assets/85796588/1d418fc5-6976-410c-9ac4-50de6f580605)

다행히 똑똑한 인텔리제이는 이를 막아준다.

### 마무리

한 파일에 톱레벨 클래스를 여러개 정의하지 말자. 컴파일 순서에 따라 값이 달라지기 때문이다.

정 그렇게 사용하려면 정적 멤버 클래스를 고려해보자.

책의 1/3 가량 읽어가는데, 지금까지 느낀점은 [객체지향 생활체조](https://jamie95.tistory.com/99)만 잘 지켜도 책의 절반은 지킨다는 생각이 든다.
