
### `var` keyword for local variable type inference

Java 10에서는 로컬 변수의 타입을 명시적으로 지정하지 않고 `var` 키워드를 사용하여 컴파일러가 할당된 값에 따라 타입을 추론할 수 있음.

예시)

Good case

```java
// var 를 간단하게 지역 변수로 활용
public class VarExample {     
	public static void main(String[] args) {         
		var list = List.of("Alice", "Bob", "Charlie", "David");          
		for (var item : list) {             
			System.out.println(item);         
		}     
	} 
}
```

이 예시에서 `var`를 사용하면 코드가 더욱 간결해짐. \
 `list`가 `List<String>`이며 `item`이 `String`임이 명확해짐.

Bad case

```java
// var 타입을 unclear 하게 사용하면 코드 가독성을 해침
	public class UnclearVarExample {     
		public static void main(String[] args) {         
			var result = calculate(); // Unclear what the type of 'result' is          
			System.out.println(result);     
		}     
	 public static Object calculate() {         
		 // Perform some calculation and return an Object     
	 } 
 }
```

이 예제에서는 `var`를 사용함으로써 `result`의 타입이 불분명해져 코드의 가독성이 감소함. \
이러한 경우에는 변수의 타입을 명시하는 것이 좋음.


[Local Variable Type Inference: Style Guidelines (openjdk.org)](https://openjdk.org/projects/amber/guides/lvti-style-guide)

[JEP 286: Local-Variable Type Inference (openjdk.org)](https://openjdk.org/jeps/286)

