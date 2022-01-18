# package와 import

## 패키지(package)

클래스의 묶음.

> 관련된 클래스끼리 묶어 효율적인 관리가 가능해짐

모든 클래스는 반드시 하나의 패키지에 속해야 함.

서로 다른 패키지에 있다면 클래스의 이름이 같아도 충돌하지 않음.

&nbsp;

클래스의 정식 이름은 앞에 패키지명을 포함하는데, 편의상 그 부분을 떼고 부르고 있음. (구별이 필요해지면 붙여서 비교해보면 됨)

> ex) `String` 클래스 &#8594; `java.lang.String` (`java.lang` 패키지에 포함되어 있음)

&nbsp;

패키지는 물리적 디렉토리로, 하위 패키지(하위 디렉토리)를 가질 수 있음.

> ex) `java` 디렉토리 > `lang` 디렉토리 > `String` 클래스

&nbsp;

## 패키지의 선언

클래스나 인터페이스 소스파일(.java)의 최상단에 선언

```java
package 패키지명;
```

> 이름은 소문자로 시작하는 것을 원칙으로 함.

이로써 소스파일에 포함된 모든 클래스와 인터페이스는 선언된 패키지에 포함됨.

&nbsp;

지금까지 패키지 선언 없이도 문제가 없었던 이유는 자동적으로 unnamed package에 속하는 것으로 취급하기 때문

큰 프로젝트나 라이브러리 작성 시에는 패키지를 반드시 구성하여 적용시켜야 함.

&nbsp;

## import문

`import`문은 사용하고자 하는 패키지의 이름을 명시해주는 용도.

클래스 이름 앞에 붙는 패키지를 일일이 작성하는 수고로움을 덜어줌.

&nbsp;

## import문의 선언

`package`문과 `class` 선언문 사이에 위치해야 함.

`package`문과 달리 `import`문은 여러번 선언 가능

```java
import 패키지명.클래스명;
// 또는
import 패키지명.*;
```

`*`을 붙이게 되면, 특정 클래스가 아니라 해당 패키지에 속한 모든 클래스를 불러온다는 의미임.

> 하나만 콕 집는거보다 성능이 떨어지지 않나? NO

단, `*`은 하위 패키지를 불러온다는 의미는 아니기 때문에

```java
import java.util.*;
import java.text.*;
```

이 2개를 합친답시고 아래처럼 쓰면 안됨.

```java
import java.*;
```

&nbsp;

간단한 예제로 package를 import 해보자

```java
import java.text.SimpleDateFormat;
import java.util.Date;

class ImportTest {
        public static void main(String[] args) {
                Date today = new Date();

                SimpleDateFormat date = new SimpleDateFormat("YYYY/MM/DD");
                SimpleDateFormat time = new SimpleDateFormat("HH:MM:SS");

                System.out.println(today);
                System.out.println(date.format(today));
                System.out.println(time.format(today));
        }
}
```

`java.text` 패키지의 `SimpleDateFormat` 클래스와 `java.util` 패키지의 `Date` 클래스를 `import`하고

현재 날짜와 시간을 원하는 형식으로 formatting 하여 출력하는 작업을 수행했음.

&nbsp;

## static import문

`static` 멤버를 호출할 때 클래스 이름을 생략할 수 있게 해줌. 특정 클래스의 `static` 멤버를 자주 사용할 때

```java
import static 패키지경로.클래스명.메서드명;
```

와 같이 `static` 키워드를 넣어 `import` 해오면 됨.

&nbsp;

예시)

```java
import static java.lang.Math.random;
import static java.lang.System.out;
```

이렇게 `import` 했다면 이제 `Math.random()` 메서드와 `System.out()` 메서드를 사용할 때 다음과 같이 쓸 수 있음.

```java
out.println(random());
```
