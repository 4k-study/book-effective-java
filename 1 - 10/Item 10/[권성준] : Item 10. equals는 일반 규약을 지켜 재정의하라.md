# Item 10. equals는 일반 규약을 지켜 재정의하라

## equals 를 재정의하지 않는 것이 좋은 상황

- 각 인스턴스가 본질적으로 고유한 경우
    - ex) Thread
- 인스턴스의 동등성(논리적 동치)을 검사할 일이 없는 경우
- 상위 클래스에서 정의한 equals 가 하위 클래스에도 들어맞는 경우
- 클래스가 private, package-private 여서 equals 호출할 일이 없는 경우

즉, 객체의 동등성(논리적 동치) 를 비교해야하는데, 상위 클래스의 equals 가 들어맞지 않는 경우 equals 를 재정의해야한다.

## equals 일반 규약

- 반사성
    - null 아닌 모든 참조 값 x에 대해, x.equals(x) 는 true 다
- 대칭성
    - null 아닌 모든 참조 값 x, y 에 대해 x.equals(y) 가 true 면, y.equals(x) 도 true 다
- 추이성
    - null 아닌 모든 참조 값 x, y, z 에 대해 x.equals(y) 가 true 고, y.equals(z) 도 true 면, x.equals(z) 도 true 다
- 일관성
    - null 아닌 모든 참조 값 x, y 에 대해 x.equals(y) 를 반복 호출하면 항상 같은 값을 반환한다
- null 아님
    - null 아닌 모든 참조 값 x에 대해, x.equals(null) 은 false 다
    

## equals 메서드 구현 방법

1. == 연산자 사용하여 입력이 자기 자신인지 확인
    1. 성능 최적화용
2. instanceof 연산자로 입력이 올바른 타입인지 확인
3. 입력을 올바른 타입으로 형변환
    1. 앞서 instanceof 를 해주는 이유임
4. 입력과 자기 자신의 필드가 모두 일치하는지 확인

## 내 생각 정리

- 최근에는 스스로 만드는 것보다 ide 를 사용하거나, 롬복의 @EqualsAndHashCode 등을 사용하는 것이 편리하고 안전하다고 생각한다. 다만, equals 규약에 대해 알고 쓰는 것과 아닌 것은 차이가 있다. 규약에 대해 알게 되어서 좋았다.
