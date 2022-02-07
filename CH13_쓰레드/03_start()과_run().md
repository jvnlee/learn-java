# start()와 run()

실컷 `run()` 메서드를 구현해놨더니 실제로는 `start()`을 사용해 쓰레드를 실행시킨다니 왜일까?

`start()`를 호출하면 우선 쓰레드를 위한 개별 호출 스택(Call Stack)을 하나 생성해주고, 자동적으로 `run()`을 호출해서 호출 스택의 첫번째 스택으로 들어가게 함.

> 모든 쓰레드는 독립적인 작업 수행을 위해 자신만의 호출 스택이 필요하기 때문에, 쓰레드를 생성하고 실행하면 새로운 호출 스택이 생겨나서 작업이 이루어진 뒤, 쓰레드가 종료되면서 그 호출 스택도 같이 사라짐.

&nbsp;

고의로 예외를 던지고 `printStackTrace()`로 예외가 발생할 당시의 호출 스택을 출력해보면 위 사실을 확인해볼 수 있음

```java
class ThreadExample {
    public static void main(String[] args) {
        ThreadA t1 = new ThreadA();
        t1.start();
    }
}

class ThreadA extends Thread {
    public void run() {
        throwException();
    }
    public void throwException() {
        try {
            throw new Exception();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

실행 결과)

    java.lang.Exception
        at ThreadA.throwException(TryWithResourcesExample.java:14)
        at ThreadA.run(TryWithResourcesExample.java:10)

실행 결과로 미루어보아, 호출 스택의 변화는 다음과 같음. (도식의 마지막 장면이 `printStackTrace()`가 포착한 호출 스택)

<img src="https://user-images.githubusercontent.com/76623442/152834220-cb226a09-51cb-4e81-bd7c-41d3a99d4d6c.png" width="800" alt="" />

main 쓰레드에서 호출된 `start()`에 의해 `t1` 쓰레드를 위한 새로운 호출 스택이 만들어지고 그 안에서 `run()`의 작업이 이루어짐.

&nbsp;

&nbsp;

`start()` 없이 `run()`을 호출하면 단순히 쓰레드 구현체에 정의된 메서드인 `run()`의 내용을 실행할 뿐, 실제로 쓰레드가 생성되지 않음.

```java
class ThreadExample {
    public static void main(String[] args) {
        ThreadA t1 = new ThreadA();
        t1.run();
    }
}

class ThreadA extends Thread {
    public void run() {
        throwException();
    }
    public void throwException() {
        try {
            throw new Exception();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

실행 결과)

    java.lang.Exception
        at ThreadA.throwException(TryWithResourcesExample.java:14)
        at ThreadA.run(TryWithResourcesExample.java:10)
        at ThreadExample.main(TryWithResourcesExample.java:4)

이 경우에는 호출 스택의 변화 흐름이 다음과 같음,

<img src="https://user-images.githubusercontent.com/76623442/152808899-09bb4256-11cf-4ff9-a0cb-b2412455bacf.png" width="800" alt="" />

보다시피 main 쓰레드 안에서만 작업이 이루어졌을 뿐, 새로운 쓰레드의 호출 스택 안에서 작업이 이루어지지 않았음.

&nbsp;

### main 쓰레드

도식에서 확인했듯, `main` 메서드의 작업을 수행하는 것도 쓰레드이고, main 쓰레드라 부름.

`main` 메서드가 수행을 마치면 프로그램이 종료된다고 배웠지만, 그건 쓰레드를 배우기 전까지 추가적인 쓰레드를 생성해 작업하지 않았기 때문.

사실은 `main` 메서드가 수행을 마쳤더라도, 아직 작업 중인 다른 쓰레드가 있다면 프로그램은 종료되지 않음.

> 작업을 마친 쓰레드는 사라지기 때문에, 프로세스 내의 쓰레드가 하나도 남지 않게 되면 프로그램은 종료됨.
