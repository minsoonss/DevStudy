# AWS well architected

1. 보안
    1. 보안 디자인 원칙
        - 강력한 신원 기반 구현 - 최소한의 권한 원칙 구현
        - 추적 로그 설정 - 모든 변경 사항과 작업 실시간 모니터링 → 자동 응답 및 조치
        - 모든 계층에 보안 적용 - OSI 레벨에 맞게
        - 보안 모법 사례 자동 적용 - aws는 best practice를 공유함
        - 전송 중 혹은 저장 시 데이터 보호
        - 사람 실수 방지!!
        - 보안 이벤트에 대비 - 사고 대응 시뮬레이션 실행, 자동화된 도구 사용하여 탐지
    2. 보안 정의
        - ID 및 액세스 관리 - MFA 사용 권장
            - root account - root account 를 사용하지 않으면 할 수 없는 일을 제외하고는 IAM유저로 사용, MFA사용
            - IAM Users, Groups and Roles
                - 사용자 별 그룹을 지정하여 특정 서비스만 사용 가능 하도록
                - IAM정책은 default가 Deny, Allow 설정을 해주더라도 Deny가 있으면 Deny가 override함
            - 결론
                1. 루트 사용 최소화
                2. MFA 활성화
                3. 패스워드 복잡하게
        - 탐지 제어
            - 탐지제어는 자동화 해라!
            - CloudTrail, Cloudwatch, AWS Config등을 활용
                - CloudTrail → 누가, 언제, 어떤, 어떤 리소스, 어디서 api call을 했는가
                - CloudWatch → 로깅을 고도화 하여 보여주고 알림을 준다
                - AWS config → AWS리소스의 변경사항을 추적하고 감사
                - AWS GuardDuty - 공부
        - 인프라 보호
            - 네트워크 보호, 시스템보안, 시스템 레벨
            - VPC
            - Security Group - instance level - stateful - 이미 연결된 것은 끊을 수 없다
            - NACL(network CLA) - subnet level - stateless - 이미 연결된 것도 끊을 수 있다.
            - VPC Flog logs - CloudWatch를 이용
            - System Manager - OS패치관리, 운영체제 구성에 도움
        - 데이터 보호
            - 암호화!!!!!!!
            - KMS(key management service)
            - Versioning을 통해 우발적인 덮어쓰기, 데이터 삭제 대비
            - 통신 간 암호화 - ELB에 SSL 적용
            - ACM
        - 사고 대응
            - 스냅샷 기준으로 복구
            - 프로세스를 잘 마련 해야함
            - 격리 된 다른 VPC에서 감염된 ec2를 복구 → 분석

2. 운영 우수성

1. 운영 우수성 디자인 원칙
    - 코드를 통한 운영
    - 주석화 된 문서
    - 변경을 작게, 자주, 원복 가능하도록
    - 운영 절차의 수시 정제 - 시뮬레이션 해보기
    - 실패 예측
    - 모든 운영실패를 통한 개선
2. 운영 우수성 정의
    1. 준비
        - 운영 업무 설계 시 비즈니스 목표 반영
        - 워크로드와 변경이 제품/서비스로 연결되도록 지원할 수 있는 운영
        - CI/CD
        - Pre-Mortem
            1. 예상되는 문제점 상상
                - 문제 영역에 대한 제한은 없다.
                - 해결책은 생각하지 않는다.
            2. 문제점 선정
                - 영향도 치명성
                - 발생 가능성
                - 통제 가능성
            3. 문제에 대한 해결책 모색
                - 선제적 대응책
                - 백업 플랜
        - 게임 데이
            - 풀스택 시뮬레이션 & Test
            - 업무세그먼트 별 small game day
        - 넷플릭스의 실패 주입!!
        - 클라우드 운영 역량
        - Snowflake Server VS Phoenix Server
    2. 운영
        - 성공적인 운영을 위한 측정지표 준비
        - 운영 이벤트 관리
            - 알려진 이벤트에 대한 Runbook, 알려지지 않은 이벤트에 대한 Playbook 준비
            - 알람, 실행프로세스 연결
            - 대응인력이 권한을 가지고 있는가
            - 대시보드와 알림을 통한 현황 공유
        - ITIL 이벤트 관리
            - 모범사례를 위한 질문
                - 필요한 이벤트 발생
                - 이벤트 수집 주기 범위 적절성
                - 이벤트 트리거, 한도 처리 규칙 리뷰
            - ITIL 인시던트 관리
            - ITIL 문제 관리
                - RCA(Root Cause Analytics)
    3. 진화 - 사내 문화와 관련!
        - 모범사례
            - 지속적이고 점진적인 개선을 위한 업무 사이클 준비
            - Lessons Learned를 다른 팀과 공유
            - 배포 활동의 결과를 활용한 개선 기회
        - 성공을 위한 제품 관리
            - 최소 제품으로 부터 시작, 변화의 증분을 작게 가져가기
            - 제품 가치 변화에 따른 업무 흐름의 가시성 확보(데이터 기반)
            - 고객으로부터 피드백을 받고 반영할 것
            - 변화를 실행할 수 있는 자율권을 부여
        - 실패에 대한 대처
            - 복잡하게 변화하는 시스템에서 실패는 불가피!
            - 휴먼 에러는 Post-Mortem의 시작점 - 비난의 대상은 아니다

3. 안정성

- 좁은 의미로 특정 기간 동안 장애가 없는 정도
- 인프라 또는 서비스 중단으로 복구할 수 있는 시스템에서 부터, 동적으로 컴퓨팅 리소스를 확보하여 요구 사항을 충족
- 가용성
    - 서버와 네트워크, 프로그램 등의 정보 시스템이 정상적으로 사용 가능한 정도

        → 정상 동작 시간 / 총 시간

    - 비용 등 비지니스 적인 기준에 의해 선택지가 달라짐 - 무조건 높다고 좋은 것은 아니다
- 아키텍처 구성
    - 2 tier
    - Auto Scaling
- 안정성 디자인 원칙
    - 복구 절차 테스트
    - 문제 발생 시 자동 복구되도록 구성
    - 시스템의 가용성을 높이기 위해 수평 확장 가능토록 구성
    - 시스템 용량 예측하지 마라 - 고정값을 주지마라
    - 변경 관리 자동화
1. 안정성 정의
    1. 기본 요건
        - Best Practices
            - On-premise 환경에서는 네트웍 및 시스템 자원의 양을 미리 충분하게 준비
            - AWS 클라우드를 사용하는 경우 각 서비스의 limit을 파악 하고, 조정
        - 제한 관리
            - AWS Trusted Advisor
            - Amazon CloudWatch
        - 네트워크 토폴로지 계획
            - VPC
            - Direct Connect
            - Gateway
    2. 변경관리
        - 수요 변화에 따른 대처
            - Auto Scaling - AMI 지정으로 EC2 Loading이 빨라진다????
            - CloudWatch
            - ELB
        - 모니터링
            - S3
            - EMR
            - CloudWatch
        - 변경 관리 수행
            - CloudFormation
            - AWS CodePipeline
            - AWS CodeDeploy
        - 구성 요소 실패에 대한 내성
            - AWS SDKs
            - ELB
            - Amazon SQS
    3. 장애 관리
        - 데이터 내구성
            - S3
            - KMS
            - EBS
            - RPO RTO 복구 테스트 수행
        - 복구 계획
            - Cloud Formation
            - RPO RTO 정의

4. 성능 효율성

- 가성비!
1. 성능 효율성 디자인 원칙
    - 최신 서비스 채택
    - 즉시 글로벌 서비스
    - 서버리스 아키텍처 사용
    - 더 자주 새로운 아이디어를 실험
2. 성능 효율성 정의
    1. 선택
        - 컴퓨팅
        - 스토리지
    2. 검토 - 벤치마킹
    3. 모니터링
        - 단계
            1. 생성
            2. 집계
            3. 실시간 처리 및 알림
            4. 저장
            5. 분석
            - CloudWatch, S3, X-Ray
        - 모범 사례
    4. 트레이드 오프
        - 읽기 부하 - CloudFront, Elastic Cache
        - 쓰기 부하 - DynamoDB
        - 대용량 - 압축
        - 대량 요청 - SQS(Queuing Service)

        WCU RCU ???? 

5. 비용 최적화

- 비용 최적화 디자인 원칙
- 비용 최적화를 위한 4가지 정의
    1. 비용 인지
        1. 이해 관계자 - 비즈니스 관련 모든 이해 관계자는 클라우드 여정의 모든 단계에서 비용 토론에 참여
        2. 가시성과 제어 - AWS 대금 및 비용 관리 서비스( Athena????가 뭐지?? 계속 나오네??? )
            - 비용 탐색기
            - 예산 ( AWS Budgets )
            - 태그 - 메타데이터 추가, 피벗
        3. 엔티티 생명주기 추적
            - AWS Config
            - AWS CloudTrail
            - Amazon CloudWatch
            - IAM
    2. 비용 효율적인 리소스
        - 적합한 인스턴스와 리소스 선택
            - 인스턴스 크기
            - 간단한 온디맨드 변경
                - 메모리가 제일 고가
            - 맞는 크기 사용 - 리소스 모니터링 및 알람 ( CloudWatch )
        - 구매 옵션
            - On-demand
            - RI(Reserve Instance)
            - SI(Spot Instance)
                - 온디맨드 요금 대비 최대 90% 절약
                - 중단 가능성 있음( 대부분이 workload 를 다 수행하고 끝남 - 걱정 말아라)
        - 지리적 선택
        - 관리형 서비스
            - email, queuing, trans-coding, search, database, monitoring, metric ...
    3. 공급과 수요의 균형
        1. 탄력성
            - Auto Scaling
            - 개발에서 사용안하는 인스턴스 끄기???
        2. 버퍼 기반 - 큐를 사용하여 매칭
    4. 지속적 최적화
        1. 측정, 모니터링, 그리고 개선
        2. 항상 그린 유지