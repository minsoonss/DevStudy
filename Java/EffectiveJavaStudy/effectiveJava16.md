# public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

```java
class Point {
    public double x;
    public double y;
}
```

위와 같은 클래스는 최악 -> 데이터 필드에 직접 접근 가능하니 캡슐화의 이점을 제공하지 못한다.

```java
class Point {
    private double x;
    private double y;
    
    public Point(double x, double y) {
        this.x = x;
        thix.y = y;
    }
    
    public double getX() {return x;}
    public double getY() {return y;}
    
    public void setX(double x) {this.x = x;}
    public void setY(double y) {this.y = y;}
}
```

public 클래스에서라면 위의 방식이 확실히 맞다. **패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공**



package-private 클래스나 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제 없음