***


# @ComponentScan , @EnableAutoConfiguration , 우선순위


### @ComponentScan
***

@ComponentScan 은 Spring 컨테이너가 지정된 패키지와 그 하위 패키지
(예: @Component, @Service, @Repository 또는 @Controller로 어노테이션된 클래스 등 Spring 관리 컴포넌트)를 검색하도록 지시하는 데 사용됩니다. 
Spring은 컴포넌트를 찾으면 애플리케이션 컨텍스트에 bean으로 등록합니다. 
즉, @ComponentScan은 나중에 종속성을 해결하고 종속성 주입을 수행하는 데 사용할 수 있는 bean을 검색하고 등록하는 일을 담당합니다. 
패키지가 지정되지 않은 경우 현재 클래스 패키지가 루트 패키지로 사용됩니다.


### @EnableAutoConfiguration
***

@EnableAutoConfiguration 어노테이션은 사용자가 추가한 jar 종속성을 기반으로 Spring을 구성하는 방법을 Spring Boot가 "추측(guess)"하도록 지시합니다. 
예를 들어, HSQLDB가 클래스 경로에 있고 데이터베이스 연결 bean을 수동으로 구성하지 않은 경우 Spring은 인메모리 데이터베이스를 자동으로 구성합니다.
즉, Spring Boot는 감지된 종속성을 기반으로 bean, 속성 및 기타 다양한 구성 요소를 자동으로 구성합니다. 

@SpringBootApplication 어노테이션은 기본 속성과 함께 @Configuration, @EnableAutoConfiguration 및 @ComponentScan을 사용하는 것과 동일합니다.


### 우선순위
***
Configuration classes
Configuration classes가 먼저 처리되며, 그 안에 정의된 모든 bean은 다른 곳에 정의된 bean보다 우선합니다. 
이는 Configuration classes가 bean을 생성하고 종속성을 와이어링하는 방법에 대한 명시적인 지침을 제공하기 때문입니다.

@ComponentScan 
다음으로, Spring 컨테이너는 @Component 어노테이션이 있는 클래스와 @Service, @Repository, @Controller와 같은 기타 어노테이션을 검색하고 이에 대한 빈 정의를 생성합니다. 
이러한 빈은 개발자가 명시적으로 정의한 것이므로 auto-configured된 bean보다 우선합니다.

@EnableAutoConfiguration 어노테이션을 활성화합니다
마지막으로, Spring Boot의 자동 구성 기능이 시작되어 명시적으로 정의되지 않은 모든 bean을 자동 구성합니다. 
이러한 bean은 합리적인 기본값을 제공하기 위한 것이며 개발자가 명시적으로 구성하지 않았기 때문에 우선 순위에서 마지막에 고려됩니다.

특정 자동 구성을 제외하거나 @Import, @ImportResource 또는 @Order 어노테이션을 사용하여 구성 클래스의 순서를 변경할 수 도 있습니다.



### 참조
***
https://stackoverflow.com/questions/35005158/what-is-the-difference-between-componentscan-and-enableautoconfiguration-in-sp
https://www.baeldung.com/spring-componentscan-vs-enableautoconfiguration
https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-using-springbootapplication-annotation.html