# Vector 클래스

동적으로 크기가 관리되는 객체 배열.

일반적인 배열의 경우 선언 및 초기화 할 때, 배열의 크기(length)가 정해져서 그 이상으로 원소를 추가할 수 없음

그러나 내부적으로 `Object` 타입의 배열을 가지고 있는 `Vector` 클래스에는 원하는 만큼 객체 원소를 추가하거나 제거할 수 있음.

&nbsp;

## 생성자

|   생성자   |                                                  설명                                                  |
| :--------: | :----------------------------------------------------------------------------------------------------: |
| `Vector()` | 10개의 객체를 저장할 수 있는 `Vector` 인스턴스 생성. 원소가 10개 이상이 되면 자동적으로 크기가 증가됨. |

&nbsp;

## 메서드

|  메서드   |  매개 변수   | 반환 타입 | 설명                                                                  |
| :-------: | :----------: | :-------: | :-------------------------------------------------------------------- |
|   `add`   | `Object` obj | `boolean` | `Vector`에 객체 추가. 추가에 성공하면 `true`, 실패하면 `false` 반환   |
| `remove`  | `Object` obj | `boolean` | `Vector`에서 객체 제거. 제거에 성공하면 `true`, 실패하면 `false` 반환 |
| `isEmpty` |              | `boolean` | `Vector`가 비어있는지 검사                                            |
|   `get`   | `int` index  | `Object`  | 지정된 index의 객체 반환. 반환 타입인 `Object`에 맞게 형변환 필요     |
|  `size`   |              |   `int`   | `Vector`에 저장된 객체의 개수 반환                                    |

&nbsp;

## 예제

```java
import java.util.*; // Vector 클래스를 사용하기 위함. 편의상 모든 util 클래스를 불러왔음

class VectorTest {
    public static void main(String[] args) {
        Buyer b = new Buyer();
        b.buy(new Tv());
        b.buy(new Computer());
        b.buy(new Audio());
    }
}

class Product {
    int price;
    int bonusPoint;

    Product (int price) {
        this.price = price;
        this.bonusPoint = (int)(price / 10.0);
    }
}

class Tv extends Product {
    Tv() {
        super(100);
    }
}

class Computer extends Product {
    Computer() {
        super(200);
    }
}

class Audio extends Product {
    Audio() {
        super(300);
    }
}

class Buyer {
    int money = 1000;
    int bonusPoint = 0;
    Vector item = new Vector(); // 구매한 물건(객체)를 담을 객체 배열 Vector

    Buyer() {
        System.out.printf("money: %d%nbonusPoint: %d%n", money, bonusPoint);
    }

    void buy(Product p) {
        money -= p.price;
        bonusPoint += p.bonusPoint;
        item.add(p); // Vector의 add 메서드로 구매한 물건을 객체 배열에 추가
        System.out.printf("money: %d%nbonusPoint: %d%n", money, bonusPoint);
    }
}
```
