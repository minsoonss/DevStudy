# Thread

Process → 실행중인 어플리케이션

하나의 프로세스가 멀티태스킹을 한다? → MultiThread

### Thread 사용방식

- 보통 Runnable Interface 를 구현하여 사용

  ```java
  Thread thread = new Thread(new Runnable() {
  	public void run() {
  		//code
  	}
  });
  
  Thread thread = new Thread(() -> {
  	//code
  });
  ```

- Thread를 상속받아서 사용도 가능

  ```java
  public class WorkerThread extends Thread {
  	@Override
  	public void run() {
  		//code
  	}
  }
  ```

### Thread Safe

멀티 스레드 프로그래밍에서 어떤 메소드 or 변수 or 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없음

Thread Safe를 지키기 위한 방법

키워드 synchronized

 스레드가 사용 중인 객체를 다른 스레드가 변경할 수 없도록 하려면 스레드 작업의 끝날 때 까지 객체에 잠금을 걸어서 다른 스레드가 사용할 수 없도록 해야함

→ 임계영역(critical section) 지정

→ 인스턴스 또는 메소드에 synchronized 키워드 

```java
public synchronized void method() {
	critical section -> 하나의 스레드만 실행 가능
}

public void method() {
	
	sychronized(공유 객체) {
		critical section;
	}
}
```

### Daemon Thread

주 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드

ex) 워드프로세서 자동저장기능, 미디어 플레이어의 동영상 or 음악 재생, 가비지 컬렉터

주 스레드가 종료되면 강제적으로 자동 종료

```java
thread.setDaemon(true);
```

### ThreadPool

병렬 작업 처리가 많아지면 스레드 개수가 증가되고 그에 따른 스레드 생성과 스케줄링으로 인해 cpu 사용량 증가로 인한 메모리 사용량 증가

→ 애플리케이션 성능 저하 발생

ExecutorService 구현 객체(ThreadPool)

1. newCachedThreadPool()
   - 초기 스레드 수 0
   - 코어 스레드 수 0
   - 최대 스레드 수 Integer.MAX_VALUE
2. newFixedThreadPool(int nThreads)
   - 초기 스레드 수 0
   - 코어 스레드 수 nThreads
   - 최대 스레드 수 nThreads