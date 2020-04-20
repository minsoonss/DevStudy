# JVM

## GC

garbage collection 의 약자

#### JVM의 메모리 영역에 대한 이해

Heap 메모리는 크게 young/old로 구분

Young Generation - 생긴지 얼마 안된 객체를 저장

- **Minor GC**
  - Young 영역(Eden/Survivor 로 분류)에서 일어난 GC
  - Eden - 객체가 생성되자마자 저장되는 곳
  - Survivor - Minor GC가 발생할 때 객체가 이동되는 곳

Old generation - 생긴지 오래된 객체를 저장

- **Full GC**
  - Old 영역의 GC
  - Full GC에 사용되는 알고리즘 - Mark & Compact(deletion & compaction과 동일??)
  - Mark & Compact - 전체 객체들의 reference를 쭉 따라가면서 reference가 연결되지 않는 객체를 Mark 함. 이 작업이 끝나면 사용되지 않는 객체를 모두 Mark가 되고 이 Mark된 객체를 삭제
  - 속도가 매우 느림, Stop the world 가 일어남
  - Full GC의 속도로 Application의 안정성에 많은 연관이 있음

#### GC 종류

1. [Serial GC](#Serial-GC)
2. [Parallel GC](#Parallel-GC)
3. [CMS GC](#CMS-GC)
4. [G1 GC](#G1-GC)

#### Serial GC

Full GC에 Mark&compact 알고리즘을 사용하는 방법

#### Parallel GC

Serial GC는 하나의 Thread에서 이루어짐

Parallel GC에서는 Multi Thread로 가능하기때문에 속도가 훨씬 빨라짐

#### CMS GC

Application 전체가 멈춰지는 시간을 줄이기 위해

일부는 Application이 돌아가는 동안 수행하고, 최소한의 작업만 Application이 멈추지 않았을때 수행

Mark&Compact 작업을 안하는게 단점인듯하다

JDK 9 부터 지원되지 않는다.

#### G1 GC

**힙 메모리를 N개의 영역으로 나눈다는 점**이 CMS와의 가장 큰 차이점 - 즉 큰 영역을 한 번에 썼다 지웠다 할것이냐? 일부 영역을 여러번 썼다 지웠다 할것이냐?

Mark & Compact 를 수행함으로 연속된 메모리 할당을 하는데 효율적



### 결론) Full GC시간을 줄이기 위해서는 어떤 일을 해야하는가??

최대한 multi thread로 작동

어플리케이션이 작동되는 도중에 하나의 쓰레드를 사용하여서 일부 GC를 진행

메모리를 분할하여 비교적 삭제 시간을 줄임