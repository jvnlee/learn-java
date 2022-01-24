# java.lang 패키지

`Java` 프로그래밍에 필요한 기본적인 클래스들을 포함하고 있는 패키지.

그래서 특별히 `import`문 없이도 바로 가져다 쓸 수 있음.

&nbsp;

## Object 클래스

모든 클래스의 최고 조상이므로, 모든 클래스에서는 `Object` 클래스의 멤버들을 사용할 수 있음 (11개의 메서드 제공)

### 1. equals

- 매개 변수: `Object` obj

- 반환 타입: `boolean`

매개 변수로 객체를 가리키는 참조변수를 받아와서 객체 주소값의 일치 여부를 비교해줌

```java
ExampleClass ec = new ExampleClass();
ExampleClass ec2 = new ExampleClass();
ec2.equals(ec); // false

ec2 = ec;
ec2.equals(ec); // true
```

> 만약 단순히 객체의 주소값을 비교하는 것이 아니라, 객체의 내용을 비교하고 싶다면 클래스에서 `equals`를 오버라이딩하면 됨
>
> 실제로, `Object`를 상속 받는 `String`, `Date`, `File` 등의 클래스에는 주소값이 아닌 내용 비교를 하도록 `equals`가 오버라이딩 되어있음.

&nbsp;

### 2. hashcode

- 매개 변수: 없음

- 반환 타입: `int`

값이 저장된 위치를 나타내는 hash code를 반환해줌

`equals`와 마찬가지로 객체의 비교에 활용되기 때문에 `String`과 같은 일부 클래스에서는 객체의 내용이 같으면 같은 해시코드를 반환하게끔 오버라이딩 되어있음

그러나 원래는 서로 다른 객체라면 서로 다른 hash code를 가지고 있어야 정상.

```java
class HashCodeExample {
    public static void main(String[] args) {
        String str1 = new String("abc");
        String str2 = new String("abc");

        System.out.println(str2.equals(str1)); // true
        System.out.println(str1.hashCode()); // 96354
        System.out.println(str2.hashCode()); // 96354
        System.out.println(System.identityHashCode(str1)); // 1175962212
        System.out.println(System.identityHashCode(str2)); // 918221580
    }
}
```

> `String`에 오버라이딩된 `hashcode`와는 달리, `System.identityHashCode`는 객체의 주소값으로 해시코드를 생성함.
>
> 따라서, 설령 두 `String` 객체의 해시코드가 같아도 다른 객체임을 확인할 수 있음.

&nbsp;

### 3. toString

- 매개 변수: 없음

- 반환 타입: `String`

인스턴스의 정보를 클래스의 이름과 16진수의 해시코드로 된 문자열로 반환해줌 (일반적으로 정보라 함은 인스턴스 변수에 저장된 값들을 지칭)

```java
class toStringExample {
    public static void main(String[] args) {
        Book b1 = new Book();
        Book b2 = new Book();

        System.out.println(b1.toString()); // Book@36baf30c
        System.out.println(b2.toString()); // Book@7a81197d
    }
}

class Book {
    String title;
    String author;
    int totalPages;

    Book() {
        this("Untitled", "Unknown", 1);
    }
    Book(String title, String author, int totalPages) {
        this.title = title;
        this.author = author;
        this.totalPages = totalPages;
    }
}
```

인스턴스나 클래스의 정보 등을 문자열로 변환하여 반환하도록 오버라이딩하는 것이 일반적

> `Date` 같은 클래스에는 `toString`이 오버라이드 되어있어 해시코드가 아닌 인스턴스가 가진 날짜와 시간이 문자열로 반환됨

&nbsp;

### 4. clone

- 매개 변수: 없음

- 반환 타입: `Object`

자신을 복제하여 새로운 인스턴스를 생성하고 그것을 반환해줌.

`Cloneable` 인터페이스를 구현한 클래스만 `clone` 메서드를 호출하여 사용할 수 있고, `CloneNotSupportedException`에 대한 예외 처리를 해주어야 함.

> 📌 [주의] 만약 참조타입의 인스턴스 변수가 있는 클래스를 `clone`으로 복제하면 얕은 복사가 되어 복제본의 변형이 원본에도 영향을 끼칠 수 있음.

```java
class cloneExample {
    public static void main(String[] args) {
        Dummy d1 = new Dummy();
        Dummy d2 = (Dummy)d1.clone();

        d2.nums[0] = 777;
        System.out.println(d1.nums[0]); // 777
    }
}

class Dummy implements Cloneable { // Cloneable 구현
    int[] nums = {1, 2, 3}; // 참조형 인스턴스 변수 (배열)

    public Object clone() { // 조상인 Object의 clone 메서드는 protected 이므로, public으로 넓혀줌
        Object obj = null;
        try {
                obj = super.clone(); // 조상인 Object의 clone 메서드 호출
        } catch (CloneNotSupportedException e) {}
        return obj;
    }
}
```

&nbsp;

### 공변 반환타입

직전의 예시에서 `d1.clone()`을 자손 타입인 `Dummy`로 형변환하는게 조금 번거롭다고 느껴질 수 있음.

`JDK` 1.5부터는 "조상 메서드의 반환 타입을 자손 클래스 타입으로 변경"할 수 있도록 허용하는 **공변 반환타입**을 지원함.

&nbsp;

오버라이딩된 `clone` 메서드의 반환 타입을 바꿔주면,

```java
public Dummy clone() {
    ...
    return (Dummy)obj;
}
```

형변환 없이도 복제된 객체를 사용할 수 있음

```java
Dummy d1 = new Dummy();
Dummy d2 = d1.clone();
```

&nbsp;

### 얕은 복사와 깊은 복사

`clone` 메서드는 객체에 저장된 "값"을 복사하기 때문에 얕은 복사가 될 수도 있고, 깊은 복사가 될 수도 있음.

&nbsp;

복사하려는 값이 "실제 데이터"(기본형)가 아닌, 데이터가 담긴 곳을 가리키는 "주소"(참조형)라면 복제본의 변형이 원본에도 영향을 줄 수 있음을 의미함.

이러한 복사를 <b>얕은 복사(shallow copy)</b>라고 부름.

&nbsp;

그러나 주소값이 아니라 참조하고 있는 "실제 데이터"를 복제해오면 원본과 복제본은 서로 영향을 주고 받을일이 없음.

이러한 복사를 <b>깊은 복사(deep copy)</b>라고 부름.

```java
class cloneExample {
    public static void main(String[] args) {
        Dummy d1 = new Dummy();
        Dummy d2 = d1.shallowCopy();
        Dummy d3 = d1.deepCopy();

        d1.num[0] = 777;

        System.out.println(d2.num[0]); // 777
        System.out.println(d3.num[0]); // 1
    }
}

class Dummy implements Cloneable {
    int[] num = {1, 2, 3};

    public Dummy shallowCopy() {
        Object obj = null;
        try {
                obj = super.clone();
        } catch (CloneNotSupportedException e) {}

        return (Dummy)obj;
    }

    public Dummy deepCopy() {
        Object obj = null;
        try {
                obj = super.clone();
        } catch (CloneNotSupportedException e) {}
        Dummy d = (Dummy)obj;
        d.num = this.num.clone(); // 배열도 Object의 일종이므로 clone 메서드 사용 가능

        return d;
    }
}
```

&nbsp;

### 5. getClass

- 매개 변수: 없음

- 반환 타입: `Class` (이름이 Class인 클래스)

자신이 속한 클래스의 `Class` 객체를 반환함.

&nbsp;

### Class 객체

- 클래스의 모든 정보를 담고 있음 (정보는 사용하기 편한 형태로 변환되어 저장됨)

  > 클래스에 정의된 멤버의 이름, 개수 등...

- 클래스 당 1개만 존재

- 클래스 파일이 ClassLoader에 의해서 메모리에 올라갈 때 자동으로 생성됨

- 객체 생성, 메서드 호출 등을 할 수 있게 해줌

  > ```java
  > Dummy d = Dummy.class.newInstance(); // new 연산자 없이, Class 객체를 이용한 새 인스턴스 생성
  > ```

&nbsp;

### Class 객체를 얻는 방법

3가지 방법

```java
// 1. 객체로부터 얻는 방법
Class c = new Dummy().getClass();
// 2. 클래스 리터럴(.class)로부터 얻는 방법
Class c = Dummy.class;
// 3. 클래스 이름으로 얻는 방법
Class c = Class.forName("Dummy");
```

&nbsp;

## String 클래스

### 변경 불가능한 클래스

`String` 클래스의 인스턴스 생성 시, 매개변수로 입력받는 문자열은 `value`라는 이름의 `char`배열에 저장됨.

```java
private char[] value;
```

이 `value`의 값은 불변하고, 값의 변경처럼 보이는 것은 사실 새 값을 담은 새로운 `String` 인스턴스가 생성되는 것임

문자열을 다루는 작업은 메모리에 부담을 줄 수 있어, 그런 작업 시에는 `StringBuffer` 클래스를 사용하는 것이 권장됨.

> `StringBuffer`에서는 담긴 문자열을 추가 인스턴스 생성 없이 변경할 수 있음

&nbsp;

### 문자열의 비교

문자열을 생성할 때는 문자열 리터럴을 사용하거나, `String` 클래스의 생성자를 사용할 수 있음.

```java
String str1 = "abc"; // 문자열 리터럴 사용
String str2 = "abc";
String str3 = new String("abc"); // String 클래스의 생성자 사용
String str4 = new String("abc");
```

`str1`과 `str2`처럼 리터럴로 만든 문자열은 재활용 방식이기 때문에 내용이 같다면 굳이 새로 하나 더 만들지 않고, 같은 메모리 주소를 할당해줌.

> 따라서, 두 참조변수가 가리키는 주소는 동일 (같은 자원을 공유하는 셈)

`str3`과 `str4`처럼 생성자를 이용한 경우에는 `new` 연산자에 의해 매번 새 인스턴스가 생성되어 새로 할당받은 메모리에 저장됨.

> 둘이 가리키는 주소가 다름

&nbsp;

문자열은 `equals()` 메서드로 비교하면 내용이 같을 경우 모두 `true`지만 등가비교연산(`==`)을 해보면,

```java
System.out.println(str1 == str2); // true
System.out.println(str3 == str4); // false
```

위에서 봤던 내용이 사실임을 알 수 있음

&nbsp;

### 문자열 리터럴

소스파일에 포함된 문자열 리터럴은 컴파일 시에 클래스 파일(.class)에 저장됨

이 때, 같은 내용의 문자열은 1번만 저장됨.

그리고 이 리터럴들은 클래스 파일이 Class Loader에 의해 메모리에 올라갈 때 `JVM`의 상수 저장소(constant pool)에 저장됨

&nbsp;

### 빈 문자열

`String`클래스 내 `char`배열의 길이가 0이면, 빈 문자열이 됨.

```java
String s = "";
// char[] c = new char[0];
```

> 📌 [주의] `char`는 비어있을 수 없음.
>
> ```java
> char c = ""; // 불가능
> ```

보통 변수 선언시 타입마다 초기화 기본값이 있는데, `String`은 `null`, `char`은 `\u0000`(유니코드 상에서 null에 해당)

그렇지만 일반적으로 `String`은 빈 문자열, `char`은 공백(space 한칸)으로 초기화 함

&nbsp;

### String 클래스의 생성자와 메서드

1. String(String s)

   ```java
   String s = new String("Hello"); // "Hello"
   ```

   String(char[] value)

   ```java
   char[] c = {'H', 'e', 'l', 'l', 'o'};
   String s = new String(c); // "Hello"
   ```

   String(StringBuffer sb)

   ```java
   StringBuffer sb = new StringBuffer("Hello");
   String s = new String(sb); // "Hello"
   ```

2. char charAt(int index)

   지정된 index에 위치한 문자 반환

   ```java
   String s = "Hello";
   char c = s.charAt(1); // l
   ```

3. int compareTo(String s)

   인자로 받아온 문자열과 dictionary 순서로 비교

   순서상 동일하면 0, 이전이면 음수, 이후면 양수 반환

   ```java
   int n1 = "abcdefe".compareTo("dafsffsf"); // -3
   ```

4. String concat(String s)

   인자로 받아온 문자열을 뒤에 덧붙여줌

   ```java
   String s1 = "Hello";
   String s2 = s1.concat("!"); // Hello!
   ```

5. boolean contains(CharSequence s)

   인자로 받아온 문자열이 포함되었는지 검사

   ```java
   String s = "Hello";
   boolean b = s.contains("el"); // true
   ```

6. boolean endsWith(String suffix)

   인자로 받아온 문자열로 끝나는지 검사

   ```java
   String s = "Hello";
   boolean b = s.endsWith("llo"); // true
   ```

7. boolean equals(Object obj)

   `obj`와 문자열 내용 비교

   (`obj`가 `String`이 아니면 `false` 반환)

   ```java
   String s1 = "Hello";
   boolean b1 = s1.equals("Hello"); // true

   String s2 = new String("Hello");
   boolean b2 = s2.equals(s1); // true
   // s1 == s2 는 false
   ```

8. boolean equalsIgnoreCase(String s)

   대소문자 구분을 무시하고 문자열 내용 비교

   ```java
   String s = "hELLo";
   boolean b = s.equalsIgnoreCase("HelLo"); // true
   ```

9. int indexOf(int ch)

   주어진 문자가 문자열에 존재하는지 확인해보고, 있다면 위치(인덱스) 반환

   없다면 -1 반환

   ```java
   String s = "Hello";
   int idx = s.indexOf('o'); // 4
   ```

   int indexOf(int ch, int pos)

   지정된 위치(pos)부터 존재 여부를 살핌 (나머지는 상동)

   ```java
   String s = "Hello";
   int idx = s.indexOf('e', 3); // -`
   ```

   int indexOf(String s)

   주어진 문자열이 존재하는지 확인 (나머지 동일)

   ```java
   String s = "Hello";
   int idx = s.indexOf("ell"); // 1
   ```

10. String intern()

    문자열을 상수 저장소(constant pool)에 등록함. 이미 같은 내용의 문자열이 있다면 그 문자열의 주소값 반환

    ```java
    String s1 = new String("Hello");
    String s2 = "Hello";
    String s3 = "Hello";

    boolean b1 = (s1 == s2); // false
    boolean b2 = (s2 == s3); // true
    boolean b3 = (s2.intern() == s3.intern()); // true (상수 풀에는 같은 내용의 문자열이 하나만 저장되므로 주소값이 같음)
    ```

11. int lastIndexOf(int ch)

    주어진 문자(문자코드)를 문자열의 오른쪽 끝에서부터 찾기 시작해 첫번째로 일치한 곳의 인덱스 반환

    못 찾으면 -1 반환

    ```java
    String s = "Hello";
    int idx1 = s.lastIndexOf('l'); // 3
    int idx2 = s.indexOf('l'); // 2
    ```

    int lastIndexOf(String s)

    주어진 문자열을 오른쪽 끝에서부터 찾기 시작해 첫번째로 일치한 곳의 인덱스 반환

    못 찾으면 -1 반환

    ```java
    String s = "one.two.one";
    int idx1 = s.lastIndexOf("one"); // 8
    int idx2 = s.IndexOf("one"); // 0
    ```

12. int length()

    문자열의 길이 반환

    ```java
    String s = "Hello";
    int len = s.length(); // 5
    ```

13. String replace(char prev, char new)

    문자열 내에서 `prev` 문자를 `new` 문자로 바꾸고, 바뀐 문자열 반환

    ```java
    String s1 = "Hello";
    String s2 = s1.replace('e', 'a'); // Hallo
    ```

    String replace(charSequence prev, charSequence)

    문자가 아닌 문자열을 인자로 받아온다는 점 빼고 원리는 동일함

14. String replaceAll(String regex, String replacement)

    지정된 문자열(또는 정규식)과 일치하는 문자열을 `replacement`로 모두 바꾸고, 바뀐 문자열 반환

    ```java
    String s1 = "I love shrimp burgers and shrimp pasta"
    String s2 = s1.replaceAll("shrimp", "beef"); // I love beef burgers and beef pasta
    ```

15. String replaceFirst(String regex, String replacement)

    지정된 문자열(또는 정규식)과 일치하는 것 중 첫번째만 `replacement`로 바꾸고, 바뀐 문자열 반환

16. String[] split(String regex)

    문자열을 지정된 문자열(또는 정규식)로 쪼갠 뒤, 문자열 배열에 담아 그 배열을 반환

    ```java
    String s = "Hello.Java.World";
    String[] strArr = s.split("."); // ["Hello", "Java", "World"]
    ```

    String[] split(String regex, int limit)

    문자열을 쪼개되, `limit` 숫자 만큼만 쪼개어 배열에 담고, 그 배열을 반환

17. boolean startsWith(String prefix)

    주어진 문자열로 시작하는지 검사

18. String substring(int begin[, int end])

    주어진 시작 인덱스부터 맨끝(또는 끝 인덱스 - 1)까지에 해당하는 문자열을 얻음.

    ```java
    String s = "I love Java so much";
    String substr1 = s.substring(2, 6); // love
    String substr2 = s.substring(7); // Java so much
    ```

19. toLowerCase()

    문자열 내의 모든 문자를 소문자로 변환

20. toUpperCase()

    문자열 내의 모든 문자를 대문자로 변환

21. toString()

    `String` 인스턴스에 저장되어 있는 문자열 반환

22. trim()

    문자열 양끝에 존재하는 공백들을 모두 제거한 결과를 반환

    ```java
    String s1 = "     Hello, Java   ";
    String s2 = s1.trim(); // Hello Java
    ```

23. static String valueOf()

    매개변수로 진위형(`boolean`), 문자형(`char`), 정수형(`int`, `long`), 실수형(`float`, `double`), 참조형(`Object`) 타입이 올 수 있음.

    지정된 값을 문자열로 변환하여 반환하고, 참조변수인 경우는 `toString()`을 호출한 결과를 반환함.

    ```java
    String long = String.valueOf(100L); // 100

    Date d = new Date();
    String date = String.valueOf(d); // Mon Jan 24 23:15:25 KST 2022
    ```

&nbsp;

### join()과 StringJoiner

주어진 구분자를 기준으로 결합하여 문자열로 만듦. (`split`과 반대의 기능)

```java
String fruits = "apple,banana,orange";
String[] fruitsArr = fruits.split(","); // ["apple", "banana", "orange"]
String str = String.join("-", fruitsArr); // apple-banana-orange
```

`java.util.StringJoiner`로도 같은 기능을 구현할 수 있음

```java
StringJoiner sj = new StringJoiner("-", "", "");
for (String fruit : fruitsArr) {
        sj.add(fruit);
}
System.out.println(sj); // apple-banana-orange
```

&nbsp;

### 유니코드의 보충문자

앞서 보았던 `String` 메서드 내용 중에 문자인데 `int`형으로 취급하는 케이스들이 있었음

유니코드는 본래 16비트 문자체계인데, 이걸로 부족해서 20비트로 확장하면서 `char`가 아닌 `int`로 다루게 되었음.

이렇게 확장되면서 추가된 문자들을 **보충 문자(supplementary characters)**라고 부름

&nbsp;

### String.format()

지시자를 사용하여 문자열을 formatting 해줌. `printf()`와 사용법은 같음

&nbsp;

### 기본형 값을 String으로 변환하기

1. 숫자 + 빈 문자열("") &#8594; 문자열

2. `valueOf` 활용

```java
int i = 77;
String s = String.valueOf(i);
```

&nbsp;

### String을 기본형 값으로 변환하기

`parseInt`, `parseBoolean`, `parseFloat` 등... (모두 `String`을 매개변수로 받음)

```java
int i = Integer.parseInt("77");
// Integer i = Integer.valueOf("77); 과 같은데, valueOf는 반환타입이 Integer임.
```

`String`을 `char`로 변환하려면 `charAt` 활용

```java
String s = "a";
char c = s.charAt(0);
```
