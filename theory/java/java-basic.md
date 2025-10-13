- [[#1.  Primitive 타입|1.  Primitive 타입]]
- [[#2. 접근 제어자|2. 접근 제어자]]

## 1.  Primitive 타입

> [!NOTE]
> 💡 **기본 공식** : n비트 → 표현 범위 = `-2^(n-1)` ~ `2^(n-1) - 1`

> [!TIP]
> 💡 `n-1`의 이유는 **맨 앞자리가 부호 비트(Sign Bit)**  
> 💡 `-1`은 **0을 포함하기 때문**


| 항목       | byte | bit | 계산식            | 결과                                                            |
| -------- | ---- | --- | -------------- | ------------------------------------------------------------- |
| byte     | 1    | 8   | -2⁷ ~ 2⁷-1     | -128 ~ 127                                                    |
| short    | 2    | 16  | -2¹⁵ ~ 2¹⁵-1   | -32,768 ~ 32,767                                              |
| int      | 4    | 32  | -2³¹ ~ 2³¹-1   | -2,147,483,648 ~ 2,147,483,647 (약 21억)                        |
| long     | 8    | 64  | -2⁶³ ~ 2⁶³-1   | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 (약 9경) |
| float    | 4    | 32  | ±3.4 × 10³⁸    | 약 ±3.4E38 (정밀도 약 7자리)                                         |
| double   | 8    | 64  | ±1.7 × 10³⁰⁸   | 약 ±1.7E308 (정밀도 약 15자리)                                       |
| char     | 2    | 16  | 0 ~ 2¹⁶–1      | 0 ~ 65,535                                                    |

> [!CAUTION]
> ⚠️ `1 / 0` → `java.lang.ArithmeticException: / by zero`  
> ⚠️ `float f = 1.0;` 은 **컴파일 에러** → 항상 `float f = 1.0f;` 로 선언해야 함

## 2. 접근 제어자
> [!NOTE]
> 💡 접근 제어자는 **클래스, 변수, 메서드, 생성자**의 접근 범위를 제한한다.

| 제어자           | 같은 클래스 | 같은 패키지 | 상속(자식 클래스) | 다른 패키지 | 주요 사용처                   |
| ------------- | ------ | ------ | ---------- | ------ | ------------------------ |
| **private**   | ✅      | ❌      | ❌          | ❌      | 내부 캡슐화 (멤버 변수, 헬퍼 메서드)   |
| **(default)** | ✅      | ✅      | ❌          | ❌      | 같은 패키지 내 협업용             |
| **protected** | ✅      | ✅      | ✅          | ❌      | 상속 관계 보호 (서브클래스에서 접근 가능) |
| **public**    | ✅      | ✅      | ✅          | ✅      | 외부 공개 (API, 공용 클래스)      |
> [!TIP]
> 💡 `default`는 “아무 제어자도 붙이지 않았을 때”의 상태.  
> 💡 일반적으로 **정보 은닉(Encapsulation)** 원칙상 `private` → `public` 순으로 최소화.

## 3. final

- final class는 상속이 안됨
- final class변수는 선언 또는 생성자를 통해 1회 초기화가 가능
- final method는 메서드 오버라이딩이 되지 않는다
- final이 불변(immutable)과 동일하지 않은 이유
	- 객체의 주소값 변경은 불가능하지만, 객체 내부 상태(필드값)은 수정 가능
```java
final List<String> list = new ArrayList<>();
list.add("A"); // ✅ 내부 데이터 변경 가능
list = new ArrayList<>(); // ❌ 참조 변경 불가
```
- static final은 상수를 정의할 때 사용
```java
public static final int MAX_COUNT = 10;
```
> 클래스 단위로 공유되며 재할당 불가

> [!CAUTION]
> 💡 `final` → 변경 불가 키워드
> 💡 `finally` → 예외 처리 후 반드시 실행되는 블록
> 💡 `finalize()` → GC 직전 한 번 호출되는 메서드 (Java 9 이후 비권장)

## 4. 불변 객체
-  **기본형(Primitive Type)** : 값 복사
- **참조형(Reference Type)** : 주소값 복사
```java
public final class ImmutableClass {
    private final Long a;

    public ImmutableClass(Long a) {
        this.a = a;
    }

    public Long getA() {
        return a;
    }

    public ImmutableClass changeA(Long newA) {
        return new ImmutableClass(newA);
    }
}
```

> [!NOTE]
> 💡 불변 객체는 내부 상태가 절대 변하지 않음
> 💡 변경이 필요할 경우 **새 객체 생성**
> 💡 스레드 안전(Thread-safe)하며 동시성 환경에서 안전하게 공유 가능

## 5. String 클래스
> [!NOTE]
> 💡 **String s = “abc”** 는 문자열 리터럴이므로 **String Constant Pool**(힙 메모리 내)에 저장된다.
> 💡 **String.valueOf(“abc”)** 는 전달된 문자열이 리터럴이라면 **풀의 동일 참조를 반환**하며, 풀은 **힙(Heap) 메모리 영역에 존재**한다.

 ```java
String a = "abc";  
String b = "abc";  
String c = new String("abc");  
String d = String.valueOf("abc");
String e = "1";  
String f = String.valueOf(1);
  
System.out.println("a"+"b" == "ab"); //true
System.out.println(a == b);  // true  : 같은 리터럴 → 같은 풀 참조
System.out.println(a == c);  // false : new로 만든 별도 힙 객체
System.out.println(a == d);  // true  : valueOf(String)은 동일 참조를 반환(복사 안 함)
System.out.println(e == f);  // false : valueOf(int)는 새 문자열 생성(자동 intern 아님)


String x = String.valueOf(new char[]{'a','b','c'}); // 새 String 생성(자동 intern 아님)  
System.out.println(a == x);              // 보통 falseSystem.out.println(a.equals(x));         // true (내용은 동일)  
System.out.println(a == x.intern());     // true (명시적 intern 후에는 동일 참조)

String s = "Hi";  
String concat = s.concat(" there");   
System.out.println(s);  //Hi
System.out.println(concat);  //Hi there 
  
  
System.out.println(10 + 20 + "30");  //3030
System.out.println("10" + 20 + 30); //102030
 ```

## 6. 중첩클래스
> [!NOTE]
> 💡 정적 중첩클래스(static)와 내부 클래스(non-static)으로 크게 구분되며, 내부 클래스, 지역클래스, 익명클래스가 내부클래스(non-static)클래스에 포함된다.
> 💡 지역 클래스의 경우 local 변수와, parameter 변수가 복사 후 final 처리되어 메모리에 저장된다.

- 정적 중첩 클래스
```java
public class NestedOuter {  
    private static int outClassValue = 3;  
    private int outInstantValue =2;  
  
    static class Nested {  
        private int nestedInstanceValue = 1;  
  
        public void print(){  
            //자신 멤버변수 접근가능  
            System.out.println("nestedInstanceValue = " + nestedInstanceValue); 
  
            //static 접근가낭 - private도 가능  
            System.out.println(NestedOuter.outClassValue);  
  
            //바깥 클래스의 인스턴스 멤버에는 접근 불가 (non-static)
            //System.out.println(NestedOuter.outInstantValue);  
        }  
    }  
  
}
```

- 내부 클래스
```java
public class InnerOuter {  
    private static int outClassValue = 3;  
    private int outInstantValue =2;  
  
    class Inner {  
        private int nestedInstanceValue = 1;  
  
        public void print(){  
            //자신 멤버변수 접근가능  
            System.out.println("nestedInstanceValue = " + nestedInstanceValue);  
  
            //static 접근가낭 - private도 가능  
            System.out.println(InnerOuter.outClassValue);  
  
            //non-static -> private접근 가능  
            System.out.println(outInstantValue);  
        }  
    }  
}
```

- 지역 클래스
```java
public class LocalOuter {  
    private int outInstantVar = 3;  
  
    private Printer process(int paramVar) {  
        int localVar = 1; //지역변수는 스택프레임이 종료되는 순간 함께 사라진다.  
        class LocalPrinter implements Printer {  
  
            int value = 0;  
  
            @Override  
            public void print() {  
                System.out.println("value = " + value);  
  
                //인스턴스는 지역변수보다 더오래 살아남는다.  
                System.out.println("localVar = " + localVar);  
                System.out.println("paramVar = " + paramVar);  
  
                System.out.println("outInstantVar = " + outInstantVar);  
            }  
        }  
  
        Printer printer = new LocalPrinter();  
        //여기서 실행하지 않고, process 스택프레임이 사라진 이후에 실행  
        //printer.print();  
        return printer;  
    }  
  
  
    public static void main(String[] args) {  
        LocalOuter localOuter = new LocalOuter();  
        Printer printer = localOuter.process(2);  
        //printer.print()를 나중에 실행한다. process()의 스택프레임이 사라진 이후에 실행  
        printer.print();  
        //localVar, paramVar는 지역클래스 인스턴스생성시 변수를 캡처(복사)하여 인스턴스에 포함  
        //여기서 final로 선언되어 별도 처리 안됨  
  
        System.out.println("필드 확인");  
        Field[] declaredFields = printer.getClass().getDeclaredFields();  
  
        for (Field field : declaredFields) {  
            System.out.println("field = " + field);  
        }  
  
    }  
}
```

- 익명 클래스
```java
public class AnonymousClass {  
  
    public static void main(String[] args) {  
  
        Printer printer = new Printer() {  
  
            @Override  
            public void print() {  
                System.out.println("1");  
            }  
        };  
  
        printer.print();  
    }  
}
```

