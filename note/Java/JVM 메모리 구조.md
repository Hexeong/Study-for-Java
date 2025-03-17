
## 목차
1. [JVM이란?](#JVM이란?)
2. [JVM 메모리 구조](#JVM-메모리-구조)
	1. [1. Class Loader](#1-Class-Loader)
	2. [2. Execution Engine](#2-Execution-Engine)
	3. [3. Garbage Collector](#3-Garbage-Collector)
	4. [4. Runtime Data Area](#4-Runtime-Data-Area)
		1. [4.1 Method Area](#41-Method-Area)
		2. [4.2 Heap Area](#42-Heap-Area)
		3. [4.3 Stack Area](#43-Stack-Area)
		4. [4.4 PC Register](#44-PC-Register)
		5. [4.5 Native Method Stack](#4.5 Native-Method-Stack)

## JVM이란?
JVM 메모리 구조를 설명하기 전에 JVM이 뭔지 알아야 합니다. JVM은 Java Virtual Machine의 약자로, 자바 가상 머신이라고 부릅니다. 그리고 자바와 운영체제 사이에서 **바이트코드를 운영체제에 맞게 실행 프로그램으로 바꿔주는 역할**을 합니다. 즉 자바 프로그램이 *운영체제에 구애 받지 않고* 프로그램을 실행할 수 있도록 도와줍니다.

또한, 가비지 컬렉터를 사용한 메모리 관리도 자동으로 수행하며, 다른 하드웨어와 다르게 레지스터 기반이 아닌 스택으로 동작합니다.

<div align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fo2kwL%2FbtqSmzWHdHV%2FOeIODqCVTN97ioNDCVjiU0%2Fimg.png" alt="자바 프로그램 실행 단계" />
</div>

> [!note]
> 먼저, 자바 컴파일러에 의해 자바 소스 파일은 바이트 코드로 변환됩니다. 그리고 이러한 바이트 코드를 JVM에서 읽어 들인 다음에, 이것저것 복잡한 과정을 거쳐서 어떤 운영체제든 간에 프로그램을 실행할 수 있도록 만드는 것입니다.
>
> 만약, 자바 소스 파일은 리눅스에서 만들었고, 윈도우에서 이 파일을 실행하고 싶다면, 윈도우용 JVM을 설치만 하면 됩니다. 여기서 JVM은 운영체제에 종속적이라는 특징을 가지고 있습니다.

## JVM 메모리 구조

<div align="center" style="background: white">
	<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpjywN%2FbtqSduBXLIK%2F2QEL5c2nEJXRm0cyhvwxF1%2Fimg.png" alt="JVM 구조"/>
</div>

위에서 자바 프로그램 실행 단계에 대해 간략히 소개했는데, 이는 JVM 내부에서 어떻게 동작하는지에 대한 내용입니다. JVM의 구성요소는 크게 보면, **Garbage Collector, Execution Engine, Class Loader, Runtime Data Area**로 4가지로 나눌 수 있습니다.

>[!important]
>JAVA source은 자바 컴파일러에 의해서 바이트 코드 형태인 클래스 파일이 됩니다. 그리고 이 클래스 파일은 클래스 로더가 읽어들이면서 JVM이 수행됩니다. 

### 1. Class Loader
JVM 내로 클래스 파일을 로드하고, 링크를 통해 배치하는 작업을 수행하는 모듈입니다. 런타임 시에 동적으로 클래스를 로드합니다.

### 2. Execution Engine
클래스 로더를 통해 JVM 내의 Runtime Data Area에 배치된 바이트 코드들을 **명렁어 단위**로 읽어서 실행합니다. 최초 JVM이 나왔을 당시에는 *인터프리터* 방식이었기때문에 속도가 느리다는 단점이 있었지만 **JIT 컴파일러 방식**을 통해 이 점을 보완하였습니다. JIT는 바이트 코드를 어셈블러 같은 네이티브 코드로 바꿈으로써 실행이 빠르지만 역시 변환하는데 비용이 발생하였습니다. 이 같은 이유로 JVM은 모든 코드를 JIT 컴파일러 방식으로 실행하지 않고, 인터프리터 방식을 사용하다가 **일정한 기준이 넘어가면 JIT 컴파일러 방식으로 실행합니다.**

### 3. Garbage Collector
Garbage Collector(GC)는 힙 메모리 영역에 생성된 객체들 중에서 참조되지 않은 객체들을 탐색 후 제거하는 역할을 합니다. 이때, GC가 역할을 하는 시간은 언제인지 정확히 알 수 없습니다.

### 4. Runtime Data Area
JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역입니다. 이 영역은 크게 Method Area, Heap Area, Stack Area, PC Register, Native Method Stack로 나눌 수 있습니다.

#### 4.1 Method Area

- **설명**: 클래스 수준의 데이터를 저장하는 메모리 영역으로, 클래스 로더가 클래스를 로드할 때 해당 클래스의 메타데이터, 상수, 정적 변수, 메서드 코드 등을 저장합니다. 모든 스레드가 공유하는 영역입니다.
  한 마디로 메소드 영역은 클래스, 인터페이스, 메소드, 필드, Static 변수 등의 바이트 코드를 보관합니다.
- **역할**:
    - 클래스 및 인터페이스 정보 저장
    - 메서드 정보(메서드의 바이트코드, 이름, 접근 제어자 등) 저장
    - 런타임 상수 풀 저장

#### 4.2 Heap Area

- **설명**: **객체**와 **배열**이 동적으로 생성되는 메모리 영역으로, 모든 스레드가 공유하는 영역입니다. JVM에서 가비지 컬렉션(Garbage Collection)이 이뤄지는 영역이기도 합니다.
  new 키워드로 생성된 객체와 배열이 생성되는 영역입니다. 또한, 메소드 영역에 로드된 클래스만 생성이 가능하고 Garbage Collector가 참조되지 않는 메모리를 확인하고 제거하는 영역입니다.
- **역할**:
    - 객체 인스턴스와 배열 저장
    - 클래스 메서드가 생성한 동적 데이터 저장
    - 프로그램의 전반적인 메모리 관리를 담당
#### 4.3 Stack Area

- **설명**: 각 스레드마다 독립적으로 할당되는 메모리 영역으로, **메서드 호출** 시 생성된 **로컬 변수**와 **프레임 정보**(메서드 호출 스택)를 저장합니다.
  구체적으로는 메서드 호출 시마다 각각의 스택 프레임(그 메서드만을 위한 공간)이 생성합니다. 그리고 메서드 안에서 사용되는 값들을 저장하고, 호출된 메서드의 매개변수, 지역변수, 리턴 값 및 연산 시 일어나는 값들을 임시로 저장합니다. 마지막으로, 메서드 수행이 끝나면 프레임별로 삭제합니다.
- **역할**:
    - 메서드 호출 시마다 프레임 생성
    - 각 프레임에 로컬 변수, 연산 스택, 메서드 결과 등을 저장
    - 메서드 호출이 완료되면 프레임은 사라짐

#### 4.4 PC Register

- **설명**: 각 스레드마다 독립적으로 존재하는 영역으로, **현재 실행 중인 명령어의 주소**(Program Counter)를 저장합니다. 스레드가 어떤 명령을 실행하고 있는지 추적할 수 있습니다.
  구체적으로는 쓰레드가 시작될 때 생성되며, 생성될 때마다 생성되는 공간으로 쓰레드마다 하나씩 존재합니다. 쓰레드가 어떤 부분을 무슨 명령으로 실행해야할 지에 대한 기록을 하는 부분으로 현재 수행중인 JVM 명령의 주소를 갖습니다.
- **역할**:
    - 현재 실행 중인 JVM 명령어의 주소를 저장
    - 스레드의 제어 흐름을 관리


#### 4.5 Native Method Stack

- **설명**: Java가 아닌 다른 언어(예: C, C++)로 작성된 **네이티브 메서드**가 호출될 때 사용하는 스택입니다. JNI(Java Native Interface)를 통해 네이티브 코드를 호출할 때 사용됩니다.
- **역할**:
    - 네이티브 메서드 실행에 필요한 메모리 관리
    - C/C++ 등의 네이티브 언어로 작성된 메서드가 실행될 때 스택 프레임을 생성
