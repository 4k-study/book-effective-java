# Item 71. 필요 없는 검사 예외 사용은 피하라

CheckedException를 잘 사용하면 프로그램 퀄리티를 높일 수 있다.

### 사용할 때

- 의미있는 조치를 할 수 있는 경우
- API를 제대로 사용해도 발생할 수 있는 경우

### 단점

- CheckedException은 예외 처리를 하거나 상위 메서드로 전파해야 하기 때문에 사용자에게 부담을 준다.
- 스트림에서 사용할 수 없다.

### 대안

- 옵셔널 반환
- UnCheckedException 예외로 변경

### 마무리

- 예전에 UnCheckedException만 실무에서 사용한다 들었고, 실제로도 거의 그렇다. CheckedException을 언제 사용할까 생각했었는데, 알게 되었다. (잔액이 부족한 경우)
- 그래도 UnCheckedException을 사용해서 얼리리턴 하는게 좋아보인다.
