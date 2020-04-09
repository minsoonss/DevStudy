# Design Pattern

소프트웨어 공학에서 소프트웨어 디자인에서 특정 문맥에서 공통적으로 발생하는 문제에 대해 재사용 가능한 해결책

디자인 패턴은 크게 세부분으로 그룹화 되어있음

[1.생성 패턴](#1-생성-패턴)

[2.구조 패턴](#2-구조-패턴)

[3.행동 패턴](#3-행동-패턴)

### 1. 생성 패턴

1. Factory Method

   구체적인 처리를 서브클래스로 미룸

   객체 생성을 위한 인터페이스를 정의

   어떤 클래스의 인스턴스를 생성할지를 서브클래스가 정함

2. Abstract Factory

   구체적인 클래스를 지정하지 않고 관련성을 갖는 객체들의 집합을 생성

   서로 독립적인 객체들의 집합을 생성할 수 있는 인터페이스 제공

3. Builder

   복합 객체 생성

   복합 객체의 생성과 표현 방법을 분리하여 동일한 생성 절차에서 서로 다른 결과를 만들 수 있게 함

4. Singleton

   단 하나의 인스턴스

   오직 하나의 클래스 인스턴스만 만들어지도록 하고, 이에 대한 전역적인 접근을 제공(Static method)

5. Prototype

   복사 패턴

   인스턴스를 복사하여 새로운 객체 생성

   프로토타입 인스턴스를 이용해 생성할 객체의 종류를 명세하고 이 프로토타입을 복사해 새로운 객체 생성

### 2. 구조 패턴

1. 어댑터

   Wrapper 패턴. 한 겹 싸서 재사용(effectiveJava18 참고)

   서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작시킴

   기존 클래스가 제공하는 인터페이스를 다른 클래스가 필요로 하는 인터페이스로 변환할 때 사용

   어댑터를 만드는 방법

   - 상속 - 클래스 패턴
   - 위임 - 객체 패턴

2. 브리지

   추상화(또는 인터페스)와 구현을 분리하여 각 각을 독립적으로 변형 가능

   구현 자체도 하나의 추상화 개념으로 다양한 변형이 가능

   > [https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%A6%AC%EC%A7%80_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/브리지_패턴)

   메소드의 구현을 다른 클래스에서 가져온다?

3. 컴포지트

   객체들의 관계를 트리 구조로 구성 부분-전체 계층으로 표현하는 패턴

   사용자가 단일 객체와 복합 객체 모두 동일하게 다루도록 함

   ```java
   import java.util.List;
   import java.util.ArrayList;
   
   /** "Component" */
   interface Graphic {
   
       //Prints the graphic.
       public void print();
   
   }
   
   /** "Composite" */
   class CompositeGraphic implements Graphic {
   
       //Collection of child graphics.
       private List<Graphic> mChildGraphics = new ArrayList<Graphic>();
   
       //Prints the graphic.
       public void print() {
           for (Graphic graphic : mChildGraphics) {
               graphic.print();
           }
       }
   
       //Adds the graphic to the composition.
       public void add(Graphic graphic) {
           mChildGraphics.add(graphic);
       }
   
       //Removes the graphic from the composition.
       public void remove(Graphic graphic) {
           mChildGraphics.remove(graphic);
       }
   
   }
   
   
   /** "Leaf" */
   class Ellipse implements Graphic {
   
       //Prints the graphic.
       public void print() {
           System.out.println("Ellipse");
       }
   
   }
   
   
   /** Client */
   public class Program {
   
       public static void main(String[] args) {
           //Initialize four ellipses
           Ellipse ellipse1 = new Ellipse();
           Ellipse ellipse2 = new Ellipse();
           Ellipse ellipse3 = new Ellipse();
           Ellipse ellipse4 = new Ellipse();
   
           //Initialize three composite graphics
           CompositeGraphic graphic = new CompositeGraphic();
           CompositeGraphic graphic1 = new CompositeGraphic();
           CompositeGraphic graphic2 = new CompositeGraphic();
   
           //Composes the graphics
           graphic1.add(ellipse1);
           graphic1.add(ellipse2);
           graphic1.add(ellipse3);
   
           graphic2.add(ellipse4);
   
           graphic.add(graphic1);
           graphic.add(graphic2);
   
           //Prints the complete graphic (four times the string "Ellipse").
           graphic.print();
       }
   }
   ```

   

4. 데코레이터(? 한번 더 보기)

   객체에 동적으로 기능을 추가, 삭제

   기능의 유연한 확장을 위해 상속 대신 사용

   > [https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/데코레이터_패턴)

5. 퍼사드

   건물의 외관, 정면을 뜻함

   간략한 인터페이스를 제공

   ex) slf4j 는 로깅 퍼사드 인터페이스

6. 플라이웨이트

   동일하거나 유사한 객체들 사이에 가능한 많은 데이터를 서로 공유하여 사용하도록 하여 메모리 사용량을 최소화하는 소프트웨어 디자인 패턴

   종종 오브젝트의 일부 상태 정보는 공유될 수 있는데, 플라이웨이트 패턴에서는 이와 같은 상태 정보를 외부 자료 구조에 저장하여 플라이웨이트 오브젝트가 잠깐 동안 사용할 수 있도록 전달

7. 프록시

   다른 객체로의 접근을 통제하기 위해서 대리자를 제공. (큰 이미지 로딩, 통신 연결 시 응답 대기)

### 3. 행동 패턴

1. 책임 연쇄

   자신에게 넘겨진 파라미터에 대하여 자신이 할 수 있는 행동을 한 뒤, 다음 순서로 넘기는 패턴

   대게 연결 리스트 구조

2. 커맨드

   명령을 내리는 개체와 명령을 실행하는 개체를 나누는 패턴

   명령, 수신자, 발동자, 클라이언트 네개의 용어가 따름

   (실행, 전등, 스위치)

   클라이언트에서 전등을 켜는 명령, 끄는 명령 정의 후 스위치(발동자)에게 할당, 스위치가 콜을 하면 수신자인 전등이 켜진다.

3. 인터프리터

   한 언어에서 문들을 평가하는 방법을 규정하는 패턴(데이터를 쉽게 해석)

4. 반복자

   객체 지향 프로그래밍에서 반복자를 사용하여 컨테이너를 가로지르며 컨테이너의 요소들에 접근하는 패턴

   Iterator

5. 중재자

   여러 클라이언트들 간의 상호작용이 복잡할 때, 클라이언트들의 필요한 결합성만을 높인 패턴

   객체들간의 상호작용을 한군데로 모은다

6. 메멘토

   객체의 상태정보를 저장하고 사용자의 필요에 의해 원하는 시점의 데이터를 복원할 수 있는 패턴(undo?)

7. 옵저버

   객체의 상태를 관찰하며, 변동사항을 다른 객체에 알려줌

   JAVA에서 기본으로 Observable 클래스와 Observer 인터페이스를 제공

8. 상태

   코드내에 조건문을 대체하려는 목적으로 상태를 객체화하여 상태가 행동할 수 있도록 하는 패턴

9. 전략

   런타임동안 알맞는 객체의 행위를 다이나믹하게 선택해서 사용하는 패턴

   > [https://velog.io/@kimjungmin0426/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4](https://velog.io/@kimjungmin0426/디자인패턴-전략패턴)

10. 템플릿 메소드

    알고리즘의 뼈대를 맞추는 패턴 - 레이아웃을 통일 but 상속을 통하여 유연성을 준다

    ex) A 행위 후 B 행위를 해라 A,B의 행위가 객체마다 다를 수 있다.

11. 비지터

    알고리즘을 객체 구조에서 분리시키는 패턴



> 참고 https://dreamlog.tistory.com/577
