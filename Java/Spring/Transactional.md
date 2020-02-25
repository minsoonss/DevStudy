# @Transactional

#### Transactional

- Transaction 처리 시 필요
- @Transactional 어노테이션으로 처리
- @Scheduled와 같이 사용 시 적용 안됨
- @Transactional 이 없는 메소드에서 @Transactional 메소드를 호출 시 트랜잭션이 적용되지 않음
- rollback이 적용되지않는 문제??????? 어떻게 해결해야하나
    - rollback은 unchecked Exception에서만 작동
- Option
    1. isolation - 트랜잭션에서 일관성이 없는 데이터를 허용하도록 하는 수준
        1. READ_UNCOMMITTED
            - 아직 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용(Dirty Read)
        2. READ_COMMITTED
            - dirty read 방지 : 트랜잭션이 커밋되어 확정된 데이터만 읽을 수 있음
        3. REPEATABLE_READ
            - 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 shared lock이 걸리므로 해당영역에 대한 수정 불가
        4. SERIALIZABLE
            - 완벽한 읽기 일관성 모드를 제공
            - 데이터의 일관성 및 동시성을 위해 MVCC(Multi Version Concurrency Control)을 사용하지 않음
                - MVCC - 다중 사용자 데이터베이스 성능을 위한 기술(SELECT시  LOCK을 걸지 않음)
    2. propagation
        - REQUIRED : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션 생성
        - REQUIRED_NEW : 부모 트랜잭션을 무시하고 무조건 새로운 트랜잭션 생성
        - SUPPORT : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 트랜잭션이 아님
        - MANDATORY : 부모 트랜잭션 내에서 실행되며 부모 트랜잭션이 없을 경우 예외 발생
        - NOT_SUPPORT : 트랜잭션으로 실행되지 않으며 부모 트랜잭션 내에서 실행될 경우 일시정지
        - NEVER : 트랜잭션으로 실행되지 않으며 부모 트랜잭션이 존재하면 예외 발생
        - NESTED : 해당 메서드가 부모 트랜잭션에서 진행될 경우 별개로 커밋되거나 롤백될 수 있음