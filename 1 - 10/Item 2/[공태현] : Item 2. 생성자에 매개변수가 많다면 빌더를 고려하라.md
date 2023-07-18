## 문제

- 선택 매개변수가 많은 클래스의 경우 생성자, 정적 팩터리 메서드를 사용하는 경우 모두 많은 생성자나 정적 팩터리 메서드를 생성해줘야 하고(점증적 생성자 패턴) 따라서 사용자 입장에서도 실수할 가능성이 높아진다.
- 자바빈즈 패턴인 기본 생성자로 인스턴스를 만들고 setter로 주입하는 방법은 점층적 생성자 패턴 문제를 해결했지만 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

## 대안

```
💡 빌더 패턴을 사용하자
```

- 필요한 객체를 직접 만드는 대신 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻고 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정한다. 마지막으로 매개변수가 없는 `build` 메소드를 호출해 객체를 얻는다.

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
}
```

위 코드를 보면 빌더의 setter들은 모두 빌더를 반환하기 때문에 연쇄적으로 setter를 호출할 수 있다. (메서드 체이닝, 플루언트 API)

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240,8)
		.calories(10).sodium(35).build();
```

위와 같이 사용함으로써 가독성을 높이고 사용하기도 쉬워진다.

### 빌더 패턴의 단점

- 빌더 객체를 먼저 생성해야 하기 때문에 성능이 민감하다면 문제가 될 수 있다.
- 코드가 장황해서 매개변수가 적으면 유용하지 않다.
