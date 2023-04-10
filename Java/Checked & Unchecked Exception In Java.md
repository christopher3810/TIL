![](https://www.baeldung.com/wp-content/uploads/2019/11/Throwable-3.png)  

자바에서 error와 exception는 모두 **`java.lang.Throwable`** 클래스의 하위 클래스.
error는 합리적인 애플리케이션이 처리하지 않아야 할 심각한 문제를 나타내는 반면, exception는 애플리케이션이 처리하길 원하는 조건.

exception는 두 가지 카테고리로 나뉨 , checked exception과 unchecked exception.

  
## checked exception & **unchecked exception**

| 카테고리 | 상속 | 예외 처리 | 체크 지점 | 예제 |
| --- | --- | --- | --- | --- |
| checked exception | 직접 또는 간접적으로 Exception (RuntimeException 제외) 상속 | 메서드 시그니처에서 처리하거나 선언해야 함 | 컴파일 시 검사 | IOException, FileNotFoundException |
| unchecked exception | 직접 또는 간접적으로 RuntimeException 상속 | 메서드 시그니처에서 명시적으로 처리하거나 선언하지 않아도 됨 즉 `선택` | 런타임에서 검사 | NullPointerException, ArithmeticException |

  

### checked exception

체크 예외는 try-catch 블록에서 처리하거나 메서드의 **`throws`** 절에서 선언해야 하는 예외.

이러한 예외는 컴파일 시간에 컴파일러에 의해 검사.

체크 예외가 처리되거나 선언되지 않으면 코드는 컴파일되지 않음.

체크 예외의 예로는 **`IOException`**, **`FileNotFoundException`** 및 ****`ClassNotFoundException`****이 있음

  
### unchecked exception

언체크 예외는 ****`RuntimeException`****에서 상속된 예외.

이러한 예외는 명시적으로 처리하거나 메서드의 **`throws`** 절에서 선언할 필요가 없음.

언체크 예외는 런타임에 검사되며 컴파일러가 처리를 강제하지 않음.

언체크 예외의 예로는 **`NullPointerException`**, **`ArithmeticException`**, **`ArrayIndexOutOfBoundsException`** 및 ****`IllegalArgumentException`****이 있음.


### Transaction 에서의 unchecked exception& error

**트랜잭션 메서드 실행 중에 발생하는 uncheckedException(즉, RuntimeException과 그 서브클래스) 또는 error는 default로 롤백 대상으로 간주됨.


### Custom Exception 예제

```java
public class InvalidFileFormatException extends Exception {
    public InvalidFileFormatException(String message) {
        super(message);
    }
}
```


```java
public class DatabaseOperationException extends RuntimeException {
    public DatabaseOperationException(String message, Throwable cause) {
        super(message, cause);
    }
}
```


```java
private final DatabaseService databaseService;  
  
public FileProcessor(DatabaseService databaseService) {  
    this.databaseService = databaseService;  
}  
  
public void processFile(String filePath) throws InvalidFileFormatException {  
    File file = new File(filePath);  
    if (!file.getName().endsWith(".zip")) {  
        throw new InvalidFileFormatException("Invalid file format. Only ZIP files are supported.");  
    }  
  
    try (ZipFile zipFile = new ZipFile(file)) {  
        zipFile.stream()  
            .filter(entry -> !entry.isDirectory())  
            .forEach(entry -> {  
                Path entryPath = Paths.get(entry.getName());  
                try {  
                    String content = Files.readString(entryPath);  
                    databaseService.saveContent(entry.getName(), content);  
                } catch (IOException e) {  
                    throw new DatabaseOperationException("Error reading file entry: " + entry.getName(), e);  
                }  
            });  
    } catch (IOException e) {  
        throw new DatabaseOperationException("Error processing the ZIP file.", e);  
    }  
}
```

```java
import org.springframework.stereotype.Service;

@Service
public class DatabaseService {
    public void saveContent(String name, String content) {
        // Simulate storing the content in a database.
        System.out.println("Storing content in the database: " + name);
    }
}

```

이 파일 프로세서에서는 먼저 파일이 예상 형식(.zip 파일)인지 확인. 

그렇지 않은 경우 InvalidFileFormatException을 던짐. 

그런 다음 zip 파일의 내용을 읽고 데이터베이스 서비스를 사용하여 데이터베이스에 저장. 

이 과정에서 오류가 발생하면 DatabaseOperationException이 발생.