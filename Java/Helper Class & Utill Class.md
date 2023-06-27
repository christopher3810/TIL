Java 프로그래밍에서는 종종 Helper Class와 Utility Class라는 용어를 듣게 됨 

둘 사이에는 어떤 차이가 존재 할까?

### `Helper Class` 

Helper Class는 주로 어플리케이션에서 공통으로 사용되는 작업을 처리하는 데 사용되는 메소드를 포함하는 클래스임. 

클래스는 `인스턴스화` 될 수 있으며, `인스턴스 변수`, `인스턴스 메소드` 그리고 `static 메소드`를 **포함할 수 있음**.

`DateHelper` 라는 이름의 Helper Class를 만들고 이 클래스에 `날짜 관련 연산을 수행`하는 메소드를 추가한다면.

```java
class DateHelper { 
	public Date currentDate; 
	public DateHelper() { 
		this.currentDate = new Date(); 
	} 
	public Date getCurrentDate() { 
		return currentDate; 
	} 
	public static String getFormattedDate(Date date) { 
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd"); 
		return sdf.format(date); 
	} 
	public static Date addDays(Date date, int days) { 
		Calendar cal = Calendar.getInstance(); 
		cal.setTime(date); 
		cal.add(Calendar.DATE, days); 
		return cal.getTime(); 
	} 
}
```

현재 날짜를 반환하는 `인스턴스 메소드`와 날짜를 문자열 형태로 반환하거나 특정 일수를 더하는 `static 메소드를 포함`하고 있습니다.

### `Utility Class`

`특정 작업을 수행하는 데 필요한 static 메소드`를 제공하는 클래스입니다. 

이 클래스는 `인스턴스화할 수 없으며`, `모든 메소드는 static`입니다.

MathUtility 라는 이름의 Utility Class를 만들고 수학 계산 관련 메소드를 추가 한다면.

```java
public final class MathUtility { 

	private MathUtility(){} 
	
	public static double calculateSquare(double number) { 
		return number * number; 
	} 
	public static double calculateCube(double number) { 
		return number * number * number; 
	} 
	public static double calculateAverage(List<Double> numbers) { 
		double sum = 0; 
		for(Double number : numbers){ 
			sum += number; 
		} 
		return sum / numbers.size(); 
	} 
}
```

## Helper Class와 Utility Class 비교

둘 다 공통 작업을 처리하는 메소드를 제공하여 코드의 재사용성을 높이는 데 사용되지만 차이점이 있습니다.

1. **Helper Class**는 `인스턴스화할 수 있지만`, **Utility Class**는 `인스턴스화할 수 없음`.

2. **Helper Class**는 `인스턴스 변수`와 `메소드`를 포함할 수 있지만, **Utility Class**는 `모든 메소드가 static` 임.

3. **Utility Class**는 `어플리케이션 전역에서 접근 가능`하지만, **Helper Class**는 `같은 패키지 내의 클래스에서만 접근 가능`함.

