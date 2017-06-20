# Java의 날짜와 시간 API

## Java 클래스에 담긴 제도의 역사

### 1582년 10월 4일의 다음날은?

- UTC (Universal Time Coordinated, 세계협정시) 시간대를 기준으로 1582년 10월 4일에 하루를 더한 날짜가 10월 5일인 것을 테스트 한다.

~~~
public class OldJdkDateTest {

    @Test
    public void shouldGetAfterOneDay() {
        TimeZone utc = TimeZone.getTimeZone("UTC");
        Calendar calendar = Calendar.getInstance(utc);
        calendar.set(1582, Calendar.OCTOBER , 4);
        String pattern = "yyyy.MM.dd";
        String theDay = toString(calendar, pattern, utc);
        assertThat(theDay).isEqualTo("1582.10.04");


        calendar.add(Calendar.DATE, 1);
        String nextDay = toString(calendar, pattern, utc);
        assertThat(nextDay).isEqualTo("1582.10.05");
    }

    private String toString(Calendar calendar, String pattern, TimeZone zone) {
        SimpleDateFormat format = new SimpleDateFormat(pattern);
        format.setTimeZone(zone);
        return format.format(calendar.getTime());
    }
}
~~~

- 위 테스트는 실패한다. 1582년 10월 4일의 다음날은 1582년 10월 15일이다.

> 1582년에서 실종된 10일은 그레고리력을 처음 적용하면서 율리우스력에 의해 그동안 누적된 오차를 교정하기 위해서 건너뛴 기간이다.

> 율리우스력은 4년마다 윤년을 두지만, 그레고리력에서는 4년마다 윤년을 두되 매 100번째 해는 윤년이 아니고, 매 400번째 해는 윤년이라는 차이가 있다.

> Calendar.getInstance() 메서드는 java.util.GregorianCalendar 클래스의 인스턴스를 반환한다. GregorianCalendar 클래스는 그레고리력과 율리우스력을 같이 구현하고 있고, setGregorianChange() 메서드로 두 역법의 전환 시점을 지정할 수 있다. 그런데 AD 4년의 3월 1일 이전에는 윤년을 불규칙하게 두었기 때문에 GregorianCalendar 클래스로 구한 날짜는 정확하지는 않다. 이런 설명은 GregorianCalendar 클래스의 API 문서에 나와 있다.

- 역사와 천문학이 복합적으로 담긴 클래스라 할 만하다.


### 서울 1988년 5월 7일 23시의 1시간 후는?

~~~
@Test
public void shouldGetAfterOneHour() {  
    TimeZone seoul = TimeZone.getTimeZone("Asia/Seoul");
    Calendar calendar = Calendar.getInstance(seoul);
    calendar.set(1988, Calendar.MAY , 7, 23, 0);
    String pattern = "yyyy.MM.dd HH:mm";
    String theTime = toString(calendar, pattern, seoul);
    assertThat(theTime).isEqualTo("1988.05.07 23:00");

    calendar.add(Calendar.HOUR_OF_DAY, 1);
    String after1Hour = toString(calendar, pattern, seoul);
    assertThat(after1Hour).isEqualTo("1988.05.08 00:00");
}
~~~

- 실패한다. 1시간 후는 5월 8일 새벽 1시이다.
- 이 시기에 서울에 적용된 서머타임 때문이다.
- Java는 시간대 데이터베이스라는 곳에 이러한 정보들이 저장되어있다.
- Java는 운영채제의 시간대에 의존하지 않고 독립적으로 시간대 데이터를 업데이트한다.


### 서울 1961년 8월 9일 23시 59분의 1분 후는?

~~~
@Test
public void shouldGetAfterOneMinute() {  
    TimeZone seoul = TimeZone.getTimeZone("Asia/Seoul");
    Calendar calendar = Calendar.getInstance(seoul);
    calendar.set(1961, Calendar.AUGUST, 9, 23, 59);
    String pattern = "yyyy.MM.dd HH:mm";
    String theTime = toString(calendar, pattern, seoul);
    assertThat(theTime).isEqualTo("1961.08.09 23:59");

    calendar.add(Calendar.MINUTE, 1);
    String after1Minute = toString(calendar, pattern, seoul);
    assertThat(after1Minute).isEqualTo("1961.08.10 00:00");
}
~~~

- 실패한다. 23시 59분의 1분 후는 0시 30분이다.
- 1961년 8월 10일은 대한민국 표준시가 UTC+8:30에서 현재와 같은 UTC+9:00로 변경된 시점이다.


### 협정세계시(UTC) 2012년 6월 30일 23시 59분 59초의 2초 후는?

~~~
@Test
public void shouldGetAfterTwoSecond() {  
    TimeZone utc = TimeZone.getTimeZone("UTC");
    Calendar calendar = Calendar.getInstance(utc);
    calendar.set(2012, Calendar.JUNE, 30, 23, 59, 59);
    String pattern = "yyyy.MM.dd HH:mm:ss";
    String theTime = toString(calendar, pattern, utc);
    assertThat(theTime).isEqualTo("2012.06.30 23:59:59");

    calendar.add(Calendar.SECOND, 2);
    String afterTwoSeconds = toString(calendar, pattern, utc);
    assertThat(afterTwoSeconds).isEqualTo("2012.07.01 00:00:01");
}
~~~

- 잘 통과한다.
- 2012년 6월 30일은 가장 최근에 '윤초'가 적용된 때이다.
- 윤년이나 서머타임과는 달리 Java에서 윤초가 Calendar 연산에 적용되지 않는다는 것을 보여준다.


---

## Java 8 이전의 날짜/시간 다루는데 문제점

- 날짜와 시간 계산은 생각보다 어렵고 고려해야 할 것도 많으며 깊이 이해하기 위해서는 배경지식도 많이 필요한 영역이다.
- 이 분야의 어려움은 별도로 치더라도 Calendar 클래스와 Date 클래스는 문제가 많다.

### 불변 객체가 아니다 (Not Immutable)

- VO(Value Object)는 완전한 불변 객체일 때 별칭문제, 스레드 불안정성 등의 부작용에서 자유롭고 여러 객체에서 공유되어도 안전하다.
- C#, Python과 달리 Java의 기본 날짜, 시간 클래스는 불변 객체가 아니다.
- Calendar / Date 클래스에서 값을 바꿀 수 있는 set메소드가 존재한다.
- 이 때문에 Calendar / Date 객체가 여러 객체에서 공유되면 한 곳에서 바꾼 값이 다른 곳에 영향을 미치는 부작용이 생길 수 있다.


### int 상수 필드의 남용
- Calendar를 사용한 날짜 연산은 int 상수 필드를 사용한다.

~~~
calendar.add(Calendar.SECOND, 2);
~~~

- Calendar.JUNE과 같이, 전혀 엉뚱한 상수가 들어가도 이를 컴파일 시점에서 확인할 방법이 없다.


### 헷깔리는 월 지정
- 1582년 10월 4일을 지정하는 코드이다.

~~~
calendar.set(1582, Calendar.OCTOBER , 4);
~~~

- Calendar.OCTOBER 값은 실제로는 9 이다.

> 월 값에 대한 실수를 유발한다.

~~~
Calendar calendar = Calendar.getInstance();
calendar.setLenient(false);
calendar.set(1999, 12, 31);
calendar.get(Calendar.MONTH);
~~~

- 13월을 의미하는 12를 넣어도 Calendar.set() 메서드가 오류를 반환하지 않기 때문에 이런 실수를 인지하기 더욱 어렵다.
- calendar.setLenient(false) 메서드를 호출하면 잘못된 월이 지정된 객체에서 IllegalArgumentException을 던져 준다. 
- 그렇게 지정해도 Calendar.set() 메서드가 호출되는 시점이 아니라, Calendar.get() 메서드가 호출될 때 Exception이 발생한다는 점도 주의해야한다


### 일관성 없는 요일 상수

~~~
@Test
@SuppressWarnings("deprecation")
public void shouldGetDayOfWeek() {  
    Calendar calendar = Calendar.getInstance();
    calendar.set(2014, Calendar.JANUARY, 1);

    int dayOfWeek = calendar.get(Calendar.DAY_OF_WEEK);
    assertThat(dayOfWeek).isEqualTo(Calendar.WEDNESDAY);
    assertThat(dayOfWeek).isEqualTo(4);
    Date theDate = calendar.getTime();
    assertThat(theDate.getDay()).isEqualTo(3);
}
~~~

- Calendar.WEDNESDAY 상수는 4이다.
- Date.getDay()로 요일을 구하면 수요일은 3이 된다.

> Date.getDay() 메소드는 deprecated 되었다.


### Date와 Calendar의 불편한 역할 분담

- JDK 1.0 시절에는 Date 클래스가 날짜 연산을 지원하는 유일한 클래스였다.
- JDK 1.1 이후부터 Calendar 클래스가 포함되면서 날짜간의 연산, 국제화 지원 등은 Calendar 클래스에서 주로 담당하고, Date 클래스의 많은 기능이 deprecated 되었다.
- 특정 시간대의 날짜 생성, 년/월/일 같은 날짜 단위의 계산은 Date 클래스만으로는 수행하기 어렵기 때문에 날짜 연산을 위해 Calendar 객체를 생성, Calendar 객체에서 Date 객체를 생성했다.

> 불필요한 중간 객체를 생성하는 형태로, 쓰기 번거롭고, Calendar 클래스 생성비용이 비싼 편이어서 비효율적이기도 하다.

- 날짜와 시간 모두 저장하는 클래스의 이름이 Date 타입이다. (이름만으로 반환 타입을 예측하기 힘들다)


### 오류에 둔감한 시간대 ID 지정

~~~
@Test
public void shouldSetGmtWhenWrongTimeZoneId(){  
    TimeZone zone = TimeZone.getTimeZone("Seoul/Asia");
    assertThat(zone.getID()).isEqualTo("GMT");
}
~~~

- 'Asia/Seoul' 대신 'Seoul/Asia'로 잘 못 지정한 코드이다.
- 오류 발생하지 않고, 'GMT'가 ID인 시간대가 지정된 것 처럼 테스트를 통과한다.


## JSR-310 : 새로운 Java의 날짜 API

- 2014년에 최종 배포되는 JDK 8에는 JSR-310이라는 표준 명세로 날짜와 시간에 대한 새로운 API가 추가되었다.
- Joda-Time 이라는 OSS에 가장 많은 영향을 받았고, 그 밖에 Time and Money 라이브러리나 ICU 등 여러 오픈소스 라이브러리를 참고했다고 한다.

~~~
public class Jsr310Test {  
    @Test  // 예제 1, 2: 1일 후 구하기
    public void shouldGetAfterOneDay() {
        LocalDate theDay = IsoChronology.INSTANCE.date(1582, 10, 4);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd");
        assertThat(theDay.format(formatter)).isEqualTo("1582.10.04");


        LocalDate nextDay = theDay.plusDays(1);
        assertThat(nextDay.format(formatter)).isEqualTo("1582.10.05");
    }


    @Test  // 예제 3, 4: 1시간 후 구하기
    public void shouldGetAfterOneHour() {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime theTime = ZonedDateTime.of(1988, 5, 7, 23, 0, 0, 0, seoul);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
        assertThat(theTime.format(formatter)).isEqualTo("1988.05.07 23:00");
        ZoneRules seoulRules = seoul.getRules();
        assertThat(seoulRules.isDaylightSavings(Instant.from(theTime))).isFalse();

        ZonedDateTime after1Hour = theTime.plusHours(1);
        assertThat(after1Hour.format(formatter)).isEqualTo("1988.05.08 01:00");
        assertThat(seoulRules.isDaylightSavings(Instant.from(after1Hour))).isTrue();
    }


    @Test  // 예제5, 6: 1분 후 구하기
    public void shouldGetAfterOneMinute() {
         ZoneId seoul = ZoneId.of("Asia/Seoul");
         ZonedDateTime theTime = ZonedDateTime.of(1961, 8, 9, 23, 59, 59, 0, seoul);
         DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm");
         assertThat(theTime.format(formatter)).isEqualTo("1961.08.09 23:59");


         ZonedDateTime after1Minute = theTime.plusMinutes(1);
         assertThat(after1Minute.format(formatter)).isEqualTo("1961.08.10 00:30");
    }

    @Test // 예제 7: 2초 후 구하기
    public void shouldGetAfterTwoSecond() {
        ZoneId utc = ZoneId.of("UTC");
        ZonedDateTime theTime = ZonedDateTime.of(2012, 6, 30, 23, 59, 59, 0, utc);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss");
        assertThat(theTime.format(formatter)).isEqualTo("2012.06.30 23:59:59");


        ZonedDateTime after2Seconds = theTime.plusSeconds(2);
        assertThat(after2Seconds.format(formatter)).isEqualTo("2012.07.01 00:00:01");
    }


    @Test // 예제 12: 1999년 12월 31일을 지정하는 코드
    public void shouldGetDate() {
        LocalDate theDay = LocalDate.of(1999, 12, 31);


        assertThat(theDay.getYear()).isEqualTo(1999);
        assertThat(theDay.getMonthValue()).isEqualTo(12);                
        assertThat(theDay.getDayOfMonth()).isEqualTo(31);                
    }

    @Test(expected=DateTimeException.class) // 예제 12: 1999년 12월 31일을 지정하는 코드의 실수
    public void shouldNotAcceptWrongDate() {
        LocalDate.of(1999, 13, 31);
    }


    @Test // 예제 13: 요일 확인하기
    public void shouldGetDayOfWeek() {
        LocalDate theDay = LocalDate.of(2014, 1, 1);


        DayOfWeek dayOfWeek = theDay.getDayOfWeek();
        assertThat(dayOfWeek).isEqualTo(DayOfWeek.WEDNESDAY);
    }
    
    @Test(expected=ZoneRulesException.class) // 예제 14: 잘못 지정한 시간대 ID
    public void shouldThrowExceptionWhenWrongTimeZoneId(){
         ZoneId.of("Seoul/Asia");
    }
}
~~~


### JSR-310의 특징

- LocalDate, ZoneDateTime 등으로 지역 시간과 시간대가 지정된 시간을 구분했다.
- LocalDate와 LocalTime으로 날짜와 시간을 별도의 클래스로 구분할 수도 있다.
- 생성자 대신 of() 메소드 같은 static factory 메소드를 많이 사용한다. DateTimeFormatter.ofPattern(), Instant.from() 등이 그 예이다. 

> static factory 메소드는 가독성 있는 이름을 따로 붙일 수 있고, 생성자와는 달리 한번 생성된 객체를 재활용할 수도 있다.

- plusDays, plusMinutes, plusSeconds 등 단위별 날짜 연산 메서드를 LocalDate, DateTime 클래스에서 지원한다. 즉 불변객체 (메소드가 호출된 객체의 상태를 바꾸지 않고 새로운 객체를 반환) 이다.
- 월의 int값과 명칭이 일치한다. 1월은 int값 1이다.
- 서머타임 기간이면 DateTimeZone.isDaylightSavings() 메서드의 반환값이 true이다..
- 13월과 같이 잘못된 월이 넘어가면 객체 생성시점에서 DateTimeException을 던진다.
- 요일 클래스는 Enum 상수로 제공한다. 잘못 지정하거나 혼동할 여지가 없다.
- 잘못된 시간대 ID 지정에는 ZoneRulesException을 던진다.
- Calendar, Date의 시간 클래스가 밀리초(millisecond) 단위의 정밀성을 가졌던 반면, JSR-310 클래스는 나노초까지 다룰 수 있다.
- 시계의 개념도 도입되어서 현재 시간과 관련된 기능을 테스트할 때도 유용하다.
- Spring Framework 4.0에서 JSR-310을 기본 지원한다. (ZoneDateTime 등의 타입이 Controller 파라미터로 선언되면, 사용자가 입력한 문자열을 날짜 객체로 변환해준다.)


# References
- [Java의 날짜와 시간 API](http://d2.naver.com/helloworld/645609)