# java-Thread
<ol>
	<li>
		<ul>스레드 구현
		<li>1.Thread</li>
		<li>2. Runnable</li>
		</ul>
	</li>
	<li>멀티 스레드</li>
	<li>스레드 메소드</li>
	<li>데몬 스레드</li>
</ol>
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

<h2>1.1 Thread</h2>
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

><p>start() 와 run()의 차이: </p>
><p>start() 메서드는 스레드를 생성한 뒤 run()을 실행한다(멀티 스레드). run()은 일반 메서드를 불러와서 실행시킬 뿐이다.</p>


<p>실행결과</p>
<pre><code>Thread</code></pre>

<h2>1.2 Runnable</h2>
<h3>구현 예제</h3>

```java
public class CountThread implements Runnable{
	int num;
	public CountThread(int num) {
		this.num = num;
	}
	public void run() {
		for(int i=0; i<num; i++) {
			System.out.println("Count: "+i);
		}
	}
}


```
```java

public class code {

	public static void main(String[] args) {
		
		Runnable rb = new CountThread(10);
		Thread tdA = new Thread(rb);
		Thread tdB = new Thread(rb);
		
		tdA.start();
		tdB.start();
		
	}

}



```
<p>실행 결</p>
<pre><code>Count: 0
Count: 1
Count: 0
Count: 2
Count: 3
Count: 1
...</code></pre>

<p>Runnable 과 Thread의 가장 큰 차이는 Thread는 extends로 상속받는 방식에 비해 Runnable은 인터페이스를 구현하는 점에 있다.</p>
<p>Thread은 extends 상속 특성 상 다중 상속이 불가능하지만, Runnable은 인터페이스 구현으로 다중 상속이 가능하다는 장점이 있다.</p>
<p> 또한 한번 선언한 Runnable Thread 객체로 여러번 재사용이 가능하므로, 일반적으로 Thread 클래스 보다 Runnable 을 선호한다. </p>
<p>*다만 Runnable은 run() 메서드만 가지고 있고, 필수로 구현해야한다.</p>

<h2>2. 멀티 스레드</h2>
<p>멀티 스레드란 main 스레드 내부에서 2개 이상의 스레드를 동시에 실행하는 것을 말한다.</p>
<p>따라서 여러 개의 작업을 수행하려면 멀티 스레드 사용이 필수적이다.</p>

<h3>구현 예제</h3>

```java

public class MyThreadA extends Thread{
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

public class Code {

	public static void main(String[] args) {
		
		for(int i=0;i<10;i++) {
			Thread tdA = new MyThreadA();
			Thread tdB = new MyThreadB();
			tdA.start();
			tdB.start();
		}
		
	}

}


```
<p>실행 결과</p>
<pre><code>
 BBABAABABB...
</code></pre>
<p> 만약 스레드가 실행 순서를 보장한다면 결과는 ABAB...식으로 나와야한다. 그러나 멀티 스레드는 각각 독립적으로 실행되어 이미 start()를 호출했다 하더라도 다른 스레드보다 늦게 실행될 수 있다.</p>

<h2>3. 스레드 메소드</h2>

><h4><b>join 메서드</b></h4>
><p>순서를 보장하려면 Thread.class의 join 메서드를 사용할 수 있다. </p>

<p> Thread.join(long millis)메서드를 호출하면 해당 쓰레드 객체에 millis 변수 시간 만큼 CPU사용권을 넘기고, 일시정지 상태가 된다.</p>
<p> 즉, join이 선언된 특정 스레드는 millis 시간 만큼의 CPU를 우선적으로 사용하고 다른 스레드는 join이 선언된 스레드가 종료될 때까지 기다리게 된다.</p>

<h3>구현 예제</h3>

```java

 public class Code {

	public static void main(String[] args) {
		
		for(int i=0;i<10;i++) {
			try {
				Thread tdA = new MyThreadA();
				Thread tdB = new MyThreadB();
				tdA.start();
				tdB.start();
				
				tdA.join();
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
<p>두 스레드가 순서를 지키며 실행되고 있는 모습이다.</p>

><h4><b>sleep 메서드</b></h4>
><p>sleep 메소드 설명</p>
<p>추가 설명</p>

<h3>구현 예제</h3>

```java
	
```


