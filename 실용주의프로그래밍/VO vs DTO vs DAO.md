
#### VO(Value Object)
'값 객체' 라는 뜻인데, 소프트웨어 관점에서 '값' 이 가지는 의미를 확실하게 알 필요가 있다. 
'값'은 다음과 같은 특징을 만족해야한다. 

##### <font color="#de7802">'값'(Value)의 특징</font>
1. 불변성
	값은 변하지 않는다. 예를 들어, 숫자 1은 영원히 숫자 1이다. 
	 추가설명 : 객체가 생성된 이후에 객체 안에 들어있는 값이 변경되지 않는다. 즉, 객체 내의 모든 멤버변수는 불변(final)으로 선언되어있어야한다. 

	 vo는 객체가 불변상태여야한다. (O) , <span style="background:#ff4d4f">객체 내의 모든 멤버변수가 final 로 선언되어있으면 VO다(X)</span>
	 --> 모든 멤버변수를 final 로 선언하더라도 원시타입이 아닌 참조타입인 객체가 있다면 불변성이 보장되지 않을 수 있기 때문이다. 
	 
```java
// 모든 멤버변수를 final 로 선언하더라도 원시 타입이 아닌 참조타입인 객체가 있다면 불변성이 보장안됨.

@Getter
public class FilledColor {
	public final int r;
	public final int g;
	public final int b;
	public final Shape shape; // FilledColor는 지정된 Shape에 들어가는 색상을 의미하는 클래스 


	public FilledColor(int r, int g, int b, Shape shape) {
		this.r = r;
		this.g = g;
		this.b = b;
		this.shape = shape;
	}
}

@Data
class Rectangle extends Shape {
	private int width; // Shape 에 들어갈수 있는 Rectangle 클래스의 멤버변수가 불변이 아님. 
	private int height; 

	public Rectangle(int width, int height) {
		this.width  = width;
		this.height = height;
	}
	
}

// 만약 객체 생성시
Rectangle rectangle     = new Rectangle(10, 20);
FilledColor filledColor = new FilledColor(1, 0, 0, rectangle);

// filledColor 의 내부 값이 변경돼 불변성이 깨졌음. 
filledColor.getRectangle().setWidth(20);


```

	 결론 : 불변객체 안의 참조 객체가 불변이 아니라면 그 객체는 불변이 아니다. 

	불변성은 함수에도 적용될수 있다. 입력값이 같을 때 항상 같은 값을 반환하는 함수를 가리켜 순수함수라고      부른다. VO안의 모든 함수는 순수 함수여야한다.(메소드가 랜덤값을 발생시켜 필드에 넣는 등의 행위를 하      면 안됨. )

	 멤버변수와 모든 메서드가 불변이면 불변성이 지켜질까? NO
	 상속을 통해서도 불변성이 깨질 수 있다. 
	 
```java
// 상속으로 설계 의도인 불변성이 깨지는 예 
public class Color {
	public final int r;
	public final int g;
	public final int b;

	public Color(int r, int g, int b) {
		this.r = r;
		this.g = g;
		this.b = b;
	}
}

class AlphaColor extends Color {
	public int a; // 상속된 클래스의 멤버변수가 불변이 아닐 수 있다. 

	public AlphaColor(int r, int g, int b, int a) {
		super(r, g, b);
		this.a = a;
	}
}


```
		그럼 vo 하나 만들 때 필드와 메소드를 final 하게 만들어야하고 클래스도 final 하게 만들어야만 하는건가?  -->  No, 불변성을 지키려고 하는 방향성만 인지하고 구현하자 , 다른 개발자들이 객체를 신뢰할수 있도록하는 것이 목표이다. 신뢰성있는 시스템과 개발자가 이해하기 쉽도록 구성한다고 생각하면됨. 


2. 동등성
	 값의 가치는 항상 같다. 예를 들어, 모든 숫자 1은 적혀있는 위치나 시간과 관계없이 항상
	 같은 숫자 1이다. 
	 VO를 만들기 위해 객체 간 비교에 사용되는 equals나 hashCode를 오버라이딩 할 필요가 있음. 오버라이딩 안할경우 equals와 hashCode는 객체의 참조값, 즉 메모리상의 주솟값을 이용해 비교하기 때문이다. 그러나 vo 만들때마다 이 작업이 귀찮다면 롬복의 @Value 어노테이션을 사용하는걸 추천한다. 당연한 이야기이지만 vo에는 식별자 역할을 하는 필드가 들어가서는 안된다. 동등성을 해치기 때문이다. 
	 롬복의 @Value 는 다음과 같은 기능을 수행한다.
	 1. equals 와 hashCode 메서드가 객체의 상태에 따라 비교하는 메소드로 자동생성된다. 
	 2. 멤버변수와 클래스가 final로 선언된다. 
	 3. 명시적으로 다른 개발자에게 '나 vo 다'라고 알릴 수 있다 .

3. 자가검증
	 값은 그 자체로 올바르다. 예를 들어, 숫자 1은 그 자체로 올바른 숫자이다. 이는 '1은 사실 1.01이지 않을까? ' 와 같은 고민을 할 필요가 없다는 의미이다. 
	 한번 생성된 VO의 멤버변수에는 이상한 값이 들어있을수 없다. 

