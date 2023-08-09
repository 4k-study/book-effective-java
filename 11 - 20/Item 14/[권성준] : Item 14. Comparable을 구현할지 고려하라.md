# Item 14. Comparable을 구현할지 고려하라

## 개요

Comparable의 compareTo는 동치성 비교에 더해 순서까지 비교할 수 있고, 제네릭하다. Comparable을 구현했다는 것은 그 클래스 인스턴스에 순서가 있다는 것이다. Comparable을 구현하는 것만으로 정렬, 검색, 극단값 계산 등의 많은 이득을 누릴 수 있다.

## compareTo 규약

equals 규약과 마찬가지로 반사성, 대칭성, 추이성을 충족해야한다.

1. 두 객체 잠초의 순서를 바꿔 비교해도 예상한 결과가 나와야 한다
2. 첫 번째가 두 번째보다 크고 두 번째가 세 번째보다 크면, 첫 번째는 세 번째보다 커야 한다
3. 크기가 같은 객체들끼리는 어떤 객체와 비교해도 항상 같다

## compareTo 비교 방법

1. 객체 참조 필드 비교 시 compareTo 재귀 호출
2. 비교자(Comparator) 사용
3. 박싱된 기본 타입 비교 시 compare 사용

## Comparator

- 일련의 비교자 생성 메서드와 팀을 꾸려 메서드 연쇄 방식으로 비교자를 생성할 수 있게 되었다
    
    ```java
    public class PhoneNumber implements Comparable<PhoneNumber> {
        private static final Comparator<PhoneNumber> COMPARATOR =
                Comparator.comparingInt((PhoneNumber pn) -> pn.areaCode)
                        .thenComparing((PhoneNumber pn) -> pn.prefix)
                        .thenComparingInt((PhoneNumber pn) -> pn.lineNum);
    
        private String areaCode;
        private String prefix;
        private String lineNum;
    
        @Override
        public int compareTo(PhoneNumber o) {
            return COMPARATOR.compare(this, o);
        }
    }
    ```
    
    - 간결하지만, 약간의 성능 저하가 있다
    - 정적 임포트 기능을 사용하면 코드가 훨씬 깔끔해진다
- 수많은 보조 생성 메서드들로 중무장하고 있다
- 객체 참조용 비교자 생성 메서드도 준비하고 있다
- ex) comparing, thenComparing

**주의점**

- <, > 연산자를 사용하여 비교하지 말아야 한다
    - 정수 오버플로를 일으키거나 부동소수점 계산 방식에 따라 오류를 낼 수 있다
    - 대신 정적 compare 메서드나, Comparator 가 제공하는 비교자 생성 메서드를 사용하자

## 내 생각 정리

- 매번 <, > 연산자를 이용하여 비교하였는데 문제를 알게 되었다
- Comparator 의 다양한 메서드들을 알게 되었다
