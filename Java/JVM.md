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

1. [Serial GC](Serial-GC)
2. [Parallel GC](Parallel-GC)
3. [CMS GC](CMS-GC)
4. [G1 GC](G1-GC)

#### Serial GC

#### Parallel GC

#### CMS GC

#### G1 GC

