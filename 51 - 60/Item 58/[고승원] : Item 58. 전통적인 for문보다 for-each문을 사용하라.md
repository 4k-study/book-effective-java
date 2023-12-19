# Item 58. 전통적인 for문보다 for-each문을 사용하라

for-each문은 Iterable 인터페이스를 구현하면 순회가 가능하다.

|  | for | for-each |
| --- | --- | --- |
| 코드 | 지저분함 | 깔끔함 |
| 변수 컴파일 | 오타 위험 있음 | 컴파일러가 잡아준다 |
| 컬렉션 | 컬렉션마다 다르게 사용 해야함 | 컬렉션이 달라도 동일한 코드 사용 가능 |

for-each문을 중첩할 때 코드가 깔끔해짐

```sql
for (Iterator<Suit> i = suits.iterator(); i.hasNext(); ) {
	Suit suit = i.next();
	for (Iterator<Rank> j = ranks.iterator(); j.hasNext(); ) {
		deck.add(new Card(suit, j.next()));
	}

for (Suit suit : suits)
	for (Rank rank : ranks)
		deck.add(new Card(suit, rank));
```

### for-each문을 사용하지 못할 때

- 파괴적인 필터링 → 스트림의 removeIf를 사용하자.
- 변형 → 리스트나 배열의 일부 혹은 전체를 교체해야 한다면 인덱스가 필요하다.
- 병렬 반복 → 병렬 제어를 하려면 각각의 인덱스 변수를 엄격하고 명시적으로 관리해야함
