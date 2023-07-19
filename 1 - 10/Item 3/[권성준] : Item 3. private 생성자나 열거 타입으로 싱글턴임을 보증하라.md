# Item 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 싱글턴
- 싱글턴은 인스턴스를 오직 하나만 만들 수 있는 클래스
- 싱글턴으로 만들면 테스트하기 까다로움
    - 인터페이스로 추상화하고 구현하여 만든 경우가 아니라면 모킹으로 대체할 수 없기 때문

## 싱글턴 만드는 방식
1. 생성자는 private, 인스턴스는 public static final
    ```java
    public class Elvis {
    	public static final Elvis INSTANCE = new Elvis();
    	private Elvis() {...}
    
    	public void leaveTheBuilding() {...}
    }
    ```
    
    - 특징
        - 리플렉션을 이용한 경우가 아니라면 클라이언트에서 새롭게 인스턴스를 만들 수 없음
            - 리플렉션을 방지하고 싶다면 생성자를 통해 새로운 인스턴스가 생성될 때 예외를 던지게 하면 됨
    - 장점
        - 해당 클래스가 싱글턴임이 잘 드러남
        - 간결함
2. 인스턴스는 private static final, 정적 팩터리 메서드를 public static
    
    ```java
    public class Elvis {
    	private static final Elvis INSTANCE = new Elvis();
    	private Elvis() {...}
    	public static Elvis getInstance() {return INSTANCE;}
    
    	public void leaveTheBuilding() {...}
    }
    ```
    
    - 특징
        - 항상 같은 인스턴스를 참조하므로 새로운 인스턴스 생성되지 않음
        - 리플렉션을 통한 생성은 방식 1과 똑같이 적용됨
    - 장점
        - 싱글턴이 아닌 경우로 변경하고자할 때, 클라이언트 코드 수정할 필요 없음
        - 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음
            - 제네릭 싱글턴 팩터리 : 제네릭으로 타입 설정 가능한 인스턴스를 만들어두고, 반환 시에 제네릭으로 받은 타입을 이용해 타입을 결정하는 방식
        - 정적 팩터리의 메서드 참조를 Supplier 로 사용 가능함
            - ex) Elvis::getInstance 를 Supplier<Elvis> 로 사용
3. Enum 사용
    - 배경
        - 방식 1, 방식 2는 직렬화하려면 모든 인스턴스 필드를 transient 선언하고 readResolve 메서드가 싱글턴 인스턴스를 반환하도록 구현해야함. 그러지 않으면 직렬화 시마다 새로운 인스턴스 생성됨.
    
    ```java
    public enum Elvis {
    	INSTANCE;
    
    	public void leaveTheBuilding() {...}
    }
    ```
    
    - 장점
        - 가장 간결함
        - 직렬화 시 별도의 노력 필요 없음
        - 복잡한 직렬화 상황, 리플렉션 공격을 방지함
    - 단점
        - 싱글턴이 다른 클래스를 상속해야한다면 이 방식은 사용 불가능
            - enum 은 이미 java.lang.enum 을 상속받기에 다른 클래스를 상속 받을 수 없기 때문
                - https://www.baeldung.com/java-extending-enums
            - enum 이 다른 인터페이스를 구현하는 방식을 사용할 수는 있음

## 내 생각 정리

- enum 은 어떻게 복잡한 직렬화와 리플렉션 공격을 방지할까?
    - clone() 메서드 미지원
    - serializable 이 기본적으로 구현되어 있어 따로 구현할 필요 없음.
        - 근데 역직렬화 시에 객체 생성은 어떻게 방지할까?
    - enum 은 외부에서 인스턴스화할 수 있는 생성자 자체가 없음. 내부 상수를 생성하는 생성자만 존재함.
    - 리플렉션은 생성자를 얻어와 newInstance() 메서드를 호출하여 객체를 생성하는데, newInstance() 시점에 enum 이라면 IllegalArgumentException 가 발생함
