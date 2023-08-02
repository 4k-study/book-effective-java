# Item 11. equals를 재정의하려거든 hashCode도 재정의하라

## 개요

equals 를 재정의한 클래스에서 hashCode 를 재정의하지 않으면 Hash 를 사용하는 자료구조에서 문제를 일으킬 수 있다.

## Object 명세

- equals 비교에 필요한 정보가 변경되지 않았다면, hashCode 메서드는 애플리케이션이 실행되는 동안 일관된 값을 반환해야 한다
- equals 가 true 면 두 객체의 hashCode 도 같아야 한다
- equals 가 다르다고 hashCode 가 다를 필요는 없다. 단, 다른 객체는 다른 hashCode 를 가져야 성능이 좋다.

위 2번째 명세를 지켜야한다.

## 내 생각 정리

- hashCode 를 재정의하지 않으면 Hash 사용 자료구조에서 왜 문제가 되는지 근본적인 이유가 궁금해서 찾아보니 아래와 같은 순서로 동작한다
    
    ![image](https://github.com/4k-study/book-effective-java/assets/82152173/2017aa0a-610e-4a18-8996-b015660d5f1f)
    
    - 출처: https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/
