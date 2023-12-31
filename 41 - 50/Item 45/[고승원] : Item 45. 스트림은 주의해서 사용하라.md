# Item 45. 스트림은 주의해서 사용하라

스트림은 다량의 데이터 처리 작업을 돕고자 자바 8에 추가되었다.

스트림의 핵심 개념은 두 가지다

- 스트림은 원소의 유한 혹은 무한 시퀀스를 뜻한다.
- 스트림 파이프 라인은 이 원소들을 수행하는 연산 단계이다.

스트림 규칙

- 스트림 원소는 어디서든 올 수 있다.
- 스트림 안의 데이터 원소는 객체 참조나 기본(int, long, double) 타입이다.
- 파이프 라인은 소스 스트림에서 종단 연산으로 끝나고, 그 사이에 0개 이상의 중간 연산이 있다.

계산 원리

- 스트림 파이프라인은 지연 평가 된다. 평가는 종단 연산 호출시 이뤄지며, 종단 연산에 쓰이지 않는 데이터 원소는 계산에 쓰이지 않는다. → 무한 스트림의 핵심 → 종단 연산은 필수이다.

사용처

- 코드가 간결해 지는 경우 / 가독성이 좋아 지는 경우
- 위의 조건을 지키면서 final인 변수만 다루고, return, break, continue를 사용하지 않는 경우
- 원소의 일관적인 변환, 필터링, 연산하여 결합, 컬렉션에 모으는 경우
- 스트림 평탄화 작업 (flatMap)

주의점

- 람다 매개변수명은 신중히 지을 것
- 스트림이 지원하는 기본 타입은 모두 숫자형이니 주의하자. (형변환할 땐 안쓰는게 나을수도)
- 람다는 final인 변수만 읽을 수 있고, return, break, continue를 사용할 수 없다.
- 파이프라인을 거치며 각 단계에서 값에 동시에 접근하기 어렵다. (매핑되면 기존의 값은 사라짐)
- 대부분은 기존코드만, 스트림만 고집하기 보단, 적절히 섞어 쓰는 것이 더 효율적이고 보기 좋다.
