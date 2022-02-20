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

&nbsp;

## File

파일은 가장 많이 사용되는 입출력 대상

`Java`에서는 `File` 클래스를 통해 파일과 디렉토리를 다룰 수 있게함. 따라서 `File` 인스턴스는 파일이 아니라 디렉토리를 의미할 수도 있음

&nbsp;

생성자:

- File(String fileName)

  주어진 이름을 가진 파일 또는 디렉토리를 위해 `File` 인스턴스 생성.

  `fileName`은 주로 파일 경로까지 포함해서 지정하지만, 파일 이름만 넘기는 경우에는 프로그램이 실행되는 위치를 파일의 경로로 간주함.

- File(String pathName, String fileName)

  File(File pathName, String fileName)

  파일의 경로와 이름을 분리해서 따로 받을 수 있게한 생성자

  `File` 인스턴스는 파일의 경로(디렉토리)를 의미할 수도 있다고 했는데, 두번째 생성자의 첫 매개변수가 그런 케이스.

- File(URI uri)

  지정된 `URI`로 파일을 생성

&nbsp;

경로와 관련된 메서드:

- String `getName`()

  파일 이름을 문자열로 반환

- String `getPath`()

  파일의 경로를 문자열로 반환

- String `getAbsolutePath`()

  파일의 절대 경로를 문자열로 반환

  File `getAbsoluteFile`()

  파일의 절대 경로를 `File` 인스턴스로 반환

- String `getParent`()

  파일의 조상 디렉토리를 문자열로 반환

  File `getParentFile`()

  파일의 조상 디렉토리를 `File` 인스턴스로 반환

- String `getCanonicalPath`()

  파일의 정규 경로를 문자열로 반환

  File `getCanonicalFile`()

  파일의 정규 경로를 `File` 인스턴스로 반환

&nbsp;

그 외 `File`의 메서드:

(교재 p.918 참고)

&nbsp;

경로와 관련된 멤버 변수:

> OS마다 사용되는 파일 경로 작성 시 사용하는 구분자가 다르기 때문에 반드시 이 멤버 변수들을 활용해서 OS에 독립적인 코드로 짜야함

- static String `pathSeparator`

  OS에서 사용하는 경로 구분자를 문자열로 반환

  > 윈도우: `;`, 유닉스: `:`

- static Char `pathSeparator`

  OS에서 사용하는 경로 구분자를 문자로 반환

- static String `separator`

  OS에서 사용하는 이름 구분자를 문자열로 반환

  > 윈도우: `\`, 유닉스: `/`

- static char `separator`

  OS에서 사용하는 이름 구분자를 문자로 반환

&nbsp;

> <b>절대 경로(Absolute Path)</b>
>
> File system의 root로부터 시작되는 파일의 전체 경로
>
> 절대 경로는 현재 디렉토리를 의미하는 `.`같은 기호 또는 링크가 포함될 수 있기 때문에 여러가지 방법으로 나타낼 수 있음
>
> ex) 절대 경로는 아래와 같이 다른 방식으로 표현 가능함
>
> /Users/hyunjun/IdeaProjects/study/src/FileExample1.java
>
> /Users/hyunjun/IdeaProjects/study/./src/FileExample1.java (중간에 `.`가 포함되어있음)
>
> &nbsp;
>
> <b>정규 경로(Canonical Path)</b>
>
> 기호나 링크를 포함하지 않는 유일한 경로
>
> ex) /Users/hyunjun/IdeaProjects/study/src/FileExample1.java 가 유일한 정규 경로임

&nbsp;

생성자로 `File` 인스턴스를 만들었다고 해서 파일이나 디렉토리가 생성되는 것은 아님.

새로운 파일을 생성하려면

1. `File` 인스턴스 생성 후, 출력 스트림을 생성하거나,

```java
File f = new File("/Users/hyunjun/IdeaProjects/study/src", "NewFile.java");
FileOutputStream fos = new FileOutputStream(f);
```

2. `createNewFile()`을 호출 해야함

```java
File f = new File("/Users/hyunjun/IdeaProjects/study/src", "NewFile.java");
f.createNewFile();
```

&nbsp;

```java
class FileExample2 {
    static int totalFiles = 0;
    static int totalDirs = 0;

    public static void main(String[] args) {
        if (args.length != 1) {
            System.exit(0);
        }

        File dir = new File(args[0]);

        if (!dir.exists() || !dir.isDirectory()) {
            System.out.println("Invalid directory.");
            System.exit(0);
        }

        printFileList(dir);

        System.out.println("총 " + totalDirs + "개의 디렉토리, 총 " + totalFiles + "개의 파일");
    }

    public static void printFileList(File dir) {
        System.out.println("Directory: " + dir.getAbsolutePath());
        File[] files = dir.listFiles();

        ArrayList<String> subDir = new ArrayList<>();

        for (int i = 0; i < files.length; i++) {
            String fileName = files[i].getName();

            if (files[i].isDirectory()) {
                fileName = "[" + fileName + "]";
                subDir.add(String.valueOf(i));
            }

            System.out.println(fileName);
        }

        int dirNum = subDir.size();
        int fileNum = files.length - dirNum;

        totalDirs += dirNum;
        totalFiles += fileNum;

        System.out.println(dirNum + "개의 디렉토리, " + fileNum + "개의 파일");
        System.out.println();

        for (int i = 0; i < subDir.size(); i++) {
            int index = Integer.parseInt(subDir.get(i));
            printFileList(files[index]);
        }
    }
}
```

실행 결과)

    Directory: /Users/hyunjun/IdeaProjects/study
    study.iml
    [out]
    data.txt
    [.idea]
    [src]
    3개의 디렉토리, 2개의 파일

    Directory: /Users/hyunjun/IdeaProjects/study/out
    [production]
    1개의 디렉토리, 0개의 파일

    Directory: /Users/hyunjun/IdeaProjects/study/out/production
    [study]
    1개의 디렉토리, 0개의 파일

    Directory: /Users/hyunjun/IdeaProjects/study/out/production/study
    FileExample3.class
    NewFile.txt
    0개의 디렉토리, 2개의 파일

    Directory: /Users/hyunjun/IdeaProjects/study/.idea
    .gitignore
    workspace.xml
    modules.xml
    misc.xml
    0개의 디렉토리, 4개의 파일

    Directory: /Users/hyunjun/IdeaProjects/study/src
    BufferedReaderExample.java
    NewFile.txt
    0개의 디렉토리, 2개의 파일

    총 5개의 디렉토리, 총 10개의 파일

/Users/hyunjun/IdeaProjects/study 를 CLI argument로 넘겨서 이 디렉토리 안에 있는 모든 서브 디렉토리와 파일들을 출력함

이 작업을 수행하는 `printFileList()` 메서드는 재귀적인 방식으로 작성됨

&nbsp;

```java
public class FileExample6 {
    static int found = 0;

    public static void main(String[] args) {
        if (args.length != 2) {
            System.out.println("2 CLI Arguments required.");
            System.exit(0);
        }

        File dir = new File(args[0]);
        String keyword = args[1];

        if (!dir.exists() || !dir.isDirectory()) {
            System.out.println("Invalid directory");
            System.exit(0);
        }

        try {
            findKeywordInFiles(dir, keyword);
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.printf("Requested keyword \"%s\" was found in %d lines", keyword, found);
    }

    public static void findKeywordInFiles(File dir, String keyword) throws IOException {
        File[] files = dir.listFiles();
        String validExtensions = "java txt bak";

        for (File f : files) {
            if (f.isDirectory()) {
                findKeywordInFiles(f, keyword);
            } else {
                String fileName = f.getName();
                String extension = fileName.substring(fileName.lastIndexOf(".") + 1);

                if (!validExtensions.contains(extension)) continue;

                fileName = dir.getAbsolutePath() + File.separator + fileName;

                FileReader fr = new FileReader(f);
                BufferedReader br = new BufferedReader(fr);

                String data = "";
                int lineNum = 0;

                while ((data = br.readLine()) != null) {
                    lineNum++;

                    if (data.contains(keyword)) {
                        found++;
                        System.out.printf("(Keyword Detected) Line %d of %s: %s%n", lineNum, fileName, data.trim());
                    }
                }
            }
        }
    }
}
```

실행 결과)

(Detected) Line 15 of /Users/hyunjun/IdeaProjects/study/src/FileExample5.java: System.exit(0);
(Detected) Line 9 of /Users/hyunjun/IdeaProjects/study/src/FileExample6.java: System.exit(0);
(Detected) Line 17 of /Users/hyunjun/IdeaProjects/study/src/FileExample6.java: System.exit(0);
Requested keyword "exit" was found in 3 lines

CLI argument로 /Users/hyunjun/IdeaProjects/study 경로와 exit 이라는 키워드를 넘겼음

이렇게 경로와 키워드를 받아 해당 경로 내의 모든 디렉토리와 파일을 뒤져 파일 내에 포착되는 키워드의 위치(라인 넘버)를 출력함

&nbsp;

```java
public class FileExample8 {
    static int deletedFiles = 0;

    public static void main(String[] args) {
        if (args.length != 1) {
            System.out.println("1 CLI argument (file extension) is required.");
            System.exit(0);
        }

        String currentDir = System.getProperty("user.dir");

        File dir = new File(currentDir);
        String extension = "." + args[0];

        delete(dir, extension);
        if (deletedFiles == 1) {
            System.out.println(deletedFiles + " file was deleted.");
        } else {
            System.out.println(deletedFiles + " files were deleted.");
        }
    }

    public static void delete(File dir, String extension) {
        File[] files = dir.listFiles();

        for (File f : files) {
            if (f.isDirectory()) {
                delete(f, extension);
            } else {
                String fileName = f.getName();

                if (fileName.endsWith(extension)) {
                    System.out.print(fileName);
                    if (f.delete()) {
                        System.out.println(" --- DELETED");
                        deletedFiles++;
                    } else {
                        System.out.println(" --- FAILED TO DELETE");
                    }
                }
            }
        }
    }
}
```

실행 결과)

dummy1.txt --- DELETED
dummy2.txt --- DELETED
2 files were deleted.

(워킹 디렉토리에 미리 2개의 txt 파일들을 생성해놓았음)

CLI argument로 "txt"를 넘겨 디렉토리를 재귀적으로 탐색하고, 확장자가 txt인 파일에 `delete()`을 호출해 제거하도록 함.

&nbsp;

```java
public class FileExample10 {
    public static void main(String[] args) {
        if (args.length < 2) {
            System.out.println("2 CLI arguments required: file name, fraction size(kB)");
            System.exit(0);
        }

        final int VOLUME = Integer.parseInt(args[1]) * 1000;

        try {
            String[] fileNameAndExt = args[0].split("\\.");
            String fileName = fileNameAndExt[0];
            String extension = fileNameAndExt[1];
            FileInputStream fis = new FileInputStream(args[0]);
            BufferedInputStream bis = new BufferedInputStream(fis); // 기본 사이즈(8192kb)의 버퍼를 가짐

            FileOutputStream fos = null;
            BufferedOutputStream bos = null;

            int data = 0;
            int i = 0;
            int fractionNum = 0;

            while ((data = bis.read()) != -1) {
                if (i % VOLUME == 0) { // 파일을 자를 크기만큼 데이터가 채워지면 진입
                    if (i != 0) bos.close(); // 작업하던 스트림은 이제 자를 크기만큼 데이터를 다 채웠다는 뜻이므로 닫아줌.
                    String newFractionFileName = fileName + "_" + ++fractionNum + "." + extension;
                    fos = new FileOutputStream(newFractionFileName);
                    bos = new BufferedOutputStream(fos); // 새 스트림 생성해서 계속 작업
                }
                bos.write(data);
                i++;
            }

            // 더 이상 읽을 데이터가 없어서 while문을 빠져나오면 스트림을 닫아줌
            bis.close();
            bos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

(미리 data.txt에 5000 byte 가량(5018 byte)의 텍스트 데이터를 넣어둠)

CLI argument로 data.txt와 1을 넘기면 1 \* 1000 (`VOLUME`)을 단위로 새 파일을 만들어줌

> data_1.txt ~ data_6.txt (총 6개의 파일)

&nbsp;

```java
public class FileExample11 {
    public static void main(String[] args) {
        if (args.length != 1) {
            System.out.println("1 CLI argument required: name of the merged file");
            System.exit(0);
        }

        String mergedFileName = args[0];

        try {
            File tempFile = File.createTempFile("~mergeTemp", ".tmp");
            tempFile.deleteOnExit(); // 프로세스 종료 시, 임시 파일 삭제

            FileOutputStream fos = new FileOutputStream(tempFile);
            BufferedOutputStream bos = new BufferedOutputStream(fos);

            BufferedInputStream bis = null;

            int number = 1; // 조각 파일 넘버 (1부터 시작)

            File f = new File(mergedFileName + "_" + number + ".txt"); // 조각 파일을 가리킴

            while (f.exists()) {
                f.setReadOnly(); // 작업 중에 파일이 변경되는 것을 방지
                bis = new BufferedInputStream(new FileInputStream(f));

                int data = 0;
                while ((data = bis.read()) != -1) { // 조각 파일의 데이터를 읽어와서
                    bos.write(data); // 임시 파일(합본)에 쓰고
                }

                bis.close(); // 조각 파일에 사용한 입력 스트림은 닫아줌
                f = new File(mergedFileName +  "_" + ++number + ".txt"); // 다음 넘버의 조각 파일을 가리킴. while문 처음으로 돌아가 과정 반복
            }

            bos.close();

            File oldFile = new File(mergedFileName + ".txt");
            if (oldFile.exists()) oldFile.delete(); // 만약 같은 이름의 파일이 이미 있다면 삭제
            tempFile.renameTo(oldFile); // 임시 파일에게 해당 이름 부여
        } catch (IOException e) {}
    }
}
```

직전 예제에서 data.txt를 data_1.txt ~ data_6.txt 총 6개의 파일로 분할한 것을 다시 합치는 과정.

임시 파일을 만드는 이유는, 예기치 못한 종료 시 불완전한 파일이 생성되는 것을 막기 위함.

> 임시 파일에 `deleteOnExit()`이 호출되어 있어 그런 상황이 발생하면 자동으로 삭제됨

임시 파일의 생성 경로는 따로 지정하지 않을 시, `System.getProperty("java.io.tmpdir")`에 명시된 경로.

> `createTempFile()`의 마지막 매개변수에 따로 경로를 지정해줄 수도 있음
