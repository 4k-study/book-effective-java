# Item 37. ordinal 인덱싱 대신 EnumMap을 사용하라

배열이나 리스트에서 원소를 꺼낼 때 ordinal 메서드를 사용해 인덱스를 얻는 방법이 있다. 이는 몇가지 문제가 동반된다. 

- Set같이 제네릭을 사용하는 경우 배열고 호환되지 않는다. → 비검사 형변환을 해야한다.
- 출력 결과를 재가공 해야한다.
- 값에 대한 보증을 해야한다.

Map을 사용하면 이 단점들을 상쇄할 수 있다.

- “비검사” 형변환 필요 없음
- 출력 문자열 제공
- 값에 대한 보증
- 코드 간결화

### 마무리

Item 35, 36, 37에서 ordinal 메서드의 단점에 대해 언급되었다.

실제로 인덱스로 관리되는 경우 변경점에 불리하고, 다른사람의 코드를 볼때 오래걸리게 된다.

모두 공감하며 읽었으나, 과연 실무에서 적용할 기회가 있을지는 의문이다.
