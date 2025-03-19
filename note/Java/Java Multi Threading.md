## 1. 멀티 스레드(Multi Thread)의 개념

### 1.1 프로세스(Process)

`프로세스`는 컴퓨터에서 **실행중인 프로그램**을 말합니다. OS가 프로그램을 실행시키면 그 프로그램은 프로세스로 변환되며, 실행에 필요한 자원을 할당받아 작업을 수행합니다.  

쉽게 말하자면, OS에서 실행 중인 하나의 애플리케이션, 즉 작업 관리자에서 프로세스 탭에 올라와 있는 애플리케이션 하나를 하나의 프로세스라고 부릅니다. 단적인 예로, 우리가 크롬 브라우저를 사용할 때, **각 탭이 하나의 프로세스**입니다. 

> 공장으로 비유하자면 한 명의 작업자라고 생각할 수 있습니다.

>[!note]
>1. **프로세스 제어 블록(PCB)**: 운영체제는 프로세스를 관리하기 위해 각 프로세스에 대한 중요한 정보를 담고 있는 구조체를 유지합니다. 이를 **프로세스 제어 블록(PCB)** 이라고 하며, 프로세스 상태, 프로그램 카운터, CPU 레지스터 정보 등을 포함합니다.
>2. **멀티태스킹**: 현대 운영체제는 여러 프로세스를 동시에 실행할 수 있는 `멀티태스킹`을 지원합니다. CPU는 빠르게 여러 프로세스를 교차하며 실행하므로, 여러 프로세스가 동시에 실행되는 것처럼 보입니다. 프로세스에 국한되어서 멀티태스킹이 진행되면 `멀티프로세싱`이라고도 불립니다.

### 1.2 스레드

**스레드**(Thread)는 **프로세스 내에서 실행되는 가장 작은 작업 단위**입니다. 하나의 프로세스는 여러 스레드를 가질 수 있으며, 각 스레드는 독립적으로 실행될 수 있습니다.

스레드는 프로세스의 자원을 공유하면서 동시에 실행될 수 있어, **멀티태스킹**을 효율적으로 처리하는 데 도움을 줍니다. 각 스레드는 별도의 **실행 흐름**을 가지지만, 같은 프로세스 내에서 **메모리, 파일, 네트워크 자원 등을 공유**합니다.

> 공장으로 비유하자면 작업자의 손을 얘기합니다.

>[!note]
>1. **경량화된 프로세스**: 스레드는 **경량 프로세스**라고 불리기도 합니다. 프로세스와 달리 스레드는 생성할 때 운영체제로부터 별도의 자원을 할당받지 않고, 부모 프로세스의 자원을 공유합니다.
>2. **자원 공유**: 같은 프로세스 내에서 실행되는 스레드들은 **코드, 데이터, 파일 핸들, 힙 메모리** 등을 공유합니다. 따라서 스레드 간에 데이터를 주고받거나 자원을 공유하는 것이 상대적으로 간단합니다.
>3. **독립적인 실행 흐름**: 각 스레드는 독립적인 **프로그램 카운터, 스택, 레지스터 집합**을 가집니다. 이것은 스레드들이 동시에 다른 작업을 처리할 수 있도록 해줍니다.

>[!important]
>프로세스와 스레드의 차이 :
>- 프로세스 : 독립적인 실행 환경을 갖추고, 메모리 및 자원을 별도로 할당받는 작업 단위
>- 스레드 : 프로세스 내에서자원을 공유하면서 실행되는 작은 작업 단위  

### 1.3 멀티 스레드

**멀티스레드(Multithreading)** 는 **하나의 프로세스 내에서 여러 스레드를 동시에 실행**하는 방식입니다. 이를 통해 프로그램은 여러 작업을 병렬로 처리할 수 있으며, CPU 자원을 효율적으로 활용할 수 있게 됩니다.

멀티스레딩은 한 프로그램이 **여러 스레드를 통해 병렬로 작업을 수행**하므로, 실행 속도를 높이고 응답성을 개선하는 데 중요한 역할을 합니다.

>[!note]
>멀티스레드의 장점:
>1. **응답성 향상**: 스레드 간에 작업을 나눠서 실행하므로, **사용자 인터페이스(UI)와 백그라운드 작업을 동시에 처리**할 수 있습니다. 이로 인해 프로그램이 멈추지 않고 지속적으로 응답할 수 있습니다.
>
>2. **작업 효율성**: 여러 스레드가 동시에 실행됨으로써 작업을 병렬로 처리할 수 있어, **대량의 데이터 처리나 복잡한 연산**에서 성능을 향상시킬 수 있습니다.
>
>3. **자원 절약**: 새로운 프로세스를 생성하는 것보다 스레드를 생성하는 것이 훨씬 적은 자원을 요구하므로, **오버헤드가 적고 효율적**입니다.

>[!warning]
>멀티스레드의 단점:
>1. **동기화 문제**: 여러 스레드가 동시에 같은 자원에 접근할 수 있기 때문에, **데이터 경쟁**(Race condition)이나 **데드락**(Deadlock) 같은 문제가 발생할 수 있습니다. 이를 해결하기 위해 스레드 간 동기화를 잘 관리해야 합니다.
>2. **디버깅의 복잡성**: 여러 스레드가 동시에 실행되면 **실행 순서를 예측하기 어려워** 디버깅이 복잡해질 수 있습니다. 특정 상황에서 발생하는 버그를 추적하기 힘든 경우가 많습니다.

---

## 2. Multi-Thread in Java

### 2.1 Thread 생성
#### 2.1.1 Thread 생성 : Thread 클래스로부터 직접 생성

`new`를 통해 Thread 클래스 객체를 생성 후 `start` 메서드를 통해 **다른 스레드에서 할 작업을 할당**하는 방법입니다. Thread객체를 생성할 때는 `Runnable 인터페이스를 구현한 클래스 객체`를 **매개변수**로 받습니다.

```java
class MyProgram {
    public static void main(String[] args) {
        MyTask task = new MyTask("Task 1");

        // Thread 생성 시 Runnable 객체(MyTask)를 전달
        Thread subThread1 = new Thread(task, "Thread-1");
        Thread subThread2 = new Thread(task, "Thread-2");

        // 두 스레드를 시작
        subThread1.start();
        subThread2.start();
    }
}

class MyTask implements Runnable {
    private String taskName;

    public MyTask(String taskName) {
        this.taskName = taskName;
    }

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            // 현재 스레드 이름을 출력
            System.out.println(
	            Thread.currentThread().getName() + 
	            ": " + taskName + " is running - " + i);
        }
    }
}
```

**모든 자바 어플리케이션은 Main Thread가 main() 메소드를 실행하면서 시작됩니다.** 
예외는 없습니다. 이러한 Main Thread 흐름 안에서 싱글 스레드가 아닌 멀티 스레드 어플리케이션은 필요에 따라 작업 쓰레드를 만들어 병렬로 코드를 실행할 수 있습니다. 싱글 스레드 같은 경우 메인 스레드가 종료되면 프로세스도 종료되지만, 멀티 스레드는 **메인 스레드가 종료되더라도 실행 중인 스레드가 하나라도 있다면 프로세스는 종료되지 않습니다.**


>[!note]
>익명 구현체 방식으로 아래와 같이 구현은 가능하나, 가독성이 떨어지기에 추천하지 않습니다.  
>
>```java
>public static void main(String args[]){
>	Runnable task = new Runnable() {
>		public void run() {
>			int sum = 0;
>			for (int index = 0; index < 10; index++) {
>				sum += index;
>				System.out.println(sum);
>			}
>			System.out.println( Thread.currentThread() + "최종 합 : " + sum);
>		}
>	};
>	Thread subTread1 = new Thread(task);
>	Thread subTread2 = new Thread(task);
>	subTread1.start();
>	subTread2.start();
>}
>```


#### 2.1.2 Thread 생성 : Thread 하위 클래스로부터 직접 생성

```java
public static void main(String args[]){
    Thread subTread1 = new CustomThread();
    
    // 익명 객체 생성
    Thread subTread2 = new Thread() {
        public void run() {
            int sum = 0;
            for (int index = 0; index < 10; index++) {
                sum += index;
                System.out.println(sum);
            }
            System.out.println( Thread.currentThread() + "최종 합 : " + sum);
        }
    };
    
    subTread1.start();
    subTread2.start();
}

// Thread클래스를 상속한 클래스
public class CustomThread extends Thread {
    
    @Override
    public void run() {
        int sum = 0;
        for (int index = 0; index < 10; index++) {
            sum += index;
            System.out.println(sum);
        }
        System.out.println( Thread.currentThread() + "최종 합 : " + sum);

    }
}
```

#### 2.1.3 데몬 스레드

데몬 스레드는 메인 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드로 주 스레드가 종료되면 데몬 스레드 더는 존재 의미가 없기에 강제로 종료됩니다. 워드의 자동 저장 기능을 예로 들을 수 있습니다. 데몬 스레드를 만드는 방법은 스레드를 만들고 해당 스레드에 `setDaemon(true);` 메소드를 세팅하는 것입니다.

```java
public static void main(String args[]){
    Thread deamon = new Thread(()->{
        while ( true ) {
            System.out.println(" 데몬 스레드가 실행 중입니다.");
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
    });
    
    deamon.setDaemon(true);
    deamon.start();
    
     try {
        Thread.sleep(10);
    } catch (InterruptedException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```

### 2.2 Thread 우선 순위

#### 2.2.1 동시성과 병렬성

멀티 스레드는 `동시성( Concurrency )` 또는 `병렬성( Parallelism )`으로 실행됩니다. 

- `동시성`이랑 멀티작업을 위해 1개의 코어로 스레드마다 돌아가면서 조금씩 실행하지만, 너무 빨라 사람의 눈에 보기에는 독립적으로 돌아가는 것처럼 보이는 것을 말합니다.
- `병렬성`이란 스레드마다 각각의 독립적인 Core가 할당되어 독립적인 Core에서 작업이 이루어지는 것을 말합니다. 

코어의 수보다 스레드의 수가 작으면 각각의 코어로 병렬성이 보장되지만 스레드의 개수가 코어보다 많은 경우 스레드를 **어떤 순서에 의해 동시적으로 실행할 것인가를 결정**해 주어야 합니다. 이것을 `스레드 스케줄링`이라 합니다. 이런 **스케쥴링 방식**은 `우선순위( Priority )` 방식과 `순환 할당( Round-Robin )` 방식으로 나누어집니다.

#### 2.2.2 우선순위( Priority ) 방식
우선순위가 높은 스레드가 실행을 더 많이 하도록 스케줄링하는 방법입니다. thread.setPriority(1) // 1 - 우선순위가 가장 낮음 , 10 - 가장 높음 thread .setPriority(Thread.MAX_PRIORITY); // 직관적 코드 MAX_PRIORITY, NORM_PRIORITY, MIN_PRIORITY

#### 2.2.3 순환 할당( Round-Robin ) 방식

시간 할당량을 정해서 하나의 스레드를 정해진 시간만큼만 실행하는 방법입니다. 해당 방식은 JVM안에서 이루어지기 때문에 개발자가 제어할 수 없습니다.

>[!note]
>자바 동기화 관련해서 메소드에 설정할 수는 있으나 이 내용은 Java Thread Monitor에서 계속하겠습니다.

