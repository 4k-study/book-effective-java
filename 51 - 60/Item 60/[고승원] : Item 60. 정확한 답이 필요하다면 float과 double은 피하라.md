# Item 60. 정확한 답이 필요하다면 float과 double은 피하라

float과 double은 **근사치** 값이다.

근사치 값으로 계산하여 오차가 생기게 되면 치명적인 시스템 결함이 된다.

그럼 어떤걸 사용해야 하는가? 값이 정확한 BigDecimal, int, long을 사용하라.

BigDecimal은 느리고, 불편하다는 단점이 있지만 정확한 값을 다룰 수 있다.
