
# 📘 인프런 - 실전! Querydsl


## 📅 2025-07-05 - Querydsl 설정

### 💡 학습 주제

- Spring Boot 프로젝트에 QueryDSL을 설정하고 적용하는 방법 학습

---

### 🧠 주요 개념 요약


| 항목 | 설명 |
|------|------|
| **Q 클래스** | 컴파일 시 생성되는 QueryDSL용 타입 클래스 (예: `QHello`) |
| **build.gradle clean 설정** | `clean` 작업 시 `/src/main/generated` 디렉터리를 자동 삭제 |
| **Q타입 생성 시점** | `build` 또는 `compileJava` 단계에서 자동 생성됨 |
| **Q타입 생성 확인** | `study/querydsl/entity/QHello.java` 파일이 생성되었는지 확인 |




---



### 🧪 실습 코드


#### 📌  1. build.gradle` (Spring Boot 3.x 이상 기준)
1. dependency 추가

```gradle
// QueryDSL 라이브러리 의존성 추가
implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'

// QueryDSL Annotation Processor 설정
annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jakarta"
annotationProcessor "jakarta.annotation:jakarta.annotation-api"
annotationProcessor "jakarta.persistence:jakarta.persistence-api"
```

2. clean 작업 시 Q클래스 디렉터리 삭제 설정

```gradle
clean {
    delete file('src/main/generated')
}
```


---
### 🧾 마무리
- QueryDSL 적용을 위해서는 annotation processor 설정과 빌드 경로 정리가 필수
- Q타입 클래스가 정상적으로 생성되지 않으면 IDE에서 인식 오류가 발생할 수 있으므로 build 작업 확인 필요
- /src/main/generated 경로는 .gitignore에 등록하여 소스 관리 대상에서 제외하는 것이 일반적

---