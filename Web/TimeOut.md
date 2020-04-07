# Timeout

### Connection Timeout

클라이언트가 서버측으로 connection을 맺길 원하지만 서버의 장애 상황으로 connection을 맺지 못하고 시간이 지나게 되면 발생함

### Read Timeout

클라이언트와 서버가 connection은 맺어졌지만 I/O 작업이 길어지거나 락이 걸려 요청이 처리되지 못하고 있을 때 클라이언트는 더 이상 기다리지 못하고 커넥션을 끊음. Java에서는 `SocketTimeoutException` 이 떨어짐

> JDBC Driver SocketTimeout은 OS의 SocketTimeout 설정에 영향을 받음
>
> JDBC Driver SocketTimeout을 설정하지 않아도 네트워크 장애 발생 이후 30분이 지나면 JDBC Connection Hang이 복구되는 것은 OS의 SocketTimeout 설정 때문

### Statement Timeout

네트워크 연결 장애에 대한 timeout을 담당하는 것이 아님

Statemet 하나가 얼마나 오래 수행되어도 괜찮은지에 대한 한계값

JDBC API인 Statement에 타임아웃 값을 설정하며, 이 값을 바탕으로 JDBC 드라이버가 StatementTimeout을 처리

JDBC API인 `java.sql.Statement.setQueryTimeout(int timeout)` 메서드로 설정

> 네트워크 장애에 대비하는 타임아웃은 JDBC Driver SocketTimeout이 처리

### Transaction Timeout

프레임워크나 애플리케이션 레벨이서 유효한 타임아웃

`StatementTimeout * N(statement 수행 수) +  α(가비지 컬렉션 및 기타)` 전체 Statement 수행 시간을 허용할 수 있는 최대 시간 이내로 제한하려 할 때 TransactionTimeout을 사용

> Statement 한 개를 수행할 때 0.1초가 필요하다면, 몇 개 안 되는 Statement를 수행할 때에는 문제가 없음
>
> 그러나 Statement 10만 개를 수행할 때에는 일만 초(약 7시간)가 필요.
>
> `TransactionTimeout`은 이런 경우에 사용 가능

### JDBC Driver's SocketTimeout

JDBC Driver Type4는 소켓을 사용하여 DBMS에 연결하는 방식. 애플리케이션과 DBMS사이의 ConnectionTimeout 처리는 DBMS에서 하지 않음

JDBC 드라이버의 SocketTimeout 값은 DBMS가 비정상적으로 종료 되었거나 네트워크 장애(기기 장애 등)가 발생했을 때 필요한 값. TCP/IP의 구조상 소켓에는 네트워크의 장애를 감지할 수 있는 방법이 없음. 그렇기 때문에 애플리케이션은 DBMS와의 연결 끊김을 알 수 없음. 이럴때 SocketTimeout이 설정돼 있지 않다면 애플리케이션은 DBMS로부터의 결과를 무한정 기다릴 수도 있음(이러한 Connection을 Dead Connection 이라고 부르기도 함) 이러한 상태를 방지하기 위해 소켓에 타임아웃을 설정해야 함. SocketTimeout은 JDBC 드라이버에서 설정할 수 있음. SocketTimeout을 설정하면 네트워크 장애 발생시 무한 대기 상황을 방지하여 장애시간 단축 가능

> 단, SocketTimeout 값을 Statement의 수행 시간 제한을 위해 사용하는 것은 바람직하지 않음
>
> 그러므로 SocketTimeout 값은 StatementTimeout 값보다는 크게 설정해야 함
>
> SocketTimeout값이 StatementTimeout보다 작으면, SocketTimeout이 먼저 동작하므로 StatementTimeout 값은 의미가 없게 되어 동작하지 않음

### OS level socket timeout 설정

SocketTimeout이나 ConnectTimeout을 설정하지 않으면 네트워크 장애가 발생해도 애플리케이션이 대부분 이를 감지할 수 없음

 따라서 연결이 되거나 데이터를 읽을 수 있을 때까지 애플리케이션이 무한정 기다리게 됨

그러나 서비스에서 발생한 실재 장애 상황에서는 30분 후에 애플리케이션(WAS)이 재연결을 시도하여 문제가 해결되는 경우가 많음

OS에서도 SocketTimeout 시간을 설정할 수 있음

해당 설정 값을 통해 OS 레벨에서 네트워크 연결 끊김을 확인

만약 리눅스 서버의 KeepAlive 체크 수행 주기가 30분이면 SocketTimeout 설정을 0으로 해도 네트워크 장애로 인한 DBMS 연결 장애 지속 시간이 30분을 넘지 않음

 Linux 서버에서 KeepAlive 체크 수행 주기는 tcp_keepalive_time로 조정할 수 있다.



> 참고 [https://effectivesquid.tistory.com/entry/Timeout%EC%97%90-%EA%B4%80%ED%95%9C-%EC%A0%95%EB%A6%AC](https://effectivesquid.tistory.com/entry/Timeout에-관한-정리)