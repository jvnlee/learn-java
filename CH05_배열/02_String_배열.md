# String 배열

앞서 보았던 기본형 배열이 아닌, 참조형 배열의 일종.

참조형 배열의 경우 배열에 각 객체의 주소가 저장됨. (그래서 "객체 배열"이라고도 부름)

&nbsp;

## 선언과 생성

```java
String[] 배열 이름 = new String[배열의 길이];
```

> `String` 배열의 경우 각 원소의 기본값은 `null`임

&nbsp;

## 초기화

각 원소마다 일일이 값을 지정해주거나,

```java
String[] fruits = new String[3];
fruits[0] = "apple";
fruits[1] = "banana";
fruits[2] = "orange";
```

> 원칙적으로는 `String`도 클래스이므로 아래와 같이 표기해야 하지만,
>
>       fruits[0] = new String("apple");
>
> `String`에 대해서만 예외적으로 인스턴스 생성을 생략할 수 있음.

&nbsp;

중괄호`{}`를 활용하여 보다 간편하게 초기화 할 수 있음

```java
String[] fruits = new String[]{"apple", "banana", "orange"};
String[] fruits = {"apple", "banana", "orange"}; // new String[] 생략 가능
```

&nbsp;

## char 배열과 String 클래스

문자열(`String`)은 개념적으로 문자(`char`)의 배열이라고 볼 수 있음.

그럼에도 `char` 배열이 아닌 `String` 클래스를 사용하는 이유는 `String` 클래스가 `char` 배열에 여러 기능(메서드)을 붙여 확장한 것이기 때문.

> C언어에서는 문자열을 `char` 배열로 다룸.

`String` 객체는 읽기만 가능하고, 내용을 변경할 수 없음. (변경하는 대신 새로운 문자열이 덮어씌워짐)

> 변경 가능한 문자열을 다루려면 `StringBuffer` 클래스를 사용하면 됨.

&nbsp;

### String 클래스의 주요 메서드

- `charAt(index)`

  파라미터로 index를 받아 문자열에서 해당 index에 있는 문자 반환

  ```java
  String exampleStr = "Hello";
  char extracted = exampleStr.charAt(3); // l
  ```

- `length()`

  문자열의 길이 반환

  ```java
  int len = exampleStr.length(); // 5
  ```

- `substring(from, to)`

  시작 인덱스와 끝 인덱스를 파라미터로 받아 해당 범위 안에(끝 인덱스는 포함 X) 있는 문자열 반환

  ```java
  String substr = exampleStr.substring(0, 2); // He
  ```

- `equals(obj)`

  파라미터로 객체(`Object`)를 받아와 문자열과 내용이 같은지 확인하여 `true` 또는 `false` 반환

  ```java
  String exampleStr2 = "abc";
  boolean isSame = exampleStr.equals(exampleStr2); // false
  ```

- `toCharArray()`

  문자열을 `char` 배열로 변환하여 반환

  ```java
  char[] charArr = exampleStr.toCharArray(); // Hello (타입만 String에서 char[]로 바뀜)
  ```

&nbsp;

## 커맨드 라인으로 String 입력 받기

기본 세팅으로 생성한 `class`는 메인 메서드인 `main`에 `String[]` 타입의 파라미터 `args`를 받음.

```java
public class someClass {
    public static void main(String[] args) {
        // ...
    }
}
```

커맨드라인을 통해 이 `main` 메서드에 바로 파라미터를 전달할 수 있음. (각 파라미터는 공백문자로 구분함)

    java 클래스이름 arg1 arg2 ...

이렇게 전달된 파라미터들은 클래스 내에서 접근 가능.

```java
System.out.println(args[0]); // arg1
System.out.println(args[1]); // arg2
...
```

이 때, 이 파라미터들은 모두 `String` 타입임을 주의.

&nbsp;

커맨드라인으로 파리미터를 입력하지 않은 경우, `JVM`은 크기가 0인 배열을 생성하여 `main` 메서드의 파라미터로 전달해줌. (args.length == 0)
