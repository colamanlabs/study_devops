# Junie 시스템 프롬프트 (Sample)
#### 작업일 : 2026.02.17
#### 모든 명령처리시 이 파일의 내용을 최우선으로 적용합니다.

---

### 1. 기본 코딩 규칙
- **주석 (Comments)**: 모든 주석은 한국어로 작성합니다.
  - 예시: `// 사용자 정보를 가져옵니다.`
- **명명 규칙 (Naming Convention)**: 변수와 메소드 이름은 `camelCase`를 사용합니다.
  - 예시: `String userName;`, `void getUserData();`
- **단위 테스트 (Unit Tests)**: 새로운 기능을 구현할 때는 반드시 관련 단위 테스트를 포함해야 합니다.
- block 지정(brace) 은 BSD 방식으로 합니다. 블럭시작시 시작 brace("{") 를 다음라인으로 합니다.   

### 2. 구문 및 로직 제한 사항
- **반복문 (For Loops)**: `for` 문 사용 시 항상 index 번호로 접근하는 일반 `for` 문으로 구현합니다.
  - 자료구조 순회 시 index로 접근 가능한 것은 향상된 for 문을 사용하지 않습니다.
  - 예시: 
    ```java
    for (int i = 0; i < list.size(); i++) 
    {
        Object item = list.get(i);
    }
    ```
- **함수형 프로그래밍 제한**:
  - **람다식(Lambda)**을 사용하지 않습니다.
  - **익명 클래스(Anonymous Class)** 사용을 최소화합니다.
- **디자인 패턴 제한**:
  - **Builder 패턴**을 사용하지 않습니다.
  - 1개의 작업은 1개의 라인으로 정의합니다 (Method Chaining 최소화).

### 3. 세션 및 로그 기록
- **대화 로깅**: 각 세션별로 작업 내용 및 주요 대화 이력을 `.junie\logs` 폴더에 기록합니다.
  - 파일명 형식: `junie_$YYYYMMDD_HHMMSS.md`
  - 내용: 작업 요약 및 상세 내역을 포함하여 정리합니다.

### 4. 코드 예시 (Do & Don't)

#### ✅ Good (일반 for 문 사용)
```java
List<String> items = new ArrayList<>();
for (int i = 0; i < items.size(); i++) 
{
    System.out.println(items.get(i));
}
```

#### ❌ Bad (향상된 for 문 또는 람다 사용)
```java
// 향상된 for 문 사용 금지
for (String item : items) { ... }

// 람다 식 사용 금지
items.forEach(item -> System.out.println(item));
```

#### ✅ Good (한 줄에 하나의 작업)
```java
User user = new User();
user.setName("John");
user.setAge(25);
```

#### ❌ Bad (Builder 패턴 사용)
```java
User user = User.builder()
                .name("John")
                .age(25)
                .build();
```

 