# Kafka

**메세지큐의 일종, 분산형 스트리밍 플랫폼**

RabbitMQ, ActiveMQ 와 같은 기존 메세지 큐에 비해 높은 성능을 보임

### Pub-Sub 모델

- 메세지를 수신자에게 보내는 것이 아님 

- Producer가 topic을 정하여 그곳에 보내며 Consumer는 해당 topic을 구독하여 읽고싶을때 읽는다 (다른 MQ와의 차이점 - 큐에서 메세지를 푸쉬하는 방식과 다름)

### Topic

Topic은 여러개의 partition으로 구성될 수 있으며, 하나의 partition은 여러개의 Log로 구성된다.

- Log - 메세지가 들어가는 한 칸

- Partition - Log가 여러개가 모여 한 줄을 이룸(offset 으로 상대적 위치 파악) 

  (**주의 - 한 번 늘린 파티션의 수는 줄일 수 없음)**

- Topic - 1개 이상의 partition 이 모여서 Topic을 이룸

### Producer

메세지 발신자

- **ACK 옵션**

  메세지를 잘 받았다는 알림

  네트워크 사용량의 차이가 될 수 있음

  - Ack = -1(all) - 메세지가 Leader에게 쓰이고 Follower들에게 다 복사돼야함

  - Ack = 1 - 메세지가 Leader에게 쓰였는지만 확인, Follower는 상관 없음

  - Ack = 0 - 응답을 기다리지 않음(메세지 유실률이 높으나, 처리량 또한 높음

### Consumer

메세지 구독자

Consumer group 존재 - 여러개의 파티션이 존재하면 그 여러개의 파티션을 읽어오는 Consumer도 그룹화 될 수 있음

Partition : Consumer = n : 1 - 하나의 컨슈머가 여러개의 파티션을 읽을 수 있음

Ex) 

- partition : consumer = 1 : 1 - 동일한 개수의 파티션과 컨슈머가 1:1매칭
- partition : consumer = 2 : 1 - 하나의 컨슈머가 2개의 파티션과 매칭
- partition : consumer = 1 : 2 - 하나의 컨슈머가 하나의 파티션과 매칭(나머지 컨슈머는 놀게됨)

위의 예를 생각해보면 병렬처리가 가능하다는 얘기가 되므로 속도면에서 우위를 가져갈 수 있음

하지만 무조건적인 장점은 아님 - 메세지의 순서가 보장되어야하는 경우 병렬처리 시 문제 발생

Consumer group의 또 다른 장점 및 구성이유는 하나의 consumer가 문제가 생겨 topic의 partition을 읽어올 수 없게 될 경우 다른 consumer가 해당 partition에 매칭되어 읽어올 수 있음

### Broker

카프카 서버를 지칭

동일한 노드 내에 여러개의 broker 서버를 띄울 수 있음

### Zookeeper

분산 메세지 큐의 정보를 관리해주는 역할

카프카를 띄우기위해서 반드시 실행되어야 함

### Replication

Replication을 Topic 별로 다르게 설정 가능

Leader 와 Follower 로 분류

모든 메세지의 read/write 는 Leader에서 이루어지며 Follower는 그것에대한 sync를 맞춘다

Leader에 문제가 생기면 Follower중 하나가 Leader를 맡게된다.