## JVM

### JVM 에 대해서

- 자바 프로그램 실행환경을 만들어 주는 소프트웨어
- 자바 코드를 컴파일하여 .class 바이트 코드로 만들면 이 코드가 자바 가상 머신 환경에서 실행
- Java는 플랫폼에 종속적이지 않지만 JVM은 플랫폼에 종속적
- interpreter 방식 JIT 방식 혼용
    - interpreter 방식: 한 줄씩 해석, 실행
    - JIT 방식:
        - 바이트코드를 JIT 컴파일러를 이용해 프로그램을 실제 실행하는 시점(바이트코드를 실행하는 시점)에 각 OS에 맞는 Native Code로 변환
        - JIT 컴파일러는 같은 코드를 매번 해석하지 않고, 실행할 때 컴파일을 하면서 해당 코드를 캐싱해버립니다. 이후에는 바뀐 부분만 컴파일하고, 나머지는 캐싱된 코드를 사용
          ![img.png](/resources/images/java-code-실행-구조.png)

### JVM 동작 방식

1. 자바로 개발된 프로그램을 실행하면 JVM은 OS로부터 메모리를 할당합니다.

2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일합니다.

3. Class Loader를 통해 JVM Runtime Data Area로 로딩합니다.

4. Runtime Data Area에 로딩 된 .class들은 Execution Engine을 통해 해석합니다.

5. 해석된 바이트 코드는 Runtime Data Area의 각 영역에 배치되어 수행하며 이 과정에서 Execution Engine에 의해 GC의 작동과 스레드 동기화가 이루어집니다.

### JVM 내부 구조

- 클래스 로더(Class Loader)
    - 프로그램이 실행 중인 런타임에서야 모든 코드가 자바 가상 머신과 연결
    - 동적으로 클래스를 로딩해주는 역할을 하는 것이 바로 클래스 로더(class loader)
    - JVM이 운영체제로부터 할당받은 메모리 영역인 Runtime Data Areas의 Method Area에 배치

- 실행 엔진(Execution Engine)
    - 로드된 바이트코드를 실행하는 런타임 모듈이 실행 엔진
- 런타임 데이터 영역 (Runtime Data Area)
    - JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역

- 가비지 컬렉터(Garbage Collector)
    - 더는 사용하지 않는 메모리를 자동으로 회수
    - 따라서 개발자가 따로 메모리를 관리하지 않아도 되므로, 더욱 손쉽게 프로그래밍을 할 수 있도록 돕는다.
    - Heap 메모리 영역에 생성(적재)된 객체들 중에 참조되지 않은 객체들을 탐색 후 제거하는 역할을 하며 해당 역할을 하는 시간은 정확히 언제인지를 알 수 없습니다.
    - GC역할을 수행하는 스레드를 제외한 나머지 모든 스레드들은 일시정지상태

### JVM 메모리 구조

![img.png](/resources/images/jvm-메모리-구조.png)

- 힙 영역, 메서드 영역은 모든 스레드가 공유해서 사용 (GC의 대상)

힙 영역 (Heap Area)
- 클래스 멤버 변수의 이름, 데이터 타입, 접근 제어자 정보와 같은 각종 필드 정보
- 메서드 정보, 데이터 Type 정보, Constant Pool, static변수, final class 등이 생성되는 영역
- 객체들은 실질적으로 Heap영역에서 생성되고 Method Area이나 Stack Area등 Root Area에서는 Heap Area에 생성된 객체의 주소만 참조하는 형식으로 구성

메서드 영역(Method Area)
- new 키워드로 생성된 객체와 배열이 생성되는 영역
- 주기적으로 GC가 제거하는 영역

- 스택 영역, PC 레지스터, 네이티브 메서드 스택 모든 스레드가 공유해서 사용 (GC의 대상)

스택 영역(Stack Area)
- 지역변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값 등이 생성되는 영역

PC 레지스터 (PC Register)
- Thread가 생성될 때마다 생성되는 영역으로 프로그램 카운터, 즉 현재 스레드가 실행되는 부분의 주소와 명령을 저장하고 있는 영역

네이티브 메서드 스택(Native Method Stack)
1. 자바 이외의 언어(C, C++, 어셈블리 등)로 작성된 네이티브 코드를 실행할 때 사용되는 메모리 영역으로 일반적인 C 스택을 사용합니다.
2. 보통 C/C++ 등의 코드를 수행하기 위한 스택을 말하며 (JNI) 자바 컴파일러에 의해 변환된 자바 바이트 코드를 읽고 해석하는 역할을 하는 것이 자바 인터프리터(interpreter)

![img.png](/resources/images/jvm-실행-구조.png)

### 가비지 컬렉터

- 자바의 메모리 관리 방법 중의 하나로 JVM의 Heap 영역에서 동적으로 할당했던 메모리 영역 중 필요 없게 된 메모리 영역을 주기적으로 삭제하는 프로세스
- 참조가 해제된 객체(unreachable)가 GC 대상

#### 장점

- 개발자 입장에서 메모리 관리, 메모리 누수(Memory Leak) 문제에서 대해 완벽하게 관리하지 않아도 되어 오롯이 개발에만 집중할 수 있다는 장점

#### 단점

- 개발자가 메모리가 언제 해제되는지 정확하게 알 수 없다.
- 가비지 컬렉션(GC)이 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생한다.

#### Mark and Sweep 알고리즘

- Mark 과정 : 먼저 Root로부터 그래프 순회를 통해 연결된 객체들을 찾아내어 각각 어떤 객체를 잠조하고 있는지 찾아서 마킹합니다.
- Sweep 과정 : 참조하고 있지 않은 객체 즉 Unreachable 객체들을 Heap에서 제거합니다.
- Compact 과정 : Sweep 후에 분산된 객체들을 Heap의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분으로 압축합니다. (가비지 컬렉터 종류에 따라 하지 않는 경우도 있음)

#### GC 동작 과정

https://coding-factory.tistory.com/829
1. 객체가 처음 생성되고 Heap영역의 Eden에 age-bit 0으로 할당됩니다. 이 age-bit는 Minor GC에서 살아남을 때마다 1씩 증가
2. 시간이 지나 Heap Area의 Eden 영역에 객체가 다 쌓이게 되면 Minor GC가 한번 일어나게 되고 참조 정도에 따라 Servivor0 영역으로 이동하거나 회수
3. 계속해서 Eden영역에는 신규 객체들이 생성됩니다. 이렇게 또 Eden영역에 객체가 다 쌓이게 되면 Young Generation(Eden+Servivor) 영역에 있는 객체들을 비어있는 Survival인 Survival1 영역에 이동하고 살아남은 모든 객체들은 age가 1씩 증가
4. 또다시 Eden 영역에 신규 객체들로 가득 차게 되면 다시한번 minor GC가 일어나고 Young Generation(Eden+Servivor) 영역에 있는 객체들을 비어있는 Survival인 Survival0으로 이동시킨 뒤 age를 1 증가시킵니다. 이 과정을 계속 반복
5. 위 과정을 반복하다 보면 age bit가 특정 숫자 이상으로 되는 경우가 발생합니다. 이때 JVM에서 설정해놓은 age bit에 도달하게 되면 오랫동안 쓰일 객체라고 판단하고 Old generation 영역으로 이동시킵니다. 이 과정을 프로모션(Promotion)
6. 시간이 지나 Old영역에 할당된 메모리가 허용치를 넘게 되면, Old 영역에 있는 모든 객체들을 검사하여 참조되지 않는 객체들을 한꺼번에 삭제하는 GC가 실행됩니다. 이렇게 Old generation영역의 메모리를 회수하는 GC를 Major GC라고 합니다. Major GC는 시간이 오래 걸리는 작업이고 이때 GC를 실행하는 스레드를 제외한 모든 스레드는 작업을 멈추게 됩니다. 이를 'Stop-the-World'

### Heap Area

![img_1.png](/resources/images/heap-area.png)

Young Generation 영역

- 자바 객체가 생성되자마자 저장되고, 생긴지 얼마 안되는 객체가 저장되는 공간
- Heap 영역에 객체가 생성되면 최초로 Eden 영역에 할당
- 이 영역에 데이터가 어느정도 쌓이게 되면 참조정도에 따라 Servivor의 빈 공간으로 이동되거나 회수
- Young Generation(Eden+Servivor) 영역이 차게 되면 또 참조정도에 따라 Old영역으로 이동 되게 되거나 회수
- 이렇게 Young Generation과 Tenured Generation 에서의 GC를 Minor GC

- Old영역에 할당된 메모리가 허용치를 넘게 되면, Old 영역에 있는 모든 객체들을 검사하여 참조되지 않는 객체들을 한꺼번에 삭제하는 GC가 실행
- 시간이 오래 걸리는 작업이고 이 때 GC를 실행하는 쓰레드를 제외한 모든 스레드는 작업을 멈춤. 이를 'Stop-the-World'
- 그리고 이렇게 'Stop-the-World'가 발생하고 Old영역의 메모리를 회수하는 GC를 Major GC 
