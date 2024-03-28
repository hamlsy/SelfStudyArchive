# java-Thread

### 목차
1. 스레드 구현
	1. Thread
 	2. Runnable
2. 멀티 스레드
3. 스레드 주 메소드
	1. join()
 	2. sleep()
 	3. set,getPriority()
4. 데몬 스레드
5. 동기화
- - -
<h3>스레드</h3>
<p> 스레드의 사전적 의미는 실행 흐름의 단위이나, 스레드와 Java에서의 스레드는 다르다.</p>
<p> 가장 큰 차이점은 스레드는 CPU에서 관리되나, 자바에서의 스레드는 JVM에 의해 관리된다.</p>

<p> <b> .class 파일 실행 -> JVM에 의해 main 스레드 생성 -> main 스레드 내부에서 다른 스레드 실행 </b> 구조로 이루어진다.</p>
<hr>

### 구현 방법
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
><p>start() 메서드는 스레드를 생성한 뒤 그 스레드 위에서 run()을 실행한다(멀티 스레드). run()은 일반 메서드를 불러와서 실행시킬 뿐이다.</p>


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
<p>실행 결과</p>
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

>### Thread.join()
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
<p> tdA스레드가 실행되고 tdA.join()으로 인해 tdA 스레드가 종료될 때까지 기다리고 tdB를 호출한다. 그 결과 tdA가 실행된 뒤 tdB가 실행되는 결과를 도출할 수 있다.</p>

----

>### Thread.sleep()
><p>Thread.sleep() 메서드는 현재 실행 중인 스레드를 지정된 시간(밀리초 단위) 동안 일시적으로 중지시킨다. 이 메서드를 호출하면 현재 스레드는 지정된 시간 동안 대기 상태가 된다. </p>


<h3>구현 예제</h3>

```java

import java.time.LocalTime;

public class MyThread implements Runnable{
	String order;
	int time;
	public MyThread(String order, int time) {
		this.order = order;
		this.time = time;
	}
	
	@Override
	public void run() {
		
		try {
			System.out.print(order + " thread start in ");
			LocalTime startTime = LocalTime.now();
			System.out.println(startTime.getSecond());
			
			Thread.sleep(time);
			
			System.out.print(order + " thread end at ");
			LocalTime endTime = LocalTime.now();
			System.out.println(endTime.getSecond());
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}


```

```java

public class Code {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MyThread td1 = new MyThread("first", 3000);
		MyThread td2 = new MyThread("second", 1000);
		Thread tdA = new Thread(td1);
		Thread tdB = new Thread(td2);
		
		tdA.run();
		tdB.run();
		
		
	}

}

```

<p>실행 결과</p>
<pre><code>
first thread start in 24
first thread end at 27
second thread start in 27
second thread end at 28
</code></pre>
<p>Runnable은 run()메서드만 사용할 수 있지만 Thread의 sleep()은 static 메서드기 때문에 Runnable 내부에서도 사용이 가능하다. </p>
<p>실행 결과를 살펴보면 지정한 밀리초 만큼 스레드가 일시정지 후 깨어난다.</p>
<p>또한 다른 스레드는 현재 휴면 중인 스레드를 중단할 수 있으나, InterruptedException이 발생한다.</p>

---

>### getPriority(), setPriority(int priority)
><p>모든 스레드는 1부터 10까지의 우선순위를 가진다. 1이 낮고 10이 높은 우선순위이다. 우선순위 기 값은 5이다. </p>
><p>두 가지 작업이 동시에 실행되고 있을 때, 우선순위가 높은 작업이 더 많은 시간을 할당받는다. 같은 작업량을 가지면 우선순위가 높은 스레드가 먼저 끝난다.</p>
><p>getPriority()로 우선순위를 가져오고, setPriority(int priority) 를 통해 스레드의 우선순위를 정할 수 있다.</p>
<h3>구현 예제</h3>

```java

public class PriorityThread extends Thread {
	int cnt;
	String name;
	public PriorityThread(int cnt, String name) {
		this.cnt = cnt;
		this.name = name;
	}
	public void run() {
		for(int i=0; i < cnt; i ++) {
			System.out.println(name + " "+ " Thread run " + i + " time");
			
		}
		
	}
}

```

```java

public class code {

	public static void main(String[] args) {

		Thread td1 = new PriorityThread(10, "MAX P");
		Thread td2 = new PriorityThread(10, "MIN P");
		
		td1.setPriority(10);
		td2.setPriority(1);
		
		td1.start();
		td2.start();
		
	}

}

```

<p>실행 결과</p>
<pre><code>
MAX P  Thread run 0 time
MAX P  Thread run 1 time
MIN P  Thread run 0 time
MAX P  Thread run 2 time
MIN P  Thread run 1 time
MAX P  Thread run 3 time
MIN P  Thread run 2 time
MAX P  Thread run 4 time
MAX P  Thread run 5 time
MAX P  Thread run 6 time
MIN P  Thread run 3 time
MAX P  Thread run 7 time
MIN P  Thread run 4 time
MAX P  Thread run 8 time
MAX P  Thread run 9 time
MIN P  Thread run 5 time
MIN P  Thread run 6 time
MIN P  Thread run 7 time
MIN P  Thread run 8 time
MIN P  Thread run 9 time
</code></pre>
우선순위를 높게 측정한 MAX P 스레드가 더 먼저 실행된다. 결과를 보면 우선순위가 항상 스레드의 실행 순서를 보장하지 않는다.
왜냐하면 스레드는 실행 전 준비 시간을 가지기 때문에, 실행 시간이 짧은 스레드의 경우 결과가 불규칙하게 나올 수 있다.


## 4. 데몬 스레드




