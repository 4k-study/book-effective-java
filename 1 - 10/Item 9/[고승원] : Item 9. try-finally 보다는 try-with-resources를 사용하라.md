# Item 9. try-finally 보다는 try-with-resources를 사용하라

자바에서 close 메서드를 호출해 직접 닫아줘야 하는 자원이 있다. 이를 닫지 않는다면 예측할 수 없는 성능 문제가 일어나기도 한다.

try-finally의 단점

- 자원이 여러개인경우 중첩해 사용해야 한다.
- 코드가 많아져 실수할 여지가 많아진다.
- 예외 정보가 남지 않아 디버깅이 어렵다.
    
    ```java
    pustatic String firstLineOfFile(String path) throws IOException {
            BufferedReader br = new BufferedReader(new FileReader(path));
            try {
                return br.readLine();
            } finally {
                br.close();
            }
        }
    ```
    
    다음과 같은 상황에서 readLine() 메서드나 close() 메서드 모두 문제가 생기는 경우에는 두 번째 예외만 반환된다.
    

이런 단점들을 상쇄하고자 try-resource-finally를 사용하자. try-resource-finally를 사용하려면 `AutoCloseable` 인터페이스를 구현해야 한다.

try-resource-finally의 장점

- 자원이 많아져도 간결하다.
- 예외 정보가 stackTrace에 담겨 출력되어 디버깅하기 보다 수월하다.

### 마무리

try를 사용했는데 catch를 안쓰는 경우가 있을까 생각하며, try-finally의 단점을 부각하려는 의도라고 생각했다.

자원에 대해 try문을 반복사용하지 않아도 되는점과 에러 트레이스가 편해진다는 것은 장점이라 생각한다.
