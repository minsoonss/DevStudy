# 생성자 매개변수가 많은 경우에 빌더 사용을 고려해 볼 것
static 팩토리 메소드와 public 생성자 모두 매개변수가 많이 필요한 경우에 불편해진다. 책에서는 `NutritionFacts`라는 클래스로 에를 든다.

## 해결책 1 : 생성자
설정하고 싶은 매개변수를 최소한으로 사용하는 생성자를 사용해서 인스턴스를 만들 수 있다.
```java
NutritionFacts cocaCola =
new NutritionFacts(240, 8, 100, 0, 35, 27);
```
위 코드와 같은 생성자를 쓰다보면 필요없는 매개변수를 넘겨야하는 경우가 발생 -> 보통 기본값을 넘김
-> **작성하기도 읽기도 어려운 코드 생성**

## 해결책 2 : 자바빈
디폴트 생성자를 이용하여 인스턴스 생성 -> 세터를 사용해 필요한 필드만 설정
```java
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```
* 세터의 단점은 최종적으로 인스턴스를 만들기까지 여러번의 호출을 거쳐야함 -> **자바빈이 중간에 사용되는 경우 안정적이지 않은 상태로 사용될 여지가 있음**
* 게터와 세터의 존재로 불변 클래스로 만들지 못한다 -> 쓰레드 세이프 하지 않다 -> 객체를 locking 해야하는데 어떻게 하지? Freezing???

## 해결책 3 : 빌더
**생성자의 안정성과 자바빈을 사용할 때 얻을 수 있었던 가독성을 모두 취할 수 있는 대안**

1. 빌더 패턴은 만들려는 객체를 바로 만들지 않음 
2. 클라이언트는 빌더에 필수적인 매개변수를 주면서 호출해 `Builder`객체를 얻음
3. 빌더 객체가 제공하는 세터와 비슷한 메소드를 사용 부가적인 필드를 채워넣음
4. 최종적으로 `build`라는 메소드를 호출해서 만들려는 객체를 생성
```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
.calories(100).sodium(35).carbohydrate(27).build();
```
```java
public abstract class Pizza {

    public enum Topping {
        HAM, MUSHROOM, ONION, PEEPER, SAUSAGE
    }

    final Set<Topping> toppings;

    abstract static class Builder<T extends  Builder<T>> { 
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build(); 
        protected abstract T self(); 
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }

}
```
```java
public class NyPizza extends Pizza {

    public enum Size {
        SMALL, MEDIUM, LARGE
    }

    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
```
```java
public class Calzone extends Pizza {

    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauseInside = false;

        public Builder sauceInde() {
            sauseInside = true;
            return this;
        }

        @Override //Covariant 타입 캐스팅
        public Calzone build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauseInside;
    }

}
```
### 장점
1. 생성자로 누릴 수 없는 사소한 이점으로, 빌더를 이용하면 가변인수(varargs) 매개변수를 여러개 사용할 수 있음 -> 생성자나 팩토리는 가변인자를 맨 마지막 매개변수에 한번밖에 못씀
2. 토핑 예제에서 처럼 여러 메소드 호출을 통해 전달받은 매개변수를 모아 하나의 필드에 담는 것도 가능함
3. 빌더 패턴은 상당히 유연함
    1. 빌더 하나로 여러 객체를 순회하면서 만들 수 있음
    2. 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수 있음 -> 시리얼 번호를 증가 시키는 방법
### 단점
1. 책에서는 성능이 문제가 될 수 있다 하지만 그렇지는 않을 것
2. 코드의 장황함 -> 클라이언트 코드는 간결하고 가독성이 높지만

## 참고
- https://github.com/keesun/study/blob/master/effective-java/item2.md
- https://projectlombok.org/features/Builder