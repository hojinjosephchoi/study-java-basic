# Java 활용하기

## static
- 별도의 객체 생성 없이 어디에서나 공통으로 사용하고자 하는 필드와 메소드를 표시할 때 사용하는 키워드


### 메모리 공간

#### static area
- static으로 선언된 변수 또는 메소드가 저장되는 공간
- 접근제한자를 public으로 선언할 시에 객체 생성 없이 어디에서나 접근 가능
- static area에 저장되는 모든 변수와 메소드들은 프로그램 로딩시에 모두 메모리에 올라가며 프로그램 종료 시까지 사라지지 않음

#### heap
- new 키워드로 객체 생성 시 인스턴스 변수가 저장되는 공간
- 클래스 로딩 후 stack 메모리 상에 존재하는 메소드와 레퍼런스 변수를 통해 접근

#### stack
- 프로그램의 시작점인 메소드가 호출되는 공간
- 클래스 파일 로딩 시 static을 제외한 것들이 올라오는 공간


### static 사용 시 주의사항
- static은 클래스가 인스턴스화 되기 전에도 호출될 수 있으므로, static이 아닌 메소드를 호출할 수 없음.
- static 메소드에서 일반 필드나 메소드를 사용하기 위해서는 반드시 객체 

---

## annotation
- 코드에 주석처럼 작성하여 특수한 의미를 부여
- 컴파일 타임 혹은 런타임에 해석

> 코드 상의 메타데이터

### annotation 장점
1. 문서화
	- 가장 영향도가 적게 사용할 수 있음
	- annotation이 붙은 소스를 컴파일 시에 수집하여 API 문서화할 수 있음

2. 컴파일 시에 체크
	- 다른 메소드에 의존하고 있다는 것을 가리키는 유용한 방법
	- ex) @Override는 메소드 오버라이딩 시, 실수로 메소드 이름을 다르게 작성하였거나, 인수의 정의가 다른 것을 찾을 수 있다.

3. 코드 분석
	- 컴파일 시에 추가적인 기능을 실행할 수 있음


### annotation 사용방법
- @annotation명으로 클래스, 변수, 메소드 앞에서 사용
- @Override : 부모의 메소드를 재정의 한다고 컴파일러에게 알려줌
- @Deprecated : 더 이상 사용하지 않을 필드나 메소드에게 표시하여 사용하지 않도록 권장
- @SuppressWarnings :  프로그램 상의 치명적인 에러가 아닌, warning과 관련된 어노테이션, 계속 warning이 떠있지 않도록 무시하는 기능 제공

~~~
class SuperClass{
	public void calculateSomething(int param){
		
	}
}

class ChildClass extends SuperClass{
	
	// 인자 타입이 다르기 때문에 컴파일 에러발생
	@Override
	public void calculateSomething(String str){
		
	}
}
~~~


---


## generics
- Java 1.5(5.0) 버전부터 추가된 것으로, 컬렉션 등 집합 클래스에서 사용할 객체를 미리 명시해 주는 것

### Generics의 특징
- 반드시 Object타입을 사용함 (primitive는 wrapper 클래스 사용)
- 컴파일 타임에 논리적인 자료형의 오류에 대한 검증을 수행
- 런타임에 자료형에 안전한 코드를 실행

### Generics 사용이유
- 타입 안정성 제공
- 타입 체크와 형변환을 생략
- 코드 간결화

### Generics 사용방법
- <> 기호를 사용하며, 괄호 안에 데이터타입을 명시
- ArrayList, HashMap과 같은 컬렉션에서 대표적으로 사용

~~~
// Generics를 사용하여 자료형을 명확히 할 수 있다.
ArrayList<String> strList = new ArrayList<String>();
String str = strList.get(0);

// Generics를 사용하지 않을 경우 자료형을 명확히 알 수 없고, 
// 잘못 캐스팅할 경우 Exception (ClassCastException) 발생할 수 있다.
ArrayList objList = new ArrayList();
String str = (String)objList.get(0);
~~~

### Generics 다형성 지원
- 다형성을 지원하기 때문에 `<? Extends Object>`와 같은 구문을 사용할 수 있고, 부모 클래스를 통해 상속 받은 클래스들을 관리할 수 있다.
