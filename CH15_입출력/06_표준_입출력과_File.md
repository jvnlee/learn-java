# 표준 입출력과 File

## 표준 입출력 - System.in, System.out, System.err

표준 입출력(standard I/O)은 console로 받는 데이터 입력과 console에 찍히는 데이터 출력을 의미함.

`Java`에서는 표준 입출력을 위해 3가지의 입출력 스트림을 제공함

- `System.in`: console로부터 데이터를 입력받는데에 사용

- `System.out`: console에 데이터를 출력하는데에 사용

- `System.err`: console에 데이터를 출력하는데에 사용 (에러와 관련한 내용)

이들은 애플리케이션 실행 시 자동적으로 생성되기 때문에 별도로 생성해줄 필요가 없음.

> 즉, 표준 입출력은 Java 애플리케이션과 console의 사이를 이어주는 스트림이라고 보면 됨.

&nbsp;

```java
public final class System {
    public final static InputStream in = nullInputStream();
    public final static PrintStream out = nullPrintStream();
    public final static PrintStream err = nullPrintStream();
    ...
}
```

소스 코드에서 알 수 있듯 `in`, `out`, `err`는 모두 static 변수이고, 타입은 각각 `InputStream`과 `PrintStream`이지만,

실제로는 `BufferedInputStream`과 `BufferedOutputStream`의 인스턴스를 사용함.

&nbsp;

```java
class StandardIOExample {
    public static void main(String[] args) {
        try {
            int input = 0;

            while ((input = System.in.read()) != -1) {
                System.out.println("Input: " + input + ", Casted Input (char): " + (char)input);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

실행 결과)

    hello
    Input: 104, Casted Input (char): h
    Input: 101, Casted Input (char): e
    Input: 108, Casted Input (char): l
    Input: 108, Casted Input (char): l
    Input: 111, Casted Input (char): o
    Input: 10, Casted Input (char):

    ^D

예제를 실행시키면 콘솔에서는 사용자 입력을 기다리는 대기 상태가 되는데, "hello"를 입력하고 ^D로 빠져나왔음.

`System.in`을 통한 콘솔 입력은 위에서 언급된 것처럼 실제로는 버퍼를 가진 `BufferedInputStream`에 의해 데이터를 받아와 읽기 때문에 한번에 버퍼의 크기 만큼 입력을 받을 수 있음 (2048 byte)

입력된 데이터가 버퍼의 크기를 초과하거나, Enter나 ^D 명령어가 입력될 때까지는 계속 입력을 기다리는 대기 상태(blocking 상태)에 있음.

^D 명령어를 입력하면 스트림의 끝, End of File을 의미하기 때문에 `read()`가 -1을 반환하고, while문을 벗어나 프로세스가 종료됨.

> Windows에서는 `^Z`, Linux 계열에서는 `^D` (작성자의 맥에서는 Cmd + D로 입력할 수 있었음)

&nbsp;

여기서 문제는 hello 입력후 누르는 Enter키도 사용자 입력으로 간주된다는 것. (콘솔 마지막에 빈칸으로 찍힘)

이를 해결하려면 기반 스트림 `System.in`에 보조 스트림 `BufferedReader`를 연계하고, `readLine()`으로 줄 단위 입력을 받으면 됨.

&nbsp;

## 표준 입출력의 대상 변경 - setOut(), setErr(), setIn()

`System`의 static 메서드로, `System.in`, `System.out`, `System.err`의 입출력 대상을 콘솔 이외의 다른 대상으로 변경할 수 있게 해줌.

- static void `setOut`(PrintStream out)

  `System.out`이 출력을 할 곳을 지정된 `PrintStream`으로 변경

- static void `setErr`(PrintStream err)

  `System.err`이 출력을 할 곳을 지정된 `PrintStream`으로 변경

- static void `setIn`(InputStream in)

  `System.in`이 입력을 받을 수 있는 곳을 지정된 `InputStream`으로 변경

&nbsp;

```java
class StandardIOExample2 {
    public static void main(String[] args) {
        FileOutputStream fos = null;
        PrintStream ps = null;

        try {
            fos = new FileOutputStream("data.txt");
            ps = new PrintStream(fos);
            System.setOut(ps); // System.out의 출력 대상을 data.txt로 변경
        } catch (FileNotFoundException e) {
            System.err.println("File not found!");
        }

        System.out.println("Hello by System.out"); // 이 내용은 data.txt에 출력됨
        System.err.println("Hello by System.err"); // 이 내용은 콘솔에 출력됨
    }
}
```

실행 결과)

    Hello by System.err

&nbsp;

## RandomAccessFile

1. 입출력이 모두 가능한 클래스

   지금까지 배운 스트림의 개념으로는 입력과 출력은 별도의 작업으로만 가능하고 한번에는 불가능함

   그러나 `RandomAccessFile`은 `InputStream`이나 `OutputStream`으로부터 상속받지 않고, `DataInput`과 `DataOutput` 인터페이스를 동시에 구현하기 때문에 읽기와 쓰기 모두가 가능함.

2. 기본형 단위 읽기/쓰기 작업이 가능

   `DataInput`과 `DataOutput` 인터페이스에는 기본형 단위로 데이터를 다루는 메서드가 정의되어 있기 때문

3. 파일의 어느 위치에서나 읽기/쓰기 작업 가능

   모든 입출력 클래스는 내부적으로 파일 포인터를 가지고 있음. `RandomAccessFile`을 제외한 모든 클래스는 순차적 읽기/쓰기 작업만 하기 때문에 포인터가 파일의 처음부터 시작해서 순차적 이동만 가능. (포인터의 위치 변경 불가)

   그러나 `RandomAccessFile`는 원한다면 파일 포인터를 특정 위치로 이동시켜서 작업할 수 있음

&nbsp;

생성자:

- RandomAccessFile(File file, String mode)

  RandomAccessFile(String fileName, String mode)

  주어진 파일에 읽기, 또는 읽기+쓰기를 하기 위한 `RandomAccessFile` 인스턴스를 생성

  > mode는 다음 중에 지정 가능
  >
  > - "r": 읽기만 수행
  >
  > - "rw": 파일에 읽기와 쓰기 모두 수행
  >
  > - "rws": "rw"와 같고, 출력 내용이 지연 없이 파일에 바로 쓰이게 함. 파일의 메타정보도 포함하여 출력
  >
  > - "rwd": "rw"와 같고, 출력 내용이 지연 없이 파일에 바로 쓰이게 함. 파일의 내용만 출력

&nbsp;

메서드:

- FileChannel `getChannel`()

  파일이 가진 file channel 반환 (채널을 통해 파일의 데이터를 읽거나, 파일에 데이터를 쓸 수 있음)

- FileDescriptor `getFD`()

  파일이 가진 file descriptor 반환

- long `getFilePointer`()

  파일 포인터의 위치 반환

- long `length`()

  파일의 크기 반환 (단위: byte)

- void `seek`(long pos)

  파일 포인터의 위치를 파일의 시작점부터 `pos`만큼 떨어진 곳으로 변경 (단위: byte)

- void `setLength`(long newLength)

  파일의 크기를 지정된 크기로 변경

- int `skipBytes`(int n)

  지정된 수 만큼의 byte를 건너뜀

&nbsp;

```java
class RandomAccessFileExample {
    public static void main(String[] args) {
        try {
            RandomAccessFile raf = new RandomAccessFile("data.txt", "rw");
            System.out.println("File Pointer's Position: " + raf.getFilePointer());

            raf.writeInt(100);
            System.out.println("File Pointer's Position: " + raf.getFilePointer());

            raf.writeLong(100L);
            System.out.println("File Pointer's Position: " + raf.getFilePointer());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

실행 결과)

    File Pointer's Position: 0
    File Pointer's Position: 4
    File Pointer's Position: 12

파일 포인터의 처음 위치는 파일의 시작점이므로 0

`int` 100을 쓰고 난 후에는 4(byte)만큼 이동해서 4

`long` 100을 쓰고 난 후에는 8(byte)만큼 이동해서 12

&nbsp;

```java
class RandomAccessFileExample2 {
    public static void main(String[] args) {
        int[] scoreData = {1, 100, 90, 90,
                            2, 70, 90, 100,
                            3, 100, 100, 100,
                            4, 70, 60, 80,
                            5, 70, 90, 100
        };

        try {
            RandomAccessFile raf = new RandomAccessFile("data.txt", "rw");
            for (int i = 0; i < scoreData.length; i++) {
                raf.writeInt(scoreData[i]);
            }

            while (true) {
                System.out.println(raf.readInt());
            }
        } catch (EOFException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

점수 데이터를 data.txt에 저장시킨다음, data.txt에 저장된 데이터를 읽어서 출력하도록 했으나

이미 포인터는 쓰기 작업을 마치면서 파일의 맨끝에 가있기 때문에 아무것도 읽지 못하고 `EOFException`을 발생시킴.

따라서 읽기를 수행할 `while`문 앞에

```java
raf.seek(0);
```

을 추가해 포인터 위치를 파일의 맨 앞으로 돌려놓아주어야함.

`RandomAccessFile`은 읽기와 쓰기를 함께하기 때문에 이런식으로 포인터 위치 조정을 항상 염두에 두고 사용해야함.
