---
title: "Exception handing (예외처리)"
excerpt: "Exception handing (예외처리)"

categories:
- Java
  tags:
- [exception, Java]

permalink: /java/exception1/

toc: true
toc_sticky: true

date: 2021-03-17
last_modified_at: 2021-03-17
---
# Exception handing (예외처리)

우리는 java를 프로그래밍을 하다보면 많은 Error 사항을 만날 수 있다.  
문법적인 오류, 잘못된 코드작성 불능 등 상황에 따라 다른 Error를 표현한다.

에러의 종류로는

- 컴파일 에러(Compile Error) / Checked exception
- 런타임 에러(Runtime Error) / UnChecked exception
- 논리적 에러(실행은 되지만, 의도와 다르게 동작)

Error 와 Exception

- Error : 프로그램 코드에서 수습 할 수 없는 심각한 오류
- Exception : 프로그램 코드에서 수습 가능한 미약한 오류

이것을 java API에서 찾아 볼 수 있는데 java.lang의 Exception를 보면 여러가지의 exception을 만난다.  
하이라키를 보면 모든 exception들은 Exception을 상위클래스로 상속받고 있다.  
그 위로는 Throwable , Object가 있는데 exception에서 사용하는 method()는  
Throwable와 Object에서 받는다.

```java
int i = 10
int j = 0

public void method(int i,int j){
    int k = i / j;
    System.out.println(i+"나누기 "+j);
}
```

위와 같이 실행하면 i / j의 value 값은 10 과 0 이기 때문에 오류가 발생한다.  
이러한 오류를 예외처리를 통해서 정리 할 수 있는데 방법으로는 trt{}catch{} 와 thowable이 존재한다.

```java
public void method (int i,int j){
		System.out.println("==> avg 시작" );
		try{
			int avg = i / j;
		}catch(ArithmeticException e){
			System.out.println("1. >> ====================");
			System.out.println("j값이 0 인 모양 입니다. 나누기 불가");
			System.out.println("2. >> ====================");
			System.out.println(e);
			System.out.println("3. >> ====================");
			e.printStackTrace();
			System.out.println("4. >> ====================");
		}
		System.out.println("==> avg 끝");
		System.out.println(i+"나누기 "+j);
	}
```

위와 같이 사용할 수 있는데 Error가 발생하는 부분을 try안에 넣어 실행하지 않고 catch를 출력하게 만든다.  
try/catch는 결국 Error가 발생하면 예외처리를 실행하고 순차적으로 계속 실행을 한다.

두번째 방법으로는 throws가 있는데 이것은 오류가 발생하는 곳으로 오류를 넘기는 것이다.  
말그대로 오류가 발생하는 최초의 원인이 되는 곳에서 Exception을 출력하게된다.  
throws는 예외 출력후 프로그램을 실행을 멈추게 된다.

```java
public void method (int i, int j) throws ArithmeticException{
    .......
}
//main
method ( i, j); // 여기서 method의 맞지 않는 arg를 넣어 실행했기 때문에 최초의 원인
```

오류가 발생하면 main의 method로 접근하여 Exception을 출력하게 된다.

예외처리 방법 2가지를 같이 사용해도 상관이 없다.

```java
public void method (int i, int j) throws ArithmeticException{
    .......
}

//main
try{
    	method(i, j);
        }catch(ArithmeticException e){
        //}cathch(Exception e){
        	System.out.println("1. >> ====================");
            System.out.println("j값이 0 인 모양 입니다. 나누기 불가");
            System.out.println("2. >> ====================");
	        System.out.println(e);
        	System.out.println("3. >> ====================");
            e.printStackTrace();
        	System.out.println("4. >> ====================");
        }
```

이렇게 사용하게 되면 thorws를 통해 원인이 되는 main의 method로 와 try / catch 의 처리 방법으로  
try가 아닌 catch를 실행후 순차적으로 마지막까지 프로그램을 실행하게 된다.

※ 여기서 중요한 것이 있는데 catch에 Exception으로 ArithmeticException 을 사용 했는데  
ArithmeticException의 상위클래스로 Exception이 존재하기에 묵시적 형변환이 적용되어  
Exception을 사용해도 오류가 발생하지 않는다.

## Finally

java에서 프로그램 종류 방법으로는 main의 return, System.exit(0) 가 있다.  
이것을 method에 사용시 return은 method 종류 System.exit(0)은 프로그램 종류이다.  
프로그램이 종류가 되는 상황이 아닌 이상 무조건 실행해야 하는 것이 있다면  
예외적으로 Finally를 넣어서 무조건 실행을 하게 만들수 있다.

하나의 코드에서 여러가지의 오류가 발생 할 수 있다.  
그렇다면 어떤 오류를 예외 처리를 해야할까??  
여러가지의 오류를 처리하기 위해서 java는 중복 catch를 지원한다.

```java
try{
    fileReader = new FileReader(str);
    fileReader.read(c,0,1024);
}catch(FileNotFoundException e1){
    System.out.println("e1 : "+e1);
    System.out.println(str+" : File이 없습니다.");
}catch(IOException e2){
    System.out.println("e2 : "+e2);
    System.out.println("read() method에서 Exception 발생");
}catch(Exception e3){
    System.out.println("e3 : "+e3);
    System.out.println("모든 Exception 은 내가 잡느다.");
}finally{
    System.out.println("여기는 fileRead() :: Exception이 발생하던 말던 나는 실행");
}
```

위와 같이 catch에 catch를 추가하여 사용하면 된다. 하지만 여기서도 오류가 발생 하는데  
그것은 하이라키구조를 알고있다면 쉽게 해결 할 수 있다.  
catch는 순차적으로 예외처리를 실행한다. 그런데 만약 첫줄에 Exception으로 FileNotFoundException의  
상위인 Exception이나 다른 예외를 넣게 되면 하이라키 구조에 묵시적형변환의 오류가 발생하기에  
예외는 하이라키 구조대로 하위 == > 상위 로 만들어야 한다. (마지막에 Exception은 꼭 넣어두자)

# 예외 발생시키기

사용하다보면 java에서 발생하는 예외 뿐만 아니라 사용자가 예외를 발생시켜야 하는 경우도 발생하게 된다.  
예를 들어, 남탕에 여자가 들어가게 된다면 오류가 발생하여 예외처리를 해야한다.  
그런데 java 의 JVM은 이러한 오류나 예외가 존재하지 않기에 처리 할 수 없다.  
그래서 우리는 각 상황에 맞는 예외를 발생하여 처리하는 방법을 알아야 한다.

```java
class FindOddException extends Exception{

	public FindOddException(){
		System.out.println("==> FindOddException Default Constructor");
	}
	public FindOddException(String msg){
		super(msg);
		System.out.println("==> 인자가 있는 FindOddException Constructor");
	}
}
```

위와 같이 모든 예외의 상위 클래스인 Exception을 상속하여 사용자예외를 만들 수 있다.

```java
public class FindOddExceptionTest{

	public FindOddExceptionTest(){
	}
    //method
	public void test(int number) throws FindOddException{
		System.out.println(":: "+this.getClass().getName()+" start.. ");
		if( number % 2 == 2){
			System.out.println("입력하신 수 : "+number);
		}else{
			throw new FindOddException();
			// thow를 사용하여 FindOddException은 Exception이라 선언(?)을 한다.
		}
		System.out.println(":: "+this.getClass().getName()+" end..\n");
	}

    //main
	public static void main(String args[]){
		FindOddExceptionTest met = new FindOddExceptionTest();
		try{
			met.test(10);
			met.test(11);
		}catch(FindOddException e){
			System.out.println("\n e : "+e);
			e.printStackTrace();
		}
	}//end main
}//end class
```

적용 방법으로는 예외처리 방법이랑 똑같이 thows를 사용하여 예외가 발생하는 곳으로 던진다.  
발생한 곳에 try / catch가 존재하면 예외처리를 실행하게 된다.

※ 사용자 예외는 javad에 JVM에 존재하지 않기에 실행시기지 않는다.  
 그렇기에 throw를 사용하여 사용자 예외를 인식 할 수 있도록 만들어 줘야한다.
