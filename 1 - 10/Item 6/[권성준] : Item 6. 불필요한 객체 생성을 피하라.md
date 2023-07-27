# Item 6. 불필요한 객체 생성을 피하라

## 객체를 재사용하면 좋은 경우

- 불변 객체, 사용 중에 변경되지 않을 가변 객체는 매번 생성하기보다는 재사용하는 것이 좋다.
    
    ```java
    // 안좋은 예
    // 매번 String 인스턴스를 새로 만든다
    String s = new String("bikini");
    
    // 좋은 예
    // 하나의 String 인스턴스를 사용한다
    String s = "bikini";
    ```
    
- 생성 비용이 아주 큰 객체가 반복해서 필요한 경우
    
    ```java
    // 변경 전
    // matches 안에서 매번 Pattern 객체를 만든다. Pattern 객체는 생성 비용이 높다.
    static boolean isQuestionMark(String s) {
    	return s.matches("?");
    }
    
    // 변경 후
    public class QuestionMark {
    	private static final Pattern QUESTION_MARK=Pattern.compile("?");
    
    	static boolean isQuestionMark(String s) {
    		return QUESTION_MARK.matcher(s).matches();
    	}
    }
    ```
    

## 불필요한 객체를 만들어내는 경우

- 오토박싱
    
    ```java
    private static long sum() {
    	Long sum = 0L;
    	for (long i = 0; i <= Integer.MAX_VALUE; i++) {
    		sum += i;
    	}
    
    	return sum;
    }
    ```
    
    - sum 을 Long 으로 선언해주어 i 가 더해질 때마다 i 가 오토박싱된다
    - 박싱된 기본 타입보다는 기본 타입을 사용하자

## 정리

다만 조심해야할 것은 방어적 복사가 필요할 경우인데 객체를 재사용한다면, 불필요하게 객체를 생성할 때보다 피해가 크다.
