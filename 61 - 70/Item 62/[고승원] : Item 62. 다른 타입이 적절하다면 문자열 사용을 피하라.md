# Item 62. 다른 타입이 적절하다면 문자열 사용을 피하라

문자열 즉 String은 텍스트를 표현하도록 설계되었다.

true/false, 숫자 같은 텍스트는 String으로 사용하는게 좋을까 각자 핏한 자료형을 사용하는게 좋을까?

당연하게도 핏한 자료형을 사용하는게 좋다. **기본 자료형이 없다면 타입을 작성하는 것도 방법이다.**

문자열을 피해야 하는 이유

- 상수를 열거할 땐 이넘 타입이 월등히 좋다.
- 문자열은 혼합 타입을 대신할 수 없다.
    - 파싱하는데 리소스가 많이 생기고 오류 가능성도 있다.
    - 적절한 편의 메서드도 없다. (equals, toString, compareTo 등)
    - 권한을 표현하기에 적절하지 않다.
