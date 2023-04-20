***

## Introduction to Lombok @Delegate Annotation

lombok 어노테이션중 알려지지 않은 기능 중 하나는 클래스에서 위임할 메서드를 자동으로 생성하는 `@Delegate` 어노테이션임 
이러한 메서드는 호출을 기본 객체로 전달하여 코드를 단순화하고 유지 관리하기 쉽게 만들어줌.

## Usage Examples

예제를 확인 해보자.

```java
public interface Calculator {    
	int add(int a, int b); 
    int subtract(int a, int b);
}  

public interface PowerCalculator {
    int power(int a, int b);
}

public class BasicCalculator implements Calculator {     
	public int add(int a, int b) {         
		return a + b;     
	}

    public int subtract(int a, int b) {
        return a - b;
    } 
}

public class AdvancedCalculator implements PowerCalculator {
    public int power(int a, int b) {
        return (int) Math.pow(a, b);
    }
}

```


이제 `CombinedCalculator` 클래스를 만들어 `BasicCalculator`와 `AdvancedCalculator`의 모든 기능을 결합하려고 한다면 

`@Delegate`를 사용하여 이 작업을 수행할 수 있음.

```java
import lombok.experimental.Delegate;  

public class CombinedCalculator {     
	@Delegate     
	private final BasicCalculator basicCalculator = new BasicCalculator(); 
	
	@Delegate
    private final AdvancedCalculator advancedCalculator = new AdvancedCalculator();
}

```

이렇게 하면 `CombinedCalculator`는 `add()`, `subtract()`, 그리고 `power()` 메서드를 모두 제공하며 내부적으로 `basicCalculator`와 `advancedCalculator` 인스턴스로 호출을 위임.

### type and exclude

```java
import lombok.experimental.Delegate;

public interface BasicCalculator {
    int add(int a, int b);
}

public interface Excluded {
    int divide(int a, int b);
}

public interface AdvancedCalculator {
    int multiply(int a, int b);
}

public class CalculatorDelegate {
    @Delegate(types = {BasicCalculator.class, AdvancedCalculator.class}, excludes = Excluded.class)
    private final CalculatorImpl calculatorImpl = new CalculatorImpl();
}

class CalculatorImpl implements BasicCalculator, AdvancedCalculator, Excluded {
    @Override
    public int add(int a, int b) {
        return a + b;
    }

    @Override
    public int multiply(int a, int b) {
        return a * b;
    }

    @Override
    public int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Division by zero is not allowed.");
        }
        return a / b;
    }
}

```


`BasicCalculator` 및 `AdvancedCalculator` 인터페이스의 메서드를 위임하고, `Excluded` 인터페이스의 메서드를 제외하고 있음. 
이렇게 하면 `CalculatorDelegate` 클래스는 `add`와 `multiply` 메서드를 제공하되, `divide` 메서드는 제공하지 않게 됨.

### Collection add와 addall에 대한 custom

```java
import lombok.experimental.Delegate;

import java.util.ArrayList;
import java.util.Collection;

public class DelegatingCollection {

    private interface Add {
        boolean add(String x);
        boolean addAll(Collection<? extends String> x);
    }

    @Delegate(excludes = Add.class)
    private final Collection<String> collection = new ArrayList<>();

    public boolean customAdd(String x) {
        System.out.println("Custom add method");
        return collection.add(x);
    }

    public boolean customAddAll(Collection<? extends String> x) {
        System.out.println("Custom addAll method");
        return collection.addAll(x);
    }
}

```

`DelegatingCollection` 클래스가 `Collection` 객체를 내부적으로 가지고 있으며, `@Delegate(excludes = Add.class)`를 사용하여 `add`와 `addAll` 메서드를 제외한 나머지 메서드들을 위임하고 있음. 

`customAdd`와 `customAddAll` 메서드를 사용하여 기존 `add`와 `addAll` 메서드에 대한 사용자 정의 구현을 제공할 수 있음.
