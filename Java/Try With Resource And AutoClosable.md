
자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. \
`InputStream`, `OutputStream`, `java.sql.Connection` 등이 좋은 예시이다. \
Java 7에서는 파일 스트림, 소켓 및 데이터베이스 연결과 같은 리소스의 자동 관리를 위해 `try-with-resources` 문을 도입했다. \
예외가 발생한 경우에도 리소스가 올바르게 닫히도록 보장하는 데 도움이 된다.

### 전통적인 자원을 닫는 수단 `try-finally`

```java
static String firstLineOfFile(String path) throws IOException { 
	BufferedReader br = new BufferedReader(new FileReader(path)); 
	try { 
		return br.readLine(); 
	} finally {
		 br.close(); 
    } 
 }
```

<br>

그다지 나쁘지 않다 하지만 자원을 하나 더 사용하게 되는 경우에는?

<br>

```java
static void copy(String src, String dst) throws IOException {
	InputStream in = new FileInputStream(src); 
	try { 
		OutputStream out = new FileOutputStream(dst); 
		try { 
			byte[] buf = new byte[BUFFER_SIZE]; 
			int n; 
			while ((n = in.read(buf)) >= 0) 
				out.write(buf, 0, n); 
		} finally { 
				out.close(); 
		} 
	} finally { 
		in.close(); 
	} 
}
```

<br>

try-finally 구문을 제대로 사용한 코드에도 결점이 있을 수 있다.

이는 try 블록과 finally 블록에서 모두 예외가 발생할 수 있다는 것 때문.

예를 들어, 파일에서 첫 번째 라인을 읽는 firstLineOfFile 메서드에서 readLine 메서드가 예외를 던지고, close 메서드도 같은 이유로 실패한다면 두 번째 예외가 첫 번째 예외를 완전히 덮어버리게 된다.

그 결과, 첫 번째 예외와 관련된 정보가 스택 추적 내역에서 사라져 실제 디버깅이 어려워진다.

이 문제를 해결하려면 두 번째 예외 대신 첫 번째 예외를 기록하도록 코드를 수정해야 하고  이렇게 하면 코드가 복잡해져 가독성이 떨어지게 된다.

### Try-with Resource 사용하기

Try-with Resource를 사용하려면 `AutoCloseable`이 구현하거나 `extend` 되어야 함.

```java
public interface AutoCloseable {  
    /**  
	..//
     */    void close() throws Exception;  
}
```
<br>

`void close()` 만 덩그러니 정의한 인터페이스다.

자바 라이브러리와 수많은 서드파티 라이브러리들의 수많은 클래스와 인터페이스가 AutoCloseable을 구현하거나 extend 해두었다.


#### Good case:

'try-with-resources'를 사용하여 파일 스트림 관리하기

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class TryWithResourcesExample {
    public static void main(String[] args) {
        String fileName = "example.txt";
        
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

<br>

`try-with-resources`는 exception이 발생하더라도 try 블록이 끝나면 `BufferedReader`가 자동으로 닫히도록 보장한다. \ 
이를 통해 리소스 누출 위험을 줄이고, 명시적인 `finally` 블록이 필요하지 않아 코드를 간단하게 만든다.

<br>

#### Bad case:

'try-with-resources'를 사용하지 않아 리소스 누출 가능성 높아짐.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class MissingTryWithResourcesExample {
    public static void main(String[] args) {
        String fileName = "example.txt";
        BufferedReader reader = null;
        
        try {
            reader = new BufferedReader(new FileReader(fileName));
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

<br>

이 예에서 `try-with-resources`를 사용하지 않으면 `finally` 블록 이전에 Exception이 발생하면 리소스 누출 가능성이 있음.\

또한 코드를 더 길고 유지보수가 어렵게 만듬.

#### Tips for using `try-with-resources` effectively

1. `AutoCloseable` 인터페이스를 구현하는 리소스를 다룰 때 항상 `try-with-resources`를 사용하여 리소스를 자동으로 닫고 리소스 누출 위험을 줄이는 것이 좋음.
    
2.  여러 리소스를 하나의 `try-with-resources` 문에서 세미콜론으로 구분하여 선언할 수 있음

<br>

```java
try (BufferedReader reader1 = new BufferedReader(new FileReader(file1));
     BufferedReader reader2 = new BufferedReader(new FileReader(file2))) {
    // Process the files
}

```
<br>

3. 여러 개의 리소스를 사용하는 경우에는, `try-with-resources` 문에서 선언된 리소스들은 모두 독립적이어야 함. \ 
   만약 하나의 리소스가 다른 리소스에 의존한다면, 올바른 리소스의 종료 순서를 유지하기 위해 `try-with-resources` 문을 중첩시켜야 함.
    
4.  3.  리소스 종료 도중 예외가 발생하면, 모든 리소스는 닫히지만, 전파된 첫 번째 예외만이 호출자에게 전달됨. \
   추가적인 예외는 suppressed 될 수 있으며, `Throwable.getSuppressed()` 메소드를 사용하여 접근할 수 있음.

5. `AutoCloseable` 을 구현해서 닫아야 하는 자원을 뜻하는 클래스를 작성해서 사용.

### `AutoCloseable`  구현하기


 닫아야 하는 자원을 뜻하는 클래스를 작성한다면 위에서 말한  `AutoCloseable`을 `extend`해서 클래스를 생성할 수있음.


Java Sql Connection 예제

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class CustomDatabaseConnection implements AutoCloseable {

    private final Connection connection;

    public CustomDatabaseConnection(String url, String user, String password) throws SQLException {
        this.connection = DriverManager.getConnection(url, user, password);
    }

    public Connection getConnection() {
        return connection;
    }

    @Override
    public void close() throws SQLException {
        connection.close();
    }
}

```

사용 예제

```java

public class DatabaseExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/myDatabase";
        String user = "myUser";
        String password = "myPassword";

        try (CustomDatabaseConnection dbConnection = new CustomDatabaseConnection(url, user, password)) {
            // Perform database operations using dbConnection.getConnection()
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```



### 참조 

-   `try-with-resources`: [https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)
    
-   Better Resource Management with Java SE 7: [https://www.oracle.com/technical-resources/articles/java/trywithresources.html](https://www.oracle.com/technical-resources/articles/java/trywithresources.html)

- Effective Java Item 9
