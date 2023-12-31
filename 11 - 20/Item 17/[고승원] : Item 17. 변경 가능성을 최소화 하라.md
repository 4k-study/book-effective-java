# Item 17. 변경 가능성을 최소화 하라

불변 클래스란 인스턴스 내부 값을 수정할 수 없는 클래스다.

불변 클래스는 설계하고 구현하고 사용하기 용이하며, 안전하다.

Complex 같은 클래스도 불변 클래스로 설계되어 계산을 하고도 새로운 인스턴스를 만들어 반환한다.

### 불변 객체 만드는 법

아래 조건을 모두 만족하는건 정말 어렵다. 실제로는 일부 완화하여 사용하기도 한다.

- 객체의 상테를 변경하는 메서드를 만들지 않는다.
- 클래스를 확장할 수 없도록 한다. (상속을 막는다.)
- 모든 필드를 final로 선언한다.
- 모든 필드를 private으로 선언한다.
- 자신 외에 내분의 가변 컴포넌트에 접근할 수 없도록 한다. (가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야한다.)

### 장점

- 불변 객체는 근본적으로 thread-safe하며 동기화 할 필요가 없다. 따라서 안심하고 공유할 수 있다. (객체에 대해서 신경쓰지 않아도 된다.)
- 불변 객체는 재활용하여 중복을 제거 할 수 있다. (ex BigInteger) → 방어적 복사가 필요 없음
- 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 있다. (불변 객체를 Map이나 Set의 키값으로 사용)
- 실패 원자성을 갖는다. (실패해도 달라지는게 없음)

### 단점

- 값이 다르면 다시 객체를 생성해야 한다. → 성능 문제로 연결될 수 있음.
    - 성능을 위해 가변 동반 클래스를 사용하기도 한다. (ex StringBuilder)

* 가변 동반 클래스 : package-priavte 한 메서드를 만들어 계산할 때마다 매번 객체를 생성하지 않도록 한다.

```java
BigInteger multiply(long v) {
        if (v == 0 || signum == 0)
          return ZERO;
        if (v == BigDecimal.INFLATED)
            return multiply(BigInteger.valueOf(v));
        int rsign = (v > 0 ? signum : -signum);
        if (v < 0)
            v = -v;
        long dh = v >>> 32;      // higher order bits
        long dl = v & LONG_MASK; // lower order bits

        int xlen = mag.length;
        int[] value = mag;
        int[] rmag = (dh == 0L) ? (new int[xlen + 1]) : (new int[xlen + 2]);
        long carry = 0;
        int rstart = rmag.length - 1;
        for (int i = xlen - 1; i >= 0; i--) {
            long product = (value[i] & LONG_MASK) * dl + carry;
            rmag[rstart--] = (int)product;
            carry = product >>> 32;
        }
        rmag[rstart] = (int)carry;
        if (dh != 0L) {
            carry = 0;
            rstart = rmag.length - 2;
            for (int i = xlen - 1; i >= 0; i--) {
                long product = (value[i] & LONG_MASK) * dh +
                    (rmag[rstart] & LONG_MASK) + carry;
                rmag[rstart--] = (int)product;
                carry = product >>> 32;
            }
            rmag[0] = (int)carry;
        }
        if (carry == 0L)
            rmag = java.util.Arrays.copyOfRange(rmag, 1, rmag.length);
        return new BigInteger(rmag, rsign);
    }
```

### 해결법

자신을 상속하지 못하게 하는법

- final 클래스로 선언한다.
- 생성자를 package-private or priavte으로 만들고 정적 팩터리 제공 (사실상 final 클래스)

### 주의할 점

- BigInteger나 BigDecimal을 설계할 땐 불변 객체에 대한 생각 적립이 없던 시절이라 모두 재정의 할 수 있다. 이 클래스의 인스턴스를 인수로 받는 경우에는 주의하자.
- Setter 메서드 생성과 사용을 주의하자.
- 단순한 객체 뿐만 아니라 무거운 객체도 불변으로 만들 수 있는지 고심하자.
- 생성자는 불변식 설정이 모두 완료된 상태의 객체를 생성해야 한다. (재사용을 위한 상태 초기화는 안된다.)

### 마무리

현대에는 하드웨어의 성능과 GC의 성능도 정말 좋아져서, 객체를 설계하고 생성할 때 리소스 비용에 대해 굉장히 자유롭다고 생각한다.

하드웨어보다 관리 포인트를 줄여 어플리케이션의 안정성을 높이는게 더 좋은 방향성이지 않을까 생각이 든다.

그럼에도 “완벽한” 불변 객체의 조건은 굉장히 까다롭다 특히 final 클래스를 만드는 부분이라 생각이 드는데, 클래스에 final 키워드를 붙여본 적이 없을 뿐 더러, 모든 객체에 정적 팩토리를 제공하는 것 또한 불가능하다고 생각한다.

지금까지 잘 완화하여 사용하고 있지 않았을까? 객체 설계할 때 잘 생각해서 final 키워드를 클래스에 붙여보자.
