# Item 7. 다 쓴 객체 참조를 해제하라

## 문제 예시

GC 가 있는 언어를 사용하여도 메모리 관리에 신경 써야한다. 예시를 보자.

1. 스택
    
    ```java
    public class Stack {
    	private Object[] elements;
    	private int size = 0;
    	private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    	public Stack() {
    		elements = new Object[DEFAULT_INITIAL_CAPACITY];
    	}
    
    	public void push(Object e) {
    		ensureCapacity();
    		elements[size++] = e;
    	}
    
    	public Object pop() {
    		if (size == 0) {
    			throw new EmptyStackException();
    		}
    
    		return elements[--size];
    	}
    
    	private void ensureCapacity() {
    		if (elements.length() == size) {
    			elements = Arrays.copyOf(elements, 2 * size + 1);
    		}
    	}
    }
    ```
    
    위 코드에서는 스택이 커졌다가 줄어들 때, 스택에서 꺼내진 객체들이 GC 로 회수되지 않는다. 스택이 그 객체들의 참조를 가지고 있기 때문이다. 그로 인해 심하면 OOM 이 일어날 수 있다.
    
    - 해법
        - 참조를 다 썼을 때 null 처리
            
            ```java
            public Object pop() {
            	if (size == 0) {
            		throw new EmptyStackException();
            	}
            
            	Object result = elements[--size];
            	elements[size] = null;
            	return result;
            }
            ```
            
            - 이점
                - null 처리한 객체를 사용할 때 NullPointerException 으로 오류를 빠르게 발견할 수 있다
            - 이상적인 방법은 아니다. 코드를 복잡하게 만든다. null 처리는 위 예시처럼 자기 메모리를 직접 관리하는 클래스인 경우에 사용한다.
        - 참조를 담은 변수를 유효 scope 밖으로 밀어내어 참조를 해제하는 것이 가장 좋다
            - ex) 메서드 안에 선언된 지역변수가 메서드 종료 시 해제되는 것
    

2. 캐시
    
    객체 참조를 캐시에 넣고 놓아두면 메모리 누수가 일어난다.
    
    - 해법
        - 외부에서 key 를 참조하는 동안만 살아있는 캐시가 필요하다면 WeakHashMap 을 사용하자
            - WeakHashMap 이란 WeakReference 유형의 키를 사용하여 사용되지 않으면 GC 에 의해 제거된다
        - 캐시 엔트리의 가치를 시간이 지날수록 떨궈 주기적으로 청소해준다


3. 리스너 혹은 콜백
    
    클라이언트가 등록만 하고 해지해주지 않으면 메모리에 쌓인다.
    
    - 해법
        - WeakHashMap 으로 저장하자

## 내 생각 정리

- WeakHashMap 와 Reference 종류를 새롭게 알게되었다.
    - Reference 종류
        - Strong
            - Java에서 가장 기본적인 참조 유형으로 new 연산을 통해 객체를 생성하는 방법
            - 스택 영역의 변수가 힙 영역 객체에 대한 참조를 가지고 있으면 절대 GC 의 대상이 되지 않는다
            - 해제하려면 null 처리
        - Soft
            - 대상 객체를 참조하는 변수가 SoftReference 만 있는 경우에 GC 의 대상이 될 수 있다
            - 다만, JVM 메모리가 부족한 경우에만 GC 에 의해 제거된다
        - Weak
            - 대상 객체를 참조하는 변수가 WeakReference 만 있으면 GC 의 대상이 된다
