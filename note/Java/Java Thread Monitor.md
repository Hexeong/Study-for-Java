## 동기화의 탄생 배경

멀티 스레드(Multi-Thread) 환경에서는 해당 스레드의 실행 순서에 대해 보장되지 않습니다. CPU 상에서 빠르게 프로세스가 바뀌면서 실행되기에 우리 눈에 동시에 실행되는 것처럼 보일 뿐이지, CPU의 코어 수만큼 스레드들을 실행시킬 수 있고, 해당 스레드들은 특정 알고리즘(Round Robin 등)에 의해 교체됩니다.

이런 환경에서 작업별로 스레드를 나눠 동시에 실행시킨다고 가정하겠습니다. 이 때 1번 작업은 반드시 2번 작업보다 먼저 수행되어야 한다고 했을 때, 둘의 작업 시간이 다르다면, 1번 작업이 반드시 2번 작업보다 먼저 수행된다는 것을 보장할 수 없습니다.

이에 대해 작업 순서를 보장하고자, `동기화(Synchronization)`라는 개념이 등장한 것입니다.

---

## 동기화(Synchonization) in Java

### Producer - Consumer 문제

총 세 개의 Thread가 있습니다. `SharedBuffer` Class 라는 공유 자원을 두고, `Producer` Class가 자원을 생산하고, `Consumer` Class가 자원을 소비하는 프로그램입니다.
하지만 Producer가 자원을 생산해야, Consumer가 자원을 소비할 수 있다는 제약이 있습니다. 해당 프로그램의 경우 멀티 스레드로 동작하기에 둘의 순서 보장을 하기 위해 동기화를 사용했습니다. 

해당 동기화를 사용하기 위해 `synchronized` 키워드와 `wait()`, `notify()` 메서드가 사용됩니다.

#### 동작 순서
1. `Producer`가 `produce()` 메서드를 통해 `SharedBuffer`에서 data를 생산하며, 데이터를 생산할 때마다 `notify()`를 호출하여 소비자에게 알립니다.
      1. `Consumer`는 `Producer`와 동시에 동작하나, `consume()` 메서드에서 `while(!isDataAvailable) wait();` 구문에 의해 blocking 되어 있는 상태입니다.
2. 1. 만약 `produce()` 메서드에서 `notify()` 메서드를 호출할 경우, `Consumer` 스레드에 걸려 있는 blocking이 해제되고, 실행되기 시작합니다.
3. 이후, `Consumer`가 `consume()` 메서드를 통해 데이터를 소비하며, 데이터를 소비할 때마다 `notify()`를 호출하여 생산자에게 알립니다.
4. 1. 똑같이 동 시간대의 `produce()` 메서드는 `wait()` 함수에 의해 `Producer` 스레드를 대기 상태로 전환시킵니다.
5. 2. `consume()` 메서드에 의해 호출된 `notify()`로 `Producer` 스레드를 깨워, 실행되기 시작합니다.

```java
class SharedBuffer {
    private int data;
    private boolean isDataAvailable = false;

    // 데이터를 생산하는 메서드
    public synchronized void produce(int value) 
          throws InterruptedException {
        while (isDataAvailable) {
            // 데이터가 이미 있을 경우 대기
            wait();
        }
        data = value;
        System.out.println("Produced: " + data);
        isDataAvailable = true; // 데이터가 존재함을 표시
        notify(); // 소비자 스레드에게 알림
    }

    // 데이터를 소비하는 메서드
    public synchronized void consume() throws InterruptedException {
        while (!isDataAvailable) {
            // 데이터가 없을 경우 대기
            wait();
        }
        System.out.println("Consumed: " + data);
        isDataAvailable = false; // 데이터가 없어짐을 표시
        notify(); // 생산자 스레드에게 알림
    }
}

class Producer implements Runnable {
    private final SharedBuffer buffer;
    
    public Producer(SharedBuffer buffer) {
        this.buffer = buffer;
    }
    
    @Override
    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                buffer.produce(i); // 1부터 5까지의 데이터 생산
                Thread.sleep(500); // 0.5초 간격으로 생산
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

class Consumer implements Runnable {
    private final SharedBuffer buffer;

    public Consumer(SharedBuffer buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                buffer.consume(); // 생산된 데이터 소비
                Thread.sleep(1000); // 1초 간격으로 소비
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

public class ProducerConsumerExample {
    public static void main(String[] args) {
        SharedBuffer buffer = new SharedBuffer();

        // 생산자 스레드와 소비자 스레드 생성
        Thread producerThread = new Thread(new Producer(buffer));
        Thread consumerThread = new Thread(new Consumer(buffer));

        // 스레드 시작
        producerThread.start();
        consumerThread.start();
    }
}

// 출력 결과 : 
// Produced: 1
// Consumed: 1
// Produced: 2
// Consumed: 2
// Produced: 3
// Consumed: 3
// Produced: 4
// Consumed: 4
// Produced: 5
// Consumed: 5
```

해당 코드 처럼, Multi Thread 환경에서 작업 순서에 대해 동기화로 보장이 가능합니다.


---

## Thread Monitor in Java

### Thread Monitor가 필요한 이유

Java에서 Thread Monitor가 필요한 이유는 멀티 스레드 환경에서 **공유 자원**에 대한 동기화 문제를 해결하기 위해서입니다. 위에서 봤듯이 Producer - Consumer 문제 또한 이에 해당합니다.
여러 스레드는 동시에 실행되기 때문에 하나의 자원에 동시에 접근이 가능해집니다. 해당 경우, Low - Level에서는 + 연산 또한 2 ~ 3개의 명령어로 이루어져 있습니다. 

만약 + 연산 도중 Dispatcher에 의해 작업이 바뀌고, 다른 스레드가 값을 변경한 다음, 다시 원래 스레드가 연산하기 시작하는 경우, 즉 두 개의 스레드가 공유 변수에 대해 동시에 값을 수정하려고 하면 공유 자원에 대해 무결성이 깨질 수 있습니다.

멀티 스레드 환경에서 공유 자원에 대한 데이터 무결성을 보장하기 위해 나타난 개념이 `동기화(Synchronization)`이고, Java 언어에서 올바른 동기화 메커니즘을 제공하는 것을 **Thread Monitor**라고 합니다.

### 사용법

사실 위의 코드가 Thread Monitor를 사용한 코드입니다. Thread Monitor를 사용하기 위해 synchronized 키워드를 사용하여 해당 메서드에 한 번에 하나의 스레드만 접근할 수 있게 모니터 락을 확보하는 것입니다.

아래의 예시는 다른 예시입니다.

```java
class SharedResource {
    private int counter = 0;
    
    // synchronized 메서드를 통해 동기화
    public synchronized void increment() {
        counter++;
        System.out.println(
           Thread.currentThread().getName() + 
           " incremented counter to: " + counter);
    }
    
    // counter 값 출력 메서드 (동기화 없음)
    public void printCounter() {
        System.out.println("Counter: " + counter);
    }
}

class WorkerThread extends Thread {
    private final SharedResource sharedResource;
    
    public WorkerThread(SharedResource sharedResource) {
        this.sharedResource = sharedResource;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            sharedResource.increment(); // 동기화된 메서드 호출
            try {
                Thread.sleep(100); // 짧은 시간 대기
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}

public class ThreadMonitorExample {
    public static void main(String[] args) {
        SharedResource sharedResource = new SharedResource();
        
        // 두 개의 스레드가 같은 자원을 공유함
        Thread thread1 = new WorkerThread(sharedResource);
        Thread thread2 = new WorkerThread(sharedResource);
        
        // 스레드 시작
        thread1.start();
        thread2.start();
        
        // 메인 스레드에서 두 스레드가 끝날 때까지 대기
        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        // 최종 counter 값 출력
        sharedResource.printCounter();
    }
}
```

>[!note]
>`synchronized` 키워드로 모니터 락을 확보해, 한번에 하나의 스레드만 해당 객체의 동기화된 코드블록에 접근할 수 있게 한다면 왜 `wait()`와 `notify()`가 필요한지 궁금할 수 있습니다.
>
>하지만 두 메서드는 스레드간 협력을 위해 사용됩니다. 
>만약 위의 코드에서 1번 thread가 작업시간이 10초이고, 2번 thread의 작업이 3초인 경우, 1번 thread가 1번 실행된 다음, 2번 thread가 4번 실행되는 게 계속 반복될 것입니다.
>
>하지만, 무조건 1번 thread 이후, 2번 thread가 1번만 실행되어야 한다면? 위의 코드는 예상과 다르게 동작하는 것입니다.
>
>이처럼 스레드 간 통신과 작업 조정이 필요한 경우에 사용된다고 생각하면 됩니다.

