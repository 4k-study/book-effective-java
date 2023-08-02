- 자바 라이브러리에는 `close()` 메소드를 호출하여 직접 닫아줘야 하는 자원이 많음
    - `InputStream`, `OutputStream`, `java.sql.Connection`
- 하지만 실수로 개발자가 `close()` 메소드 호출을 해주지 않는다면 성능 문제가 발생할 수 있음

## 전통적인 방법: try-finally

```java
BufferedReader br = new BufferedReader(new FileReader(path));
try {
	return br.readLine();
} finally {
	br.close();
}
```

괜찮은 방법이지만 사용하는 자원이 많을수록 호출해야할 `close()` 메소드 수가 많아지기 때문에 코드가 지저분해질 수 있다.

### 문제점

위 코드에서 `br.readLine()` 부분과 `br.close()` 부분 모두에서 예외가 발생한다면 `br.close()`에서 발생한 예외가 던져지고 처음 예외가 발생했던 `br.readLine()` 부분은 생략된다. 이렇게 되면 첫 번째 예외가 발생한 부분을 찾지 못하여 디버깅이 어려워진다. 

## 해결법: try-with-resources

- `AutoCloseable` 인터페이스를 구현한 자원인 경우 자동으로 `close()` 메소드를 호출해준다.

```java
try (BufferedReader br = new BufferedReader(new FileReader(path))) {
	return br.readLine();
}
```

- 코드가 짧아져 가독성이 좋아진다.
- 위 코드를 보면 `br.readLine()`에서 예외가 발생했을 경우 추적이 가능해진다. (앞선 문제 해결)
    - 만약 `close()` 메소드 호출 시 예외가 발생해도 숨겨졌다는 의미인 suppressed라는 키워드를 달고 예외가 출력된다.

```java
public class ResourceTest {
    public static void main(String[] args) throws Exception {

        MyResource resource = new MyResource();

        try {
            resource.use();
        } finally {
            resource.close();
        }
        

    }

    static class MyResource implements AutoCloseable {

        public void use() {
            throw new RuntimeException("use exception");
        }

        @Override
        public void close() throws Exception {
            throw new RuntimeException("close exception");
        }
    }
}
```

위 코드에서 `try-finally`를 사용해서 자원을 사용하고, `close()`하였다. 그리고 각 메소드 호출 시 예외가 발생하도록 하였다.

![image](https://github.com/4k-study/book-effective-java/assets/68289543/48bd04bb-3189-4571-bd8e-7c9ca931fe87)

결과는 `use()` 메소드 호출 시 발생한 예외는 무시되고 `close()` 메소드 호출 시 발생했던 예외만 출력되었다.

```java
public class ResourceTest {
    public static void main(String[] args) throws Exception {

        try (MyResource resource = new MyResource()) {
            resource.use();
        }

    }

    static class MyResource implements AutoCloseable {

        public void use() {
            throw new RuntimeException("use exception");
        }

        @Override
        public void close() throws Exception {
            throw new RuntimeException("close exception");
        }
    }
}
```

이번엔 `try-with-resources`를 사용하였다.

![image](https://github.com/4k-study/book-effective-java/assets/68289543/41c40abd-bb18-4d0a-9965-ec74555c98b0)

결과는 먼저 발생한 `use()` 메소드의 예외가 추적되었고, `close()` 메소드 호출 시 발생한 예외도 Suppressed로 출력되었다.

```java
try (MyResource resource = new MyResource()) {
    resource.use();
} catch (RuntimeException e) {
    // ...
}
```

`try-with-resources`도 catch문을 사용할 수 있다.

### 정리

회수해야 하는 자원을 사용할 때 `try-with-resources`문을 사용하자. 다음과 같은 장점이 있다.

- 코드가 간결하여 가독성이 좋아진다.
- 만들어지는 예외 정보가 더 유용하다.
