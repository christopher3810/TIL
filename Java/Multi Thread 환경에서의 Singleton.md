***

### Singleton 패턴

---

> 인스턴스를 오직 한개만 제공하는 클래스

-   커넥션 풀, 스레드 풀, 디바이스 설정 객체 등과 같은 경우 인스턴스를 여러 개 만들게 되면 불필요한 자원을 사용하게 되고 예상치 못한 결과를 낳을 수 있다.
-   인스턴스를 오직 한 개만 만들어 제공하는 클래스가 필요

-   해당하는 클래스에 인스턴스가 하나만 만들 수 있어야 하고 그 만들어진 하나에 글로벌하게 접근할 수 있게 해야 함
    
    -   오로지 하나의 인스턴스만 존재해야 하는 경우.(ex : 설정)
    
-   필요한 요소
    
    1.  new를 실행할 수 없도록 생성자에 private 접근 제어자를 지정한다.
    2.  유일한 단일 객체를 반환할 수 있는 정적 메서드가 필요하다.
    3.  유일한 단일 객체를 참조할 정적 참조 변수가 필요하다.


	```java
public class App {

	public static void main(String[] args) {
		Settings settings = new Settings(); // 외부에서 접근 불가 Setting 클래스 내부에서 접근 할 수 있도록 해줘야 함
		Settings s1 = Settings.getInstance();
		Settings s2 = Settings.getInstance();
		Settings s3 = Settings.getInstance();
		
		System.out.println(s1);
		System.out.println(s2);
		System.out.println(s3);
		// 스택 프레임에 생성된 s1, s2, s3 참조 변수가 단일 객체를 참조하게 된다
		// 객체 참조 변수 자체를 출력시 객체의 toString() 메서드가 호출되고 별도로 오버라이딩 하지 않았다면 객체 고유 값인 hashcode를 반환하게 되는데 호출할 때마다 같은 값을 출력한다.
	}

}

public class Settings {

	private static Settings instance; // 정적 객체 참조 변수

	private Settings() {}; // 해당 클래스 내부에서만 접근할 수 있도록 접근 제어자 private 사용

	public static Settings getInstance() { // 객체 반환 정적 메서드

		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}
	
}
	```

## 멀티 쓰레드 환경에서 쓰레드 언세이프?

---

> 싱글턴 패턴은 멀티 쓰레드 환경에서 잠재적 문제를 갖고 있다. 여러 개의 쓰레드가 동시에 `getInstance()` 메서드에 접근한다고 할 때 여러 개의 인스턴스가 만들어질 수도 있는 상황이 발생이 있다. 
> 이를 해결하기 위한 방법으로는 몇 가지가 있다

### 동기화(Synchronization) 사용

`getInstance()` 메서드에 `synchronized` 키워드를 사용하여 동시에 여러 개의 쓰레드가 접근하는 것을 막을 수 있다.

```java
public class Settings {

	private static Settings instance;

	private Settings() {};

	public static synchronized Settings getInstance() {
		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}
}

```

이 방법은 간단하지만, 동기화로 인한 성능 저하가 발생할 수 있다.

### 초기화 시 인스턴스 생성(Eager Initialization)

클래스 로딩 시점에 인스턴스를 생성하여 멀티 쓰레드 문제를 해결할 수 있다.  \
이 방법은 클래스 로딩 시점에 인스턴스가 생성되기 때문에 사용하지 않을 경우에도 인스턴스가 생성되는 단점이 있다.

```java
public class Settings {

	private static Settings instance = new Settings();

	private Settings() {};

	public static Settings getInstance() {
		return instance;
	}
}

```

### Double-checked Locking 사용

`getInstance()` 메서드 내부에 synchronized 블록을 사용하여 인스턴스가 null인 경우에만 동기화를 수행하는 방법이다.  \
이 방법은 성능 저하를 최소화하면서 멀티 쓰레드 문제를 해결할 수 있다.

```java
public class Settings {

	private static volatile Settings instance;

	private Settings() {};

	public static Settings getInstance() {
		if (instance == null) {
			synchronized (Settings.class) {
				if (instance == null) {
					instance = new Settings();
				}
			}
		}
		return instance;
	}
}

```

이러한 방법들을 통해 싱글턴 패턴의 멀티 쓰레드 문제를 해결할 수 있다. 프로젝트의 요구 사항에 따라 적절한 방법을 선택하여 사용하면 된다.