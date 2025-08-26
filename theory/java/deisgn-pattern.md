- [[#1. 템플릿 메소드 패턴|1. 템플릿 메소드 패턴]]
- [[#2. 전략 패턴|2. 전략 패턴]]
- [[#3. 템플릿 콜백 패턴|3. 템플릿 콜백 패턴]]
- [[#4. 프록시 패턴 (Proxy Pattern)|4. 프록시 패턴 (Proxy Pattern)]]
- [[#5. 데코레이터 패턴 (Decorator Pattern)|5. 데코레이터 패턴 (Decorator Pattern)]]

---

## 1. 템플릿 메소드 패턴
- 변하지 않는 부분을 템플릿화하고, 변하는 부분은 상속을 통해 구현
```mermaid
classDiagram
    class AbstractClass {
        template()
        +call1()
        +call2()
    }
    class AClass {
        +call1()
        +call2()
    }
    AbstractClass <|-- AClass
```

---

## 2. 전략 패턴 
- 변하지 않는 부분을 Context에 두고 변하는 부분을 interface에 구현
```mermaid
classDiagram
    class Context {
        +execute()
    }
    class Strategy {
        <<interface>>
        +call()
    }
    class AClass {
        +call()
    }
    class BClass {
        +call()
    }
    Context --> Strategy
    Strategy <|.. AClass
    Strategy <|.. BClass
```

---

## 3. 템플릿 콜백 패턴
- 전략 패턴에서 콜백 인터페이스를 실행시 주입하는 방식
```mermaid
classDiagram
    class Client
    class Context {
        +execute()
    }
    class Callback {
        <<interface>>
        +call()
    }
    class ConcreteCallback {
        +call()
    }

    Client --> Context
    Context --> Callback
    Callback <|.. ConcreteCallback
```
---

## 4. 프록시 패턴 (Proxy Pattern)
- 객체의 접근 제어를 위한 패턴
```mermaid
classDiagram
    class Client
    class Subject {
        <<interface>>
        +operation()
    }
    class Proxy {
        +operation()
    }
    class RealSubject {
        +operation()
    }
    Client --> Subject
    Subject <|.. Proxy
    Subject <|.. RealSubject
```
---

## 5. 데코레이터 패턴 (Decorator Pattern)
- 기존 기능에 새로운 기능을 추가하는 패턴
```mermaid
classDiagram
    class Client
    class Component {
        <<interface>>
        +operation()
    }
    class RealComponent {
        +operation()
    }
    class Decorator {
        +operation()
    }
    Client --> Component
    Component <|.. RealComponent
    Component <|.. Decorator
```
---
