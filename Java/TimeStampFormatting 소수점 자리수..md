
```text
2023-05-10 17:29:29.8
2023-05-10 17:29:29.81
2023-05-10 17:29:29.813
2023-05-10 17:29:29.8134
```

이 타임스탬프들은 초의 소수점 이하 자릿수가 1자리에서 4자리에 이르기까지 다양함. 

이를 처리하기 위해, 먼저 Java의 기본적인 날짜 및 시간 파싱 기능을 사용할 수 있음.

```java
public class Main {

    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.S");
        String[] timeStamps = {
            "2023-05-10 17:29:29.8",
            "2023-05-10 17:29:29.81",
            "2023-05-10 17:29:29.813",
            "2023-05-10 17:29:29.8134"
        };

        for (String timeStamp : timeStamps) {
            LocalDateTime dateTime = LocalDateTime.parse(timeStamp, formatter);
            System.out.println(dateTime);
        }
    }
}

```

이 코드를 실행하면, `DateTimeParseException`이 발생. 

이는 고정된 패턴으로 파싱을 시도했기 때문이며, 소수점 이하의 자릿수가 고정 패턴과 일치하지 않아 발생하는 문제.

```java
public class Main {

    public static void main(String[] args) {
        String timeStamp = "2023-05-10T17:29:29.813Z";
        TemporalAccessor ta = DateTimeFormatter.ISO_INSTANT.parse(timeStamp);
        Instant instant = Instant.from(ta);
        System.out.println(instant);
    }
}

```

Java 8의 `DateTimeFormatter.ISO_INSTANT`를 사용해도 같은 문제가 발생.

`ISO_INSTANT`는 ISO 8601 날짜 및 시간 표준을 따르는 문자열을 처리. 

이 표준은 일반적으로 '2023-05-10T17:29:29.813Z'와 같은 형식으로 표현. 

따라서 문자열 데이터는 이 표준과 맞지 않음.

### 해결방법

Java 8의 `DateTimeFormatterBuilder`를 사용하여 유연한 날짜 및 시간 파싱 패턴을 만들 수 있음. 

`DateTimeFormatterBuilder`는 다양한 형식의 날짜 및 시간 문자열을 처리할 수 있는 파싱 패턴을 만드는 fluent API를 제공함.

```java
public class Main {

    public static void main(String[] args){
        DateTimeFormatter formatter = new DateTimeFormatterBuilder()
                .appendPattern("yyyy-MM-dd HH:mm:ss")
                .appendFraction(ChronoField.NANO_OF_SECOND, 1, 9, true)
                .toFormatter();

        String[] timeStamps = {
                "2023-05-10 17:29:29.8",
                "2023-05-10 17:29:29.81",
                "2023-05-10 17:29:29.813",
                "2023-05-10 17:29:29.8134"
        };

        for (String timeStamp : timeStamps) {
            LocalDateTime dateTime = LocalDateTime.parse(timeStamp, formatter);
            System.out.println(dateTime);
        }
    }
}


```

이 코드는 `DateTimeFormatterBuilder`를 사용하여 소수점 이하 초의 자릿수를 1자리에서 9자리까지 처리할 수 있는 `DateTimeFormatter`를 생성.

이제 위의 타임스탬프 문자열을 성공적으로 파싱할 수 있음.

