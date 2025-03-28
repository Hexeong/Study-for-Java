
>[!note]
>Java의 `record`는 데이터 중심의 클래스에서 자주 사용되는 `보일러플레이트 코드`를 줄이기 위해 도입된 기능입니다. 주로 **값을 담는 불변 객체**를 쉽게 만들기 위한 목적으로 나온 것이죠. Java 14에서 처음 소개되었고, Java 16에서 정식으로 도입되었습니다.

---
## 주요 목적:

1. **보일러플레이트 코드 감소**: 기존의 데이터 클래스에서는 필드, 생성자, `getter`, `equals()`, `hashCode()`, `toString()` 등의 메서드를 일일이 작성해야 했습니다. `record`를 사용하면 이러한 코드를 자동으로 생성해주기 때문에 간결하게 코드를 작성할 수 있습니다.
    
2. **불변성**: `record`는 기본적으로 **불변 객체**를 생성합니다. 필드는 `final`로 선언되며, 생성 이후에는 수정할 수 없습니다. 따라서 `데이터의 일관성`을 유지하는 데 유리합니다.
    
3. **데이터 중심 클래스**: `record`는 주로 데이터를 저장하고 전달하기 위한 객체를 만드는 데 초점을 맞추고 있습니다. 특히 `DTO(Data Transfer Object)`나 `VO(Value Object)`와 같은 패턴을 사용할 때 적합합니다.

---

## `record`의 기본적인 사용 예시:

```java
public record Person(String name, int age) { }
```

위 예시에서 `Person` 클래스는 자동으로 `name`과 `age` 필드에 대한 생성자, `getter`, `equals()`, `hashCode()`, `toString()` 메서드를 생성합니다.

### 장점:

- **코드 간결성**: 데이터를 담는 클래스를 매우 간단하게 정의할 수 있습니다.
- **불변성**: 데이터 변경을 허용하지 않아, 무결성 보장.
- **자동으로 생성되는 메서드들**: `equals()`, `hashCode()`, `toString()` 등의 메서드가 자동으로 생성되어, 개발자가 일일이 작성할 필요가 없습니다.