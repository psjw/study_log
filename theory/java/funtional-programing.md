- [[#1. Functional Interface|1. Functional Interface]]
- [[#2. 1급함수|2. 1급함수]]

---

## 1. Functional Interface

- `Function<T, R>` : R apply(T t);
	- 입력 T를 받아 R을 반환
- `Supplier<T>` : T get();
	- 입력값 없이 T를 반환
- `Consumer<T>` : void accept(T t);
	- 입력 T를 받아 수행
- `Predicate<T>` : boolean test(T t);
	- 조건 검사 후 참/거짓 반환
- `Comparator<T>` : int compare(T var1, T var2);
	- 두 객체비교, 음수/0/양수 반환

---

## 2. 1급함수

- 변수에 할당
- 파라미터로 사용 가능
- 반환 값으로 사용 가능