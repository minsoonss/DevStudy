# try-finally 보다는 try-with-resources를 사용하라

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다.

ex) `InputStream, OutputStream, java.sql.Connection` 등

자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어지기도 한다. 이런 자원 중 상당수가 안전망으로 finalizer를 활용하지만 `item8` 에서 얘기한 것 처럼 피하는것이 좋다.

```java
/**
 * 예1)
 */
static String firstLineOfFile(String path) throws IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    return br.readLine();
  } finally {
    br.close();
  }
}
```

```java
/**
 * 예2)
 */
static void copy(String src, String dst) throws IOException {
  InputStream in = new FileInputStream(src);
  try {
    OutputStream out = new FileOutputStream(dst);
    try {
      byte[] buf = new byte[BUFFER_SIZE];
      int n;
      while((n = in.read(buf)) >= 0)
        out.write(buf, 0, n);
    } finally {
      out.close();
    }
  } finally {
    in.close();
  }
}
```

예1 에서는 예외가 try 블록과 finally 블록 모두에서 발생할 수 있다  try의 readLine메소드에서 발생하고 finally의 close메소드에서도 발생할 수 있다. 그렇게 되면 close메소드에서 발생한 예외가 readLine에서 발생한 예외를 먹어버려 readLine의 예외는 어디에서도 찾을 수 없다.

해당 문제를 자바 7에서 생긴 `try-with-resources`로 해결할 수 있다. `try-with-resources`로 해결하기 위해서는 **AutoClosable** 인터페이스를 구현해야 한다. 

```java
/**
 * try-with-resources를 사용해 재작성한 예1
 */
static String firstLineOfFile(String path) throws IOException {
  try(BufferedReader br = new BufferedReader(
	  new FileReader(path))) {
    return br.readLine();
  }
}
```

```java
/**
 * try-with-resources를 사용해 재작성한 예2
 */
static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src);
      	OutputStream out = new FileOutputStream(dst)) {
    byte[] buf = new byte[BUFFER_SIZE];
    int n;
    while((n - in.read(buf)) >= 0)
      out.write(buf, o, n);
  }
}
```

재작성한 예1 에서는 readLine메소드와 close메소드에서 예외가 발생하게되면 close메소드에서 발생한 예외는 숨겨지고 readLine메소드에서 발생한 예외가 기록된다. 하지만 close의 예외도 버려지는것이 아니라 숨겨졌다는 꼬리표를 달고 출력된다.

또한 `try-with-resources` 에서도 **`catch`** 절을 쓸 수 있다.

```java
/**
 * try-with-resources에 catch까지 사용해 재작성한 예1
 */
static String firstLineOfFile(String path, String defaultVal) {
  try(BufferedReader br = new BufferedReader(
  			new FileReader(path))) {
    return br.readLine();
  } catch (IOException e) {
    return defualtVal;
  }
}
```



### 결론

try-finally에서 보여주는 결함이 많다.

try-with-resources를 사용할 경우 결함도 없애며 코드도 더욱 간결해짐을 볼 수 있다.