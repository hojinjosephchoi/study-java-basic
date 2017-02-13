# Java API

## 문자열 관련 API

### String 클래스 메소드
- char charAt(int index)
- int compareTo(String str)
- String concat(String str)
- boolean equals(String str)
- boolean equalsIgnoreCase(String str)
- int length()
- String replace(char oldChar, char newChar)
- String substring(int offset, int endIndex)
- String toLowerCase()
- String toUpperCase()

### String / StringBuffer / StringBuilder

1. String
	- 문자열 조합시 느림
	- 동기화 지원하지 않음

2. StringBuffer
	- 문자열 조합시 빠름
	- 동기화 지원

3. StringBuilder
	- 문자열 조합시 빠름
	- 동기화 지원하지 않음

~~~
String str = "안녕";
str += "하세요";
// "안녕", "안녕하세요" 두개의 문자열 객체 생성

StringBuffer sb = new StringBuffer("안녕");
sb.append("하세요");
// "안녕하세요" 하나의 객체만 사용하여 속도가 빠름
~~~
---

## 컬렉션 API

자주 사용되는 데이터의 구조를 편리하게 사용할 수 있도록 제공

### List 계열
- 중복허용여부 : 중복 허용
- 값 저장방법 : 순서유지
- 데이터의 저장 공간으로 배열을 사용
- Vector는 Multi-Thread상황에서 동기화 가능, ArrayList는 불가능
- 예 : ArrayList, LinkedList, Vector

#### List 계열 컬렉션 대표적 메소드
- add(E element)
- contains(Object obj)
- get(int index)
- indexOf(Object obj)
- remove(int index)
- set(int index, E element)
- size()


### Set 계열
- 중복허용여부 : 중복 허용 안함
- 값 저장방법 : 내부적인 순서
- 예 : HashSet, TreeSet, LinkedHashSet


### Map 계열
- 중복허용여부 : key데이터 중복허용 안함, value데이터 중복허용
- 값 저장방법 : key & value 쌍
- 예 : HashMap, TreeMap, LinkedHashMap, HashTable

#### Map 계열 컬렉션 대표적 메소드
- containsKey(Object key)
- containsValue(Object value)
- get(Object key)
- keySet()
- put(K key, V value)
- remove(Object key)
- size()

---

## 숫자 관련 API

### Math 클래스
- 숫자관련 유용한 기능들을 모아둔 클래스, 수학계산을 쉽게 할 수 있도록 여러가지 메소드를 제공
- static int abs(int num) : 절대값
- static double acos(double num) : arc cosine을 반환
- static double asin(double num) : arc sine을 반환
- static double atan(double num) : arc tangent를 반환
- static double cos(double num) : cosine을 반환
- static double sin(double num) : sine을 반환
- static double tan(double num) : tangent를 반환
- static double ceil(double num) : 올림 값(ceiling) 반환
- static double floor(double num) : 내림 값(flooring) 반환
- static double exp(double power) : 지정된 power만큼 승 된 값을 반환
- static double pow(double num, double power) : num을 power만큼 누승한 값을 반환
- static double random() : 0.0(포함)과 0.1(미포함) 사이의 난수를 반환한다.
- static double sqrt(double num) : num의 제곱근을 반환한다. num은 양수이어야 한다.

### BigInteger 클래스
- 기본 자료형인 long으로도 표현할 수 없는 정수형 표현

~~~
import java.math.BigInteger;

BigInteger bigValue = new BigInteger("10000000000000000000000000000000000000000000000000000");
~~~

### BigDecimal 클래스
- 실수의 부동소수점 연산으로 인한 오차를 줄임

~~~
double d1 = 1.0;
double d2 = 0.1;
for(int inx = 0; inx < 5; inx++){
	d1 += d2;
	System.out.println(d1);
}

// 출력
// 1.1
// 1.2000000000000002
// 1.3000000000000003
// 1.4000000000000004
// 1.5000000000000004
~~~

~~~
import java.math.BigDecimal;

BigDecimal d1 = new BigDecimal("1.0");
BigDecimal d2 = new BigDecimal("0.1");
for(int inx = 0; inx < 5; inx++){
	d1 = d1.add(d2);
	System.out.println(d1.toString());
}

// 출력
// 1.1
// 1.2
// 1.3
// 1.4
// 1.5
~~~

### Wrapper 클래스
- primitive type의 데이터를 객체로 만들 수 있는 클래스
- collection은 데이터를 객체만 받을 수 있기 때문에 primitive type의 데이터는 Wrapper 객체를 통해 값을 취급해야 한다.

#### Boxing & Unboxing
- Boxing : Primitive Type 데이터 -> Wrapper 객체
- Unboxing : Wrapper 객체 -> Primitive Type 데이터

#### Auto-unboxing
Wrapper 클래스에서 primitive type으로의 자동 변환

~~~
Integer obj = new Integer("55");
int sum = obj + 20;  // auto-unboxing
System.out.println(sum);
~~~