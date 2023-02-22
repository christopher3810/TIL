***
코드 예시
```java
public class Student { 
	private String name; 
	private int year; 
	private List<Integer> marks; 
	private Profile profile; 
// constructor getters and setters 
}
```


다중 Filter
```java
@Test public void whenUsingMultipleFilters_dataShouldBeFiltered() { 
	List<Student> filteredStream = students.stream() 
		.filter(s -> s.getMarksAverage() > 50) 
		.filter(s -> s.getMarks().size() > 3) 
		.filter(not(s -> s.getProfile() == Student.Profile.PHYSICS)) 
		.collect(Collectors.toList()); 
	assertThat(filteredStream).containsExactly(mathStudent); 
	}
```

복잡한 단일 FIlter
```java
@Test public void whenUsingSingleComplexFilter_dataShouldBeFiltered() { 
	List<Student> filteredStream = students.stream() 
		.filter(s -> s.getMarksAverage() > 50 && s.getMarks().size() > 3 
		&& s.getProfile() != Student.Profile.PHYSICS) .collect(Collectors.toList()); 
	assertThat(filteredStream).containsExactly(mathStudent); }
```

가독성이 떨어지면 별도의 메서드를 활용
```java
public boolean isEligibleForScholarship() { 
	return getMarksAverage() > 50 && marks.size() > 3 
			&& profile != Profile.PHYSICS; }
```

```Java
@Test public void whenUsingSingleComplexFilterExtracted_dataShouldBeFiltered() {
	List<Student> filteredStream = students.stream() 
		.filter(Student::isEligibleForScholarship) 
		.collect(Collectors.toList()); 
	assertThat(filteredStream).containsExactly(mathStudent); }
```


### 복잡한 단일FIlter  vs 다중 Filter

![MultiFilter](https://user-images.githubusercontent.com/61622657/220779557-23ab848c-a9ea-476d-9568-9fe4faf02797.jpg)
![MultiFilter](https://user-images.githubusercontent.com/61622657/220779557-23ab848c-a9ea-476d-9568-9fe4faf02797.jpg)

여러개의 Filter를 사용하면 코드 가독성이 향상될 수 있으나 그 반면에 여러개의 인스턴스 생성을 의미하며 성능 손실로 이어질 수 있습니다.

<br>

### 최적화 되지 않은 Filter로 인한 성능저하
***

Filter도 결국 Boolean을 반환하는 Predicate 이기 때문에 다중 필터를 사용하는지 여부와 관계 없이 검사 자체가 최적의 순서로 실행되지 않으면 Filtering으로 성능이 저하될 수 있습니다.

100개의 정수 Stream이 있고 20보다 작은 짝수를 찾는다고 가정한다면
```java
@Test public void givenWrongFilterOrder_whenUsingMultipleFilters_shouldEvaluateManyConditions() { 
	long filteredStreamSize = IntStream.range(0, 100).boxed() 
		.filter(this::isEvenNumber) 
		.filter(this::isSmallerThanTwenty) 
		.count(); 
	assertThat(filteredStreamSize).isEqualTo(10);
	assertThat(numberOfOperations).hasValue(150); 
	}
```

-   첫번째 filter에서 100개를 확인하고 이후 filter에서 50번을 확인합니다 - 150번
-   하지만 filter 순서를 바꾼다면 120개만 검사하면 됩니다.


### 참조
***
https://www.baeldung.com/java-streams-multiple-filters-vs-condition