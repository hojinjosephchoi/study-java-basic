# Thread

## Thread의 개념

### Task
- 일 혹은 작업. 프로세스와 Thread까지 포함한 개념
- 웹 브라우저, 워드 같은 프로그램부터계산기의 덧셈과 같이 작은 연산 작업까지 포함
- 세부적으로는 프로세스와 Thread로 구분

### Process
- OS로부터 자원을 할당 받아 동작하는 독립된 프로그램
- 웹 브라우저를 실행하면 OS에서는 CPU나 메모리 등의 자원을 할당하여 여러가지 일을 수행
- 명령어를 실행하면 OS로부터 자원을 할당받아 실행되는 JVM이 일종의 프로세스라 할 수 있음

### Thread
- 하나의 프로세스에서 실행하는 작업의 단위

### Thread 생명주기

1. New: Thread 객체를 생성했을 때
2. Runnable: Thread 객체의 start()메소드를 호출했을 때 
3. Running: Thread 객체가 실제로 동작하는 단계
4. Waiting/Blocked: Thread가 대기하고 있는 상태 (Running -> Waiting/Blocked -> Runnable)
5. Terminated: Thread 객체의 run()메소드가 그 역할을 다하여 Thread 객체가 종료된 상태

---


## Thread 구현

### Thread 클래스 상속 + run() 메소드 오버라이딩

~~~
class MyThread extends Thread{
	public void run(){
	}
}

class Test{
	public static void main(String[] args){
		MyThread t1 = new MyThread();
		t1.start();
	}
}
~~~
- java에서는 단일 상속만을 지원, 다른 클래스 상속한 경우 Thread 클래스 상속 불가


### Runnable 인터페이스 구현

~~~
class MyThread implements Runnable{
	public void run(){
	}
}

class Test{
	public static void main(String[] args){
		Thread t2 = new Thread(new MyThread());
		t2.start();
	}
}
~~~


### Thread API

- interrupt() : sleep(), join()에 의해 일시정지된 쓰레드를 실행 대기 상태로 변경
- join() : 지정된 시간 동안 쓰레드가 실행되도록 함
- sleep(long millis) : 일정 시간동안 쓰레드를 일시 정지시켰다가 시간이 지난 후 실행 대기 상태로 변경
- stop() : 쓰레드 즉시 종료
- yield() : 실행 중에 다른 쓰레드에게 양보하고 실행 대기 상태로 변경
- currentThread() : 현재 실행중인 쓰레드 객체 리턴 

---


## Thread 동기화 

### 동기화
- 하나의 Thread가 자신의 작업이 종료될 때까지 다른 Thread의 접근을 제한
- synchronized구문, Thread lock 필

### 메소드 자체 동기화

~~~
접근제어자 synchronized 리턴타입 메소드명(파라미터){
	//메소드 작성
}
~~~

### 메소드 내부에 동기화 블록 지정

~~~
메소드 선언{
	//메소드 내용
	synchronized(락_객체){
		//공유자원 변경
	}
}
~~~