# Item 28. 배열보다는 리스트를 사용하라

### 배열과 제네릭 타입의 차이

- 배열은 공변이고, 제네릭은 불공변임
    - 공변이라함은 함께 변한다는 의미로 Sub 가 Super 의 하위 타입이면, Sub[] 가 Super[] 의 하위 타입이 된다는 의미
    - `List<Sub>` 는 `List<Super>` 의 하위도 상위도 아님
    
      ```java
      // 배열 예시
      Object[] objectArray = new Long[1];
      objectArray[0] = "Test"; // ArrayStoreException 을 던짐
      
      // 제네릭 타입 예시 - 컴파일 되지 않음
      List<Object> ol = new ArrayList<Long>(); // 호환되지 않음
      ol.add("Test");
      ```
    
    - 제네릭 타입을 사용하면 컴파일 시점에 오류를 할 수 있지만, 배열은 런타임 시점에서야 오류를 알 수 있음
- 배열은 런타임에도 자신의 원소 타입을 인지하고 확인하고, 제네릭은 런타임에 타입 정보가 소거됨.
    - 제네릭은 컴파일 타임에만 타입을 검사하고 런타임에는 알 수 없다는 의미
    - 그렇기 때문에 제네릭 배열은 사용할 수 없음
        - ex) new List<E>[], new List<String>[], new E[]
