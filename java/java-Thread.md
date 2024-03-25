# java-Thread
<h3>스레드</h3>
<p> 스레드의 사전적 의미는 실행 흐름의 단위이나, 스레드와 Java에서의 스레드는 다르다.</p>
<p> 가장 큰 차이점은 스레드는 CPU에서 관리되나, 자바에서의 스레드는 JVM에 의해 관리된다.</p>

<p> <b> .class 파일 실행 -> JVM에 의해 main 스레드 생성 -> main 스레드 내부에서 다른 스레드 실행 </b> 구조로 이루어진다.</p>
<hr>

<h2>구현 방법</h2>
<ol>
 <li><b>Thread</b></li>
 <li><b>Runnable</b></li>
</ol>

<h2>1. Thread</h2>
<h3>구현 예제</h3>

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
<p>스레드 생성을 위해 Thread 클래스를 상속받고 run() 메서드를 구현해야한다. </p>
<p>.start메서드는 run()메서드를 실행시킨다.</p>

<p>실행결과</p>
<pre><code>Thread</code></pre>

<h2>멀티 스레드</h2>
<p>멀티 스레드란 main 스레드 내부에서 2개 이상의 스레드를 동시에 실행하는 것을 말한다.</p>
<p>따라서 여러 개의 작업을 수행하려면 멀티 스레드 사용이 필수적이다.</p>

<h3>구현 예제</h3>

```java

public class MyThreadA extends ThreadA {
 public void run() {
  System.out.print('A');
 }
}

```

```java

public class MyThreadB extends Thread{
 public void run() {
  System.out.print("B");
 }
}

```
<p>Main class</p>

```java

public class TestMain {

	public static void main(String[] args) {
		
		for(int i=0;i<10;i++) {
			Thread tdA = new MyThreadA();
			Thread tdB = new MyThreadB();
			td.start();
			tdB.start();
		}
		
	}

}


```
<p>실행 결과</p>
<pre><code>
 BBABAABABB...
</code></pre>
<p>만약 스레드가 실행 순서를 보장한다면 결과는 ABAB...식으로 나와야한다. 그러나 멀티 스레드는 각각 독립적으로 실행되어 이미 start()를 호출했다 하더라도 다른 스레드보다 늦게 실행될 수 있다.</p>
<p>순서를 보장하려면 Thread.class의 join 메서드를 사용할 수 있다. </p>
<p>Thread.join(long millis)메서드를 호출하면 해당 쓰레드 객체에 millis 변수 시간 만큼 CPU사용권을 넘기고, 일시정지 상태가 된다.
즉, join이 선언된 특정 스레드는 millis 시간 만큼의 CPU를 우선적으로 사용하고 다른 스레드는 join이 선언된 스레드가 종료될 때까지 기다리게 된다.</p>

<h3>구현 예제</h3>

```java

 public class TestMain {

	public static void main(String[] args) {
		
		for(int i=0;i<10;i++) {
			try {
				Thread td = new MyThread();
				Thread tdB = new MyThreadB();
				td.start();
				tdB.start();
				
				td.join();
				tdB.join();
			}catch(InterruptedException e) {
				
			}
			
		}
		
	}

}


```

<p>실행 결과</p>
<pre><code>
 ABABABABAB...
</code></pre>

