# java.time 패키지

기존 `Date`와 `Calendar`의 단점을 커버해줄 수 있는 패키지 (`JDK` 1.8부터 도입)

여기 하위 패키지들에 속한 클래스들은 불변성을 가지기 때문에 thread-safe 하다는 장점이 있음

> 인스턴스의 값을 변경하지 않고, 항상 새로운 인스턴스를 생성하여 반환함

> 멀티 쓰레드 환경에서 여러 쓰레드가 동시에 한 객체에 접근할 때 문제가 생길 수 있는데, 값이 불변성을 띄면 충돌이 일어날 일이 없으므로 thread-safe하다고 말함

&nbsp;

## java.time 패키지의 핵심 클래스

날짜와 시간이 별도로 분리되어있어 따로 사용할 수 있고, 합쳐진 클래스로 사용할 수도 있음

- `LocalDate`: 날짜

- `LocalTime`: 시간

- `LocalDateTime`: 날짜 + 시간

- `ZonedDateTime`: 날짜 + 시간 + 시간대

  > 기존의 `Calendar`도 날짜 + 시간 + 시간대가 하나로 합쳐져 있는 클래스

- `Instant`: 날짜 + 시간을 초단위로 나타냄 (타임스탬프)

&nbsp;

### Period와 Duration

두 날짜 혹은 두 시간의 간격을 표현하는 클래스

- `Period`: 날짜 간의 간격

- `Duration`: 시간 사이의 간격

&nbsp;

### 객체 생성하기 - now(), of()

`java.time` 패키지에 속한 클래스를 생성하는 기본적인 방법

1. 현재 날짜와 시간 기준으로 생성

```java
LocalDate ld = LocalDate.now(); // 2022-01-27
LocalTime lt = LocalTime.now(); // 13:37:58.628031
LocalDateTime ldt = LocalDateTime.now(); // 2022-01-27T13:37:58.628108
ZonedDateTime zdt = ZonedDateTime.now(); // 2022-01-27T13:37:58.628600+09:00[Asia/Seoul]
```

2. 지정된 날짜와 시간 기준으로 생성

```java
LocalDate ld = LocalDate.of(1996, 10, 28); // 1996-10-28
LocalTime lt = LocalDate.of(16, 30, 30); // 16:30:17

LocalDateTime ldt = LocalDateTime.of(ld, lt); // 1996-10-28T16:30:17
ZonedDateTime zdt = ZonedDateTime.of(ldt, ZoneId.of("Asia/Seoul")); // 1996-10-28T16:30:17+09:00[Asia/Seoul]
```

&nbsp;

### Temporal과 TemporalAmount

Temporal은 "시간의"라는 뜻인데, time(시분초)보다 더 넓은 범위인 연월일시분초를 내포하는 용어로 쓰임.

1. `Temporal`, `TemporalAccessor`, `TemporalAdjuster` 인터페이스를 구현한 클래스:

   `LocalDate`, `LocalTime`, `LocalDateTime`, `ZonedDateTime`, `Instant` 등

2. `TemporalAmount` 인터페이스를 구현한 클래스:

   `Period`, `Duration`

&nbsp;

### TemporalUnit과 TemporalField

`Chrono~`에 열거된 상수들은 날짜/시간 관련 메서드에서 요구하는 `Temporal~` 타입의 매개변수에 대입해서 사용하게 됨.

- `TemporalUnit`: 날짜와 시간의 단위를 정의한 인터페이스

  &#8594; `ChronoUnit`: `TemporalUnit`을 구현한 `enum`

- `TemporalField`: 연, 월, 일 등 날짜와 시간의 필드를 정의한 인터페이스

  &#8594; `ChronoField`: `TemporaField`을 구현한 `enum`

&nbsp;

## LocalDate와 LocalTime

### 특정 필드의 값 가져오기 - get()

`Calendar`와 다르게 월은 1 ~ 12의 범위이고, 요일은 월요일(1)을 시작으로 1 ~ 7의 범위를 가짐.

- `get()`과 `getLong()`:

  원하는 필드(`ChronoField` enum의 상수 중 하나)를 직접 지정해서 값을 얻을 수 있음

  ```java
    LocalDate ld = LocalDate.now();
    System.out.println(ld); // 2022-01-27
    int dom = ld.get(ChronoField.DAY_OF_MONTH);
    System.out.println(dom); // 27
  ```

  > 대부분은 `int`형 범위 내의 값이라서 `get`을 쓰지만, `ChronoField.NANO_OF_DAY`(그 날의 몇 나노초)처럼 큰 값은 `long` 타입을 반환하는 `getLong()`을 사용

- `getYear()`, `getMonth()`, `getDayOfMonth()` 등:

  이름에 명시된 특정 필드의 값을 얻을 수 있음

  ```java
    LocalDate ld = LocalDate.now();
    System.out.println(ld); // 2022-01-27
    long m = ld.getMonthValue();
    System.out.println(m); // 1
  ```

&nbsp;

### 필드의 값 변경하기 - with(), plus(), minus()

- `with()`:

  원하는 필드(`ChronoField` enum의 상수 중 하나)를 직접 지정해서 값을 변경할 수 있음

  ```java
    LocalDate ld = LocalDate.now();
    System.out.println(ld); // 2022-01-27
    ld = ld.with(ChronoField.DAY_OF_WEEK, 1); // 요일을 월요일(1)로 변경함
    System.out.println(ld); // 2022-01-24
  ```

- `withYear()`, `withDayOfMonth()`, `withHour()` 등:

  이름에 명시된 특정 필드의 값을 변경할 수 있음

  ```java
    LocalDate ld = LocalDate.now();
    System.out.println(ld); // 2022-01-27
    ld = ld.withMonth(12); // 월을 12월로 변경함
    System.out.println(ld); // 2022-12-27
  ```

- `plus()`, `minus()`:

  원하는 필드에 값을 더하거나 뺄 수 있음

  ```java
  LocalDate ld = LocalDate.now();
  System.out.println(ld); // 2022-01-27
  ld = ld.plus(100, ChronoUnit.YEARS); // 연도에 100을 더함
  System.out.println(ld); // 2122-01-27
  ```

  > 두번째 인자는 `ChronoField`(필드명)가 아닌 `ChronoUnit`(단위)임

&nbsp;

### 날짜와 시간의 비교 - isAfter(), isBefore(), isEqual()

1. `compareTo()`

   `LocalDate`, `LocalTime` 클래스에도 `compareTo()` 메서드가 오버라이딩 되어있어서 전후관계 비교에 사용할 수 있음

   ```java
   LocalDate ld1 = LocalDate.of(1996, 10, 28);
   LocalDate ld2 = LocalDate.now();
   System.out.println(ld2.compareTo(ld1)); // 26
   ```

   > 같으면 0, 이전이면 음수, 이후면 양수 반환

&nbsp;

2. `isAfter()`, `isBefore()`, `isEqual()`

`compareTo()`보다 좀 더 직관적인 방식 (비교 결과에 따른 `boolean`값을 반환)

```java
LocalDate ld1 = LocalDate.of(1996, 10, 28);
LocalDate ld2 = LocalDate.now();
System.out.println(ld2.isEqual(ld1)); // false
System.out.println(ld2.isBefore(ld1)); // false
System.out.println(ld2.isAfter(ld1)); // true
```

> `equals()`도 사용할 수 있지만, 연표가 다른 경우에는 무조건 `false`가 나오므로, `isEqual()`을 사용

&nbsp;

## Instant

에포크 타임으로부터 경과된 시간을 나노초 단위로 표현

> **에포크 타임(EPOCH TIME)**
>
> 1970-01-01 00:00:00 UTC

`Instant`는 UTC(+00:00) 기준이라 한국 기준(+09:00)과 9시간의 갭이 존재함

&nbsp;

### Instant 생성

`now()` 또는 `ofEpochSecond()`으로 생성

```java
Instant now = Instant.now();
Instant now2 = Instant.ofEpochSecond(now.getEpochSecond());
Instant now3 = Instant.ofEpochSecond(now.getEpochSecond(), now.getNano());
```

### Instant의 필드값 가져오기

`Instant`는 시간을 초 단위, 나노초 단위 2가지로 방식으로 나누어 저장하기 때문에 각각 `getEpochSecond()` 또는 `getNano()`를 사용하여 값을 가져올 수 있음.

오라클 DB의 타임스탬프같은 밀리초 단위의 사용을 위해 `toEpochMilli()` 메서드도 존재함.

```java
long es = now.getEpochSecond();
int ns = now.getNano();
long ms = now.toEpochMilli();
```

&nbsp;

### Instant와 Date 간의 형변환

`Instant`가 기존의 `java.util.Date`를 대체하기 위한 것이기 때문에 상호 변환이 가능함.

1. `Instant` &#8594; `Date`

   ```java
   Instant i = Instant.now();
   Date d = Date.from(i);
   ```

2. `Date` &#8594; `Instant`

   ```java
   Date d = new Date();
   Instant i = d.toInstant();
   ```

&nbsp;

## LocalDateTime과 ZonedDateTime

### LocalDate와 LocalTime으로 LocalDateTime 만들기

`LocalDateTime`의 `of()`, `atTime()`, `atDate()` 메서드 사용

```java
LocalDate ld = LocalDate.now();
LocalTime lt = LocalTime.now();

LocalDateTime ldt1 = LocalDateTime.of(ld, lt);
LocalDateTime ldt2 = ld.atTime(lt);
LocalDateTime ldt3 = lt.atDate(ld);
```

&nbsp;

### LocalDateTime의 변환

반대로 `LocalTime`이나 `LocalDate`로 변환

`LocalDateTime`의 `toLocalDate()`와 `toLocalTime()` 메서드 사용

```java
LocalDateTime ldt = LocalDateTime.now();
LocalDate ld = ldt.toLocalDate();
LocalTime lt = ldt.toLocalTime();
```

&nbsp;

### LocalDateTime으로 ZonedDateTime 만들기

`ZoneId` 클래스의 `of()` 메서드로 시간대 정보를 얻은 뒤, `LocalDateTime`에 붙여주면 됨

```java
LocalDateTime ldt = LocalDateTime.now();
ZoneId zid = ZoneId.of("Asia/Seoul");
ZonedDateTime zdt = ldt.atZone(zid);
```

다른 시간대에 있는 곳의 시간 알아내기

```java
ZoneId zid = ZoneId.of("America/New_York");
ZonedDateTime zdt = ZonedDateTime.now().withZoneSameInstant(zid);
```

&nbsp;

### ZoneOffset

시간대가 UTC 기준(+00:00)과 얼만큼 차이 나는지 표현

```java
ZoneOffset krOffset = ZonedDateTime.now().getOffset(); // +09:00
```

&nbsp;

### OffsetDateTime

`ZonedDateTime`은 `ZoneId`로 시간대를 구분하는데, 그 대신 `ZoneOffset`으로 구분하는 것이 `OffsetDateTime`

`ZoneId`는 써머 타임 같은 규칙들이 포함되어있는데 `ZoneOffset`은 그런걸 다 배제하고 오로지 시간의 차이만을 내포함.

```java
LocalDateTime ldt = LocalDateTime.now();
ZoneOffset krOffset = ZonedDateTime.now().getOffset();
OffsetDateTime odt = OffsetDateTime.of(ldt, krOffset);
```

&nbsp;

### ZonedDateTime의 변환

`ZonedDateTime` &#8594; `GregorianCalendar`

```java
ZonedDateTime zdt = ZonedDateTime.now();
Calendar c = GregorianCalendar.from(zdt);
```

&nbsp;

## TemporalAdjusters

`plus()`, `minus()` 같은 메서드로 날짜와 시간 연산을 해서 원하는 값을 얻을 수 있지만, 자주 필요한 값은 좀 더 편리하게 얻을 수 있게 하기 위해 `TemporalAdjusters` 클래스에서 메서드로 제공

```java
LocalDate ld = LocalDate.of(1996, 10, 28);
LocalDate d = ld.with(TemporalAdjusters.next(DayOfWeek.FRIDAY)); // 기준일에서 가장 가까운 다음 금요일의 날짜
System.out.println(d); // 1996-11-01
```

예시에 등장한 `next()` 외에도 여러 메서드 존재 (교재 p.565)

&nbsp;

### TemporalAdjuster 직접 구현하기

만약 `TemporalAdjusters` 클래스에 있는 메서드로도 충분치 않다면, 원하는 기능을 가진 클래스와 메서드를 직접 구현해서 쓰면 됨

단, 새로 만들 클래스는 `TemporalAdjuster` 인터페이스를 구현해야함. (유일한 추상메서드인 `adjustInto()` 구현 필요)

구현하는 것은 `adjustInto()`지만, 내부적으로만 사용되는 메서드이므로, 실제 적용할 때는 `with()`을 사용하고 인자에 새로 만든 클래스의 인스턴스를 넘김.

```java
class CustomTAExample {
    public static void main(String[] args) {
        LocalDate d1 = LocalDate.now();
        LocalDate d2 = d1.with(new DayAfterTomorrow()); // with를 사용했고, 새 클래스의 인스턴스를 넘김
        System.out.println(d2);
    }
}

class DayAfterTomorrow implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal t) {
        return t.plus(2, ChronoUnit.DAYS); // 일 단위로 2를 더한다 = 내일 모레
    }
}
```

&nbsp;

## Period와 Duration

### between()

1. Period(날짜 사이의 간격)

   ```java
   LocalDate ld1 = LocalDate.of(1996, 10, 28);
   LocalDate ld2 = LocalDate.now();

   Period p = Period.between(ld1, ld2);
   System.out.println(p); // P25Y3M
   ```

   > P25Y3M은 25년 3개월이라는 의미

&nbsp;

2. Duration(시간 사이의 간격)

   ```java
   LocalTime lt1 = LocalTime.of(10,0, 0);
   LocalTime lt2 = LocalTime.now();

   Duration d = Duration.between(lt1, lt2);
   System.out.println(d);
   ```

   > PT7H17M40.072211S은 7시간 17분 40.072211초라는 의미

&nbsp;

`Period`나 `Duration`으로부터 특정 필드의 값을 얻을 때는 `get()` 사용

직전에 구했던 `p`를 가지고 년수만 추출하려면,

```java
Period p = Period.between(ld1, ld2);
long year = p.get(ChronoUnit.YEARS);
System.out.println(year);
```

같은 방식으로 월, 일을 각각 추출할 수 있음.

`Duration`의 경우에는 초와 나노초 값만 추출 가능함.

> 시간이나 분은 초를 구해서 환산해주거나 아예 `Duration`을 `LocalTime`으로 변환한 다음 `LocalTime`의 메서드를 사용해서 구함.

&nbsp;

### between()과 until()

둘은 기능적으로 유사하지만 `between()`은 스태틱, `until()`은 인스턴스 메서드임.

```java
LocalDate today = LocalDate.now();
LocalDate bday = LocalDate.of(2022, 10, 28);
long dday = today.until(bday, ChronoUnit.DAYS);
System.out.println(dday);
```

&nbsp;

### of(), with()

`Period`와 `Duration`에도 `of()` 메서드 시리즈를 이용해 필드값 지정이 가능하고, `with()` 메서드 시리즈를 이용해 필드값 변경이 가능함.

```java
Period p = Period.of(3, 6, 10); // 3년 6개월 10일
Duration d = Duration.ofSeconds(30); // 30초

p = p.withMonths(8); // 3년 8개월 10일로 변경
d = d.withSeconds(45); //45초로 변경
```

&nbsp;

### 사칙연산, 비교연산, 기타 메서드

`plus()`, `minus()` 말고도 `multipliedBy()`(곱셉), `dividedBy()`(나눗셈) 메서드 존재

> 단, `Period`는 날짜 기간이므로 나눗셈 메서드 없음

```java
Period p = Period.of(3, 6, 10);
Duration d = Duration.ofSeconds(30);

p = p.multipliedBy(2); // 6년 12개월 20일
d = d.dividedBy(3); // 10초
```

이외에도 `Peroid`와 `Duration` 산출값에 대해 아래 메서드를 활용 가능

- `isNegative()`: 음수인지 확인

- `isZero()`: 0인지 확인

- `negate()`: 부호를 반대로 변경

- `abs()`: 부호 없앰

&nbsp;

### 다른 단위로 변환 - toTotalMonths(), toDays(), toHours(), toMinutes()

to로 시작하는 메서드를 통해 `Peroid`와 `Duration` 산출값을 특정 단위로 변경 가능

```java
Period p = Period.of(3, 6, 10);
Duration d = Duration.ofSeconds(30);

long months = p.toTotalMonths(); // 42
long minutes = d.toMinutes(); // 0
```

> 30초가 0분으로 변환된다는 점에서 볼 수 있듯, 지정된 단위 이하의 값은 모두 버려짐

&nbsp;

## 파싱과 포맷

`java.time.format` 패키지의 `DateTimeFormatter`를 통해 날짜와 시간을 원하는 형식으로 출력할 수 있음.

> `LocalTime`과 `LocalDate`에도 `format()`이 있으므로 편한 방식으로 하면 됨

```java
LocalDate ld = LocalDate.of(2022, 10, 28);
String fDate = ld.format(DateTimeFormatter.ISO_LOCAL_DATE); // 2022-10-28
String fDate2 = DateTimeFormatter.ISO_LOCAL_DATE.format(ld); // 2022-10-28
```

`ISO_LOCAL_DATE`와 같은 상수들은 `DateTimeFormatter`에 정의되어있음 (교재 p. 572)

&nbsp;

### 로케일에 종속된 형식화

`ofLocalizedDate()`, `ofLocalizedTime()`, `ofLocalizedDateTime()`은 현재 지역에 종속적인 formatter를 생성함

```java
LocalDate ld = LocalDate.of(2022, 10, 28);
DateTimeFormatter formatter = DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL);
String fDate = ld.format(formatter); // 2022년 10월 28일 금요일
```

`FormatStyle` enum에 정의된 상수마다 출력형태가 다름.

&nbsp;

### 출력형식 직접 정의하기

`DateTimeFormatter`의 `ofPattern()`으로 원하는 방식의 출력형태를 만들 수 있음.

형식화 클래스 `SimpleDateFormat`에서 봤던 패턴 기호들과 유사함.

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd"); // 2022/10/28 과 같은 형태로 출력시키는 패턴 생성
```

&nbsp;

### 문자열을 날짜와 시간으로 파싱하기

`parse()`를 사용하여 문자열로 받은 날짜 데이터를 `LocalDate`, `LocalTime`, `LocalDateTime` 타입으로 파싱할 수 있음

```java
LocalDate ld = LocalDate.parse("1996-10-28", DateTimeFormatter.ISO_LOCAL_DATE);
```

`ofPattern()`으로 커스텀 패턴을 만든 후, 적용하는 것도 가능함

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
LocalDate ld = LocalDate.parse("1996/10/28", formatter);
```
