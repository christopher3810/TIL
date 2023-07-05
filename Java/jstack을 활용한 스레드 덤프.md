
`jstack.exe`는 Java Development Kit(JDK)와 함께 제공되는 유틸리티로, Java 스레드 덤프를 생성하는데 사용됨.

글에서는 Windows와 Linux 환경에서 `jstack.exe`를 사용하는 방법 및 이점에 대해서 다시 한번 정리하려고 한다.

### Windows와 Linux에서 jstack.exe로 Thread Dump 생성하기

먼저, 스레드 덤프를 생성하고 싶은 Java 프로세스의 PID(Process ID)를 찾아야 함. 

Windows에서는 `jps` 명령어를, Linux에서는 `ps -ef | grep java`를 사용하여 찾을 수 있음.

**Windows**

```bash
C:\>jps
3533 MyJavaProcess
```

**Linux**

```bash
$ ps -ef | grep java
user1 3533  1  5 08:30 ?    00:02:35 /usr/bin/java -jar /path/to/my/java/application.jar
```

위의 예에서, MyJavaProcess 또는 application.jar의 **PID는 3533**임.

이제 이 PID를 사용하여 `jstack.exe`로 스레드 덤프를 생성할 수 있음. 

리다이렉션을 사용하여 결과를 파일로 저장할 수 있음.

**WIndow & Linux**

```bash
${jdk경로}/bin/jstack.exe 3533 > threadDump.txt //win
${jdk경로}/bin/jstack 3533 > threadDump.txt //linux
```

이 명령은 스레드 덤프를 생성하고 `threadDump.txt` 파일에 저장함.

### 스레드 덤프의 세부 정보

스레드 덤프는 많은 세부 정보를 포함하고 있음. 

한 가지 중요한 세부 정보는 `nid` 임. 이는 각 스레드의 고유 식별자로, `16진수로 표시됨`.

```java
"main" #1 prio=5 os_prio=0 tid=0x00007f312c080000 nid=0x6ec3 runnable [0x00007f312d4f5000]
```

위의 라인에서 "main"은 스레드의 이름이고, "nid=0x6ec3"는 스레드의 ID임.

#### jstack.exe의 추가 옵션  -l 과 -e

jstack 도구는 몇 가지 추가적인 옵션을 제공함.

`-l` 옵션을 사용하면, 추가로 락 정보를 출력함. 

```bash
jstack -l 3533 > threadDumpWithLocks.txt
```

```bash
"main" #1 prio=5 os_prio=0 tid=0x00007f312c080000 nid=0x6ec3 runnable [0x00007f312d4f5000]
   java.lang.Thread.State: RUNNABLE
  at MyProgram.runProgram(MyProgram.java:13)
  - locked <0x000000076abf4a28> (a MyProgram)

```

`locked <0x000000076abf4a28> (a MyProgram)` 라는 라인이 추가적인 락 정보를 보여줌

> 여기서 말하는 락은?
> `synchronized 키워드`를 사용해서 특정 부분의 코드 또는 메소드를 한 번에 하나의 스레드만 실행하도록 제한하는 동기화 블록 또는 메소드에 의해 발생하는 락.

locked <0x000000076abf4a28> (a MyProgram) 이라는 표기는 `스레드가 현재 MyProgram 객체에 대한 락을 보유하고 있음을 의미`합니다. 

그러므로 해당 스레드가 실행 중이며, 동시에 다른 스레드들이 동기화된 메서드나 블록에 접근하는 것을 막고 있음.

따라서 `이 락 때문에 메서드 실행이 막히지 않음`. 

오히려 `이 락이 현재 스레드에게는 실행 권한을 주면서 다른 스레드들로부터 접근을 제한`하고 있음.

락이 걸린 상태에서 스레드가 움직이지 않거나 리소스만 먹고 있다는 것은 아님. 

위 예시에서 `스레드 상태는 RUNNABLE`임. 즉, 이 스레드는 실행 중이거나, 실행 가능한 상태를 의미. 

이 스레드가 멈추지 않고 계속 실행 중이며, 현재는 `MyProgram 객체에 락을 걸어 동기화된 부분을 실행`하고 있다는 것을 의미.
    
`<0x000000076abf4a28>`은 `객체의 해시 코드를 16진수로 표현`한 것입니다. 

이 값은 JVM에 의해 해당 객체에 고유하게 부여된 값을 나타내며, 디버깅 시 해당 객체를 식별하는 데 사용됩니다. 

따라서 여기에서 `0x000000076abf4a28은 MyProgram 객체를 고유하게 식별하는 값`.

따라서, `스레드 덤프나 힙 덤프 등에서 동일한 객체를 추적하는데 사용`될 수 있음.

`-e` 옵션을 사용하면, 상세한 에러 메시지를 출력함.

```bash
jstack -e 3533 > threadDumpWithErrors.txt
```

```bash
Exception in thread "main" java.lang.NullPointerException
  at MyProgram.runProgram(MyProgram.java:20)

```

위의 출력에서, `Exception in thread "main" java.lang.NullPointerException `라는 라인은 `jstack -e` 명령어로 얻어진 추가적인 에러 정보를 보여줌.

### 스레드 덤프 스택의 순서

스레드 덤프 내의 스택 정보는 아래에서부터 위로 순서대로 기록됨. 

가장 아래의 정보는 최근에 실행된 메서드를 나타내고, 그 위로 갈수록 이전에 실행된 메서드를 나타냄.

### Spring Boot 서비스의 스레드 덤프 생성하기

Spring Boot 서비스를 실행중인 경우, 보통 내장 Tomcat의 PID를 대상으로 스레드 덤프를 생성. 

예를 들어, `mySpringBootApp.jar`이 실행 중이라면 `jps` 또는 `ps -ef | grep java`를 사용하여 해당 PID를 찾은 후, `jstack` 명령을 실행하면 됨.

찾아지지 않은 경우 window에서는 작업관리자 에 pid 값을 

linux의 경우에는

Linux 환경에서는 `ps` 명령어와 `grep` 명령어를 조합하여 PID를 찾을 수 있습니다. 그러나 더 간편하게 PID를 파악하려면 `pgrep` 명령어를 사용할 수 있음.

```bash
pgrep -f mySpringBootApp.jar
```

위 명령어는 'mySpringBootApp.jar'가 포함된 모든 프로세스의 PID를 출력. 

이를 통해 Spring Boot 애플리케이션의 PID를 쉽게 찾을 수 있음.

### 스레드 덤프를 통해 얻을 수 있는 이점

`Performance Tuning`

스레드 덤프는 애플리케이션 성능 튜닝에 필수적인 정보를 제공.

`Deadlock Detection`

스레드 덤프를 통해 애플리케이션 내의 데드락을 쉽게 발견할 수 있음.

`CPU Usage Analysis`

스레드 덤프는 CPU 사용률 분석에 도움이 됨.

`Memory Leak Detection`

스레드 덤프를 통해 메모리 누수를 찾아낼 수 있음.
