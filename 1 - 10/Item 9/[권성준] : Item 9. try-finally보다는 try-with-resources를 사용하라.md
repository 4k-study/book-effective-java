# Item 9. try-finally보다는 try-with-resources를 사용하라

## 개요

자바에는 InputStrea, OutputStrea, Connection 등 직접 닫아줘야 하는 자원들이 많다. finalizer 가 안전망으로 사용되고 있지만 finalizer 는 안전하지 못하다. 이를 어떻게 해결하는지 살펴보자.

## 전통적인 방법

전통적으로 자원을 닫을 때, try-finally 방식을 이용하였다. 

```java
static String firstLineOfFile(String path) throws IOException {
	BufferedReader br = new BufferedReader(new FileReader(path));	

	try {
		return br.readLine();
	} finally {
		br.close();
	}
}
```

이 방식에는 몇 가지 문제가 있다.

- 자원이 추가될 수록 코드가 복잡해진다
- 예외 정보가 사라져 디버깅이 어려워지는 경우가 발생한다
    - 위의 firstLineOfFile 메소드를 실행 시, 기기의 문제로 br.readLine(), 와 br.close() 에서 예외가 발생했다고 해보자. 이런 상황이라면 br.close() 에서 발생한 예외가 br.readLine() 의 예외를 삼켜버린다. 실제로 필요한 예외는 처음 발생한 br.readLine() 의 예외이므로 유용한 로그를 제공하지 못한다.

## try-with-resources

AutoCloseable을 구현한 객체를 자동으로 닫아준다

```java
static void copy (String src, String dst) throws IOException {
	try (InputStream in = new FileInputStream(src);
			OutputStream out = new FileOutputStream(dst)) {
		byte[] buf = new byte[BUFFER_SIZE];
		int n;

		while ((n = in.read(buf)) >= 0 ) {
			out.write(buf, 0, n);
		}	
	}
}
```

- 장점
    - 닫아야하는 자원이 많아져도 코드가 간결하다
    - 예외 정보가 삼켜지지 않고 스택 트레이스에 담긴다
