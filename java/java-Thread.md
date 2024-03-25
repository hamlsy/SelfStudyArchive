# java-Thread
<h3>스레드</h3>
<p> 스레드의 사전적 의미는 실행 흐름의 단위이나, 스레드와 Java에서의 스레드는 다르다.</p>
<p> 가장 큰 차이점은 스레드는 CPU에서 관리되나, 자바에서의 스레드는 JVM에 의해 관리된다.</p>

<p> <b> .class 파일 실행 -> JVM에 의해 main 스레드 생성 -> main 스레드 내부에서 다른 스레드 실행 </b> 구조로 이루어진다.</p>
<hr>
<h3>예제</h3>

```java
 public class TestThread extends Thread{
    //스레드 생성
    public void run(){
      System.out.println("Thread");
    }
    public static void main(String[] args){
      TestThread td = new TestThread();
      //스레드 실행
      td.start();
    }
  }
```
<p>스레드 생성을 위해 Thread 클래스를 상속받으면 run() 메서드를 구현해야한다. </p>
<p>.start메서드는 run()메서드를 실행시킨다.</p>

<p>실행결과</p>
<pre><code>Thread</code></pre>

# 멀티 스레드
<p>멀티 스레드란 main 스레드 내부에서 2개 이상의 스레드를 동시에 실행하는 것을 말한다.</p>
<p>따라서 여러 개의 작업을 수행하려면 멀티 스레드 사용이 필수적이다.</p>

<h3>예제</h3>

```java
  public class MyThread extends Thread{
    int num;
    public MyThread(int num){
      this.num = num;
    }
    public void run(){
      System.out.println(this.num + "Thread");
    }
  }

```

```java
  public class TestMain{
    
    public static void main(String[] args){
      for(int i=0; i<5; i++){
        MyThread td = new MyThread(i);
        td.start();
      }
      
    }
  }

```



