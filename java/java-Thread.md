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
5. 스레드 동기화
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
<p> 즉 join이 선언된 특정 스레드는 millis 시간 만큼의 CPU를 우선적으로 사용하고 다른 스레드는 join이 선언된 스레드가 종료될 때까지 기다리게 된다.</p>

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
><p>모든 스레드는 1부터 10까지의 우선순위를 가진다. 1이 낮고 10이 높은 우선순위이다. 우선순위 기본 값은 5이다. </p>
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

<p>데몬 스레드(daemon thread)는 일반 스레드가 종료될 때 같이 종료되는 스레드를 의미한다.
<p>일반 스레드는 다른 스레드가 종료되어도 자신의 스레드를 계속 실행하지만, 데몬 스레드는 다른 스레드기 종료되면, 실행 중인 자신의 스레드를 종료한다.</p>

<p>데몬스레드 예시로는 Garbage Collector가 있다. GC는 프로그램이 실행되는 동안 메모리 관리를 하지만 주 프로그램이 종료될 때 같이 종료된다는 특성이 있다.</p>
<p>또한 자동 저장 기능이 있는 문서 프로그램에도 활용될 수 있다. 지정한 시간 마다 자동 저장 기능이 작동하지만 메인 프로그램을 종료하면 함께 종료한다.</p>

### 구현 예제

```java

public class MyThread extends Thread{

	public void run() {
		System.out.println((isDaemon() ? "데몬스레드":"일반스레드") + " 실행");
		for(int i=1; i< 10000; i++) {
			System.out.println("Count: " + i);
			try {
				Thread.sleep(1000);
			}catch (Exception e) {}
		}
	}

}

```

```java


public class Code {

	public static void main(String[] args) {

		Thread td1 = new MyThread();
		td1.setDaemon(true);
		td1.start();
		
		try {
			Thread.sleep(10000);
			System.out.println("main Thread end");
		}catch (Exception e) {
			
		}
	}

}


```
실행결과

```
데몬스레드 실행
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
Count: 6
Count: 7
Count: 8
Count: 9
Count: 10
main Thread end

```
<p>setDaemon으로 데몬 스레드를 지정한다. 데몬 스레드는 10000번 반복으로 1초마다 실행하지만, 10초 뒤 메인 스레드가 종료되면서 같이 종료되는 것을 볼 수 있다.</p>
<p>하지만 꼭 메인 스레드가 종료되어야 데몬 스레드가 종료되는 것은 아니다. 즉 **메인 스레드가 종료되어도 다른 스레드가 실행되고 있으면 데몬 스레드는 종료되지 않는다.** </p>
<p>데몬 스레드는 다른 모든 스레드가 종료되어야 같이 종료하는 조건이기 때문이다. </p>

## 5. 스레드 동기화

<p>동기화(synchronized)는 멀티 스레드 환경 속에서 데이터 일관성 문제를 방지한다. 쉽게 말해, 하나의 작업이 완전히 끝나야 다른 작업을 수행하는 것을 의미한다. 반대로 비동기(asynchronous)는 작업 완료 여부와 상관 없이 다른 작업을 수행하는 개념이다.</p>
<p>다음 예제를 통해 동기화가 필요한 이유를 살펴보자.</p>

### 구현 예제

```java

public class Board {
	int view = 0;
	
	public void lookUp() {
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		this.view += 1;
	}
}

```

```java

public class MyThread extends Thread{
	Board board;
	
	public MyThread(Board board) {
		this.board = board;
	}
	
	public void run() {
		board.lookUp();
		System.out.println("조회수: " + board.view);
	}

}

```

```java

public class Code {

	public static void main(String[] args) {
		Board board = new Board();
		
		
		for(int i=0;i<1000;i++) {
			Thread td1 = new MyThread(board);
			td1.start();
		}
		
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		
		System.out.println("최종 조회수: "+board.view);
		
	}

}

```

실행결과

```
...

최종 조회수: 998

```

<p>Board 클래스의 조회수를 증가시키는 스레드를 만들었다. 그리고 1000번 반복하여 조회수를 증가시켰지만 결과는 1000이 아닌 998이 나왔다. </p>
<p>그 이유는 view 필드에 하나의 스레드가 작업 중일 때 다른 스레드가 변경 작업을 수행했기 때문이다. 즉 먼저 작업 중인 스레드가 끝나지 않았기 때문에 값이 변하지 않는 현상이 생긴다.</p>
<p>가벼운 예시로 1000번을 수행했지만, 트래픽이 거대한 사이트의 경우 멀티 스레드의 동기화가 적용되지 않으면 다양한 문제가 생길 것이다.</p>

<p>그렇다면 동기화를 적용해보자. 동기화는 2종류가 있다.</p>

>### 5.1 메서드 동기화

<p>메서드 동기화는 2개 이상의 스레드가 동시에 한 메서드를 실행할 수 없게한다.</p>
<p>동기화하고자 하는 메서드 리턴 타입 앞에 synchronized를 선언하여 적용할 수 있다.</p>

### 구현예제

```java

public class Board {
	int view = 0;
	// 조회수 증가 메서드에 synchronized 추가
	public synchronized void lookUp() {
		
		this.view += 1;
	}
}


```
실행 결과

```
...
조회수: 998
조회수: 999
조회수: 1000
최종 조회수: 1000

```

<p>메서드 동기화를 적용하면 스레드 충돌로 인한 값은 보장할 수 있다. 그러나 하나의 스레드만 실행될 수 있기 때문에 성능이 저하될 수도 있다.</p>

>### 5.2 블록 동기화

<p>블록 동기화는 메서드 전체가 아닌 필요한 객체를 부분적으로 동기화한다.</p>
<p>구현하려면 synchronized() 에 동기화하고자 하는 객체 필드를 넣으면 된다.</p>

### 구현예제

```java

public class Board {
	int view = 0;
	
	public void lookUp() {
		synchronized(this) {
			this.view += 1;
		}
		
	}
}

```
실행결과는 동일하다.

## 동기화 원리
