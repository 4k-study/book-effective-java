# Item 82. 스레드 안전성 수준을 문서화하라.

주석을 사용하던지, 어노테이션을 사용하던지, 스레드 안정성은 꼭 문서화 하자.

### 스레드 안정성 레벨

스레드 안정성은 중요하다. 어떤 상황에서 어떤 레벨을 갖는지 알아보자.

- immutable : 상수와 같아서 외부 동기화가 필요 없다. (String, Long 등)
- unconditionally thread-safe : 인스턴스는 수정 가능, 내부적으로 충분한 동기화 (Atomic Long, ConcurrentHashMap 등)
- conditionally thread-safe : 무조건적 스레드 안전과 같지만, 외부 동기화가 필요하다. (Collections.synchronized 반환 컬렉션)
- not thread-safe : 인스턴스 수정 가능, 동기화 사용하려면 외부 동기화 필요. (ArrayList, HashMap 등)
- thread-hostile : 외부 동기화를 해도 thread-safe하지 않다. 이런 경우는 deprecated 하는게 옳다.
