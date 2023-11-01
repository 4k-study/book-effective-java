# Item 43. 람다보다는 메서드 참조를 사용하라

람다의 가장 큰 장점은 간결함이다. 메서드 참조를 사용하면 람다보다 더 간결하게 사용할 수 있다.

람다로 작성이 가능한건 모두 메서드 참조로 변환할 수 있다.

예를들어 다음 코드를 살펴보자

```sql
//람다
map.merge(key, 1, (count, incr) -> count + incr);

//메서드 참조
map.merge(key, 1, Integer::sum);
```

메서드 참조를 사용하면 매개변수를 적을 필요 없어서 더 간결하다.

코드가 간결해져 좋긴 하지만, 람다의 매개변수 이름을 통해 이해를 도와주는 코드도 있으니 적절히 사용해보자.

람다의 참조 유형은 다섯가지가 있다.

| 참조 유형 | 메서드 참조 | 람다 |
| --- | --- | --- |
| 정적 | Integer::parseInt | str → Integer.parseInt(str) |
| 한정적 인스턴스 | Instant.now()::isAfter | Instant then = Instant.now();
t → then.isAfter(t) |
| 비한정적 인스턴스 | String::toLowerCase | str → str.toLowerCase() |
| 클래스 생성자 | TreeMap<K, V>::new | () → new TreeMap<K, V>() |
| 배열 생성자 | int[]::new | len → new int[len] |

### 마무리

- 람다보다 메서드 참조가 대부분 간단 명료하니 사용하자.
- 메서드 참조가 항상 짧고, 직관적이진 않다. 람다가 더 짧거나, 가독성이 좋다면 람다를 사용하자.
