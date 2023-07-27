# Item 7.  다 쓴 객체 참조를 해제하라

이런 말을 들어본 적이 있을 것이다. “Stack 말고 Deque을 사용하라.” 왜인지 알아보자

```java
public class Stack {
    int size;
    int [] stack;

    public Stack(int size) {
        this.size = size;
        stack = new int[size];
    }

    public void push(int item) {
        stack[++size] = item;
    }

    public void pop() {
        if(size=0) {
          throw new EmptyStackException();
        }
        return stack[--size];
    }
}
```

참조되지 않는 객체는 GC의 대상이 되어 사라지는데, 스택을 구현하는 코드에서는 자원을 해제하지 않고, size만 줄이기 때문에 pop된 객체들은 GC의 대상이 되지 않는다.

이렇게 사용하지 않지만, 남아있는 객체들로 인해 메모리 누수가 발생해 OutOfMemory가 생길 수 있다.

pop메서드를 다음과 같이 바꿔주자.

```java
public void pop() {
      if(size=0) {
          throw new EmptyStackException();
      }
      Object result = stack[--size];
      stack[size] = null;
      return result;
}
```

그렇다고 모든 객체를 null 처리할 순 없으니, 해당 변수를 scope 밖으로 밀어내는 것이다.

캐시 역시 메모리 누수가 생기기 쉬운데, 이런 경우 캐시의 life time을 설정하여 소멸되게 하면 해결된다.

마지막으로 Listener, Callback 함수다. 위 함수를 등록하고 해제하지 않으면, 계속 쌓이게 된다. 이때 콜백을 약한 참조로 저장하면 GC가 바로 수거해간다.

### 마무리

Deque의 pop과 같은 코드를 보면 메모리를 해제해주고 있다.

```java
@Override
public E pollFirst() {
    Node<E> node = last;
    return node ==null ?null : unlinkFirst();
}
```

메모리 누수는 찾지못해 몇년간 잠복하는 사례도 있어 찾기 어려운 문제이다. 따라서 최고의 해결법은 예방하는 것이니 코드를 더욱 신경써서 만들어보자.
