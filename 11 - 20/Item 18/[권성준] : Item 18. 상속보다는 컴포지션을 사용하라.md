# Item 18. 상속보다는 컴포지션을 사용하라

## 상속의 문제점

- 캡슐화를 깨트림
    - 상위 클래스의 구현에 따라 하위 클래스의 동작 이상이 생길 수 있음
        
        ```java
        public class CustomSet<E> extends HashSet<E> {
        
            private int count = 0;
        
            @Override
            public boolean add(E e) {
                count++;
                return super.add(e);
            }
        
            @Override
            public boolean addAll(Collection<? extends E> c) {
                count += c.size();
                return super.addAll(c);
            }
        
            public int getCount() {
                return count;
            }
        }
        ```
        
        - addAll에 size 가 3인 List를 넣으며 호출 시 count 는 3을 기대하지만 6이 됨. HashSet의 addAll이 내부적으로 add를 이용하기 때문임.

## 대안 : 컴포지션

- 정의
    - 기존 클래스를 확장하는 대신 새로운 클래스에 private 필드로 참조하는 방식
- 장점
    - 기존 클래스의 구현에 영향 받지 않고, 기존 클래스에 메서드가 추가되어도 영향 받지 않음
- 예시
    
    ```java
    public class CustomSet<E> {
    
        private int count = 0;
        private HashSet<E> set;

        public CustomSet(HashSet<E> set) {
            this.set = set;
        }
    
        public boolean add(E e) {
            count++;
            return set.add(e);
        }
    
        public boolean addAll(Collection<? extends E> c) {
            count += c.size();
            return set.addAll(c);
        }
    
        public int getCount() {
            return count;
        }
    }
    ```
    

## 상속을 사용해도 되는 경우

- 하위 클래스가 상위 클래스의 ‘진짜’ 하위인 경우. 즉 is-a 관계인 경우
- 상위 클래스의 API에 결함이 없어야 함
