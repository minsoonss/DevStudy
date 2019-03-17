# Docker

## docker?
* 컨테이너 기반의 오픈소스 가상화 플랫폼
* 다양한 프로그램, 실행환경을 컨테이너로 추상화
    * 배포 및 관리를 용이하게 함
* 여러 환경에서 실행 가능
* 기존의 VM(Virtual Machine)은 호스트 OS위에 게스트 OS 전체를 가상화
    * 무겁다 매우!
* 컨테이너와 이미지의 개념이 제일 중요

### container?
* 격리된 공간에서 프로세스가 동작하는 기술

### image?
* 컨테이너 실행에 필요한 파일과 설정값 등을 포함한 것
* 컨테이너에 포함되어 실행
* 레이어 방식으로 저장
* Dockerfile을 이용해서 docker image 생성
* 생성된 이미지는 DockerHub혹은 개인 DockerRepository에 저장 관리

##Dockerfile
##### docker image 설정 파일
* FROM
    * 어떤 이미지를 기반으로 새로운 이미지를 만들지 명시
* MAINTAINER
    * 메인테이너 정보
* RUN
    * bash shell 명령어 입력
* VOLUME
    * 호스트와 공유할 디렉토리 목록
* ADD
    * 호스트 파일시스템에서 파일 가져오기
* CMD
    * docker container가 실행되었을 때 실행할 명령어
* WORKDIR
    * CMD에서 설정한 실행파일이 실행될 디렉토리
* EXPOSE
    * 호스트와 연결할 포트
    

<hr/>
[참고](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)
     (http://blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=220646382977&parentCategoryNo=7&categoryNo=&viewDate=&isShowPopularPosts=true&from=search) 