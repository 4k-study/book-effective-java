# Item 77. 예외를 무시하지 말라

**예외가 발생하는 이유는, 예외를 적절히 처리하라는 뜻이다.** 하지만, 잘 지켜지지 않고 있다.

이는 catch 블록에서 아무 일도 하지 않는 경우를 뜻한다.

catch 블록에서 아무런 조치를 하지 않으면, 예외는 없는 것과 다름 없다.

만약, 예외를 무시하기로 했다면, 그에 맞는 적절한 로그를 남기고, 예외 이름도 ignored 표시를 해두는게 좋다.

예외를 무시하면, 예측하지 못한 상황에서 발생한 위험을 지나치기 쉽상이다. 

catch 블록을 비워두는 것 보다 차라리 상위 API로 전파하라.

### 예외를 무시할 때

- FileInputStream을 닫을 때
