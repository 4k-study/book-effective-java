# Item 84. 프로그램의 동작을 스레드 스케줄러에 기대지 말라.

스케줄링은 OS가 알아서 판단하여 작업을 할당하지만, OS에 따라 정책이 변경되어 플랫폼 자유도가 떨어질 수 있다.

## 견고하고 플랫폼 자유도 있는 프로그램을 작성하는 방법

- **무엇보다 스레드 개수가 프로세서 개수보다 너무 많으면 안된다.**
- 당장 처리할 작업이 없을 때 스레드를 실행하지 않는다.
- 적절한 스레드 개수와 작업 시간을 갖도록 설계한다.
- Thread.yield는 속도, 테스트면에서 이점이 없다.
- 스레드 우선순위는 정말 타당한 상황이 아니면 하지 않는게 좋다.
