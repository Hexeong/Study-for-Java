
>[!note]
>Java에서 `enum`(열거형 타입)은 **서로 연관된 상수들의 집합**을 정의하는 데 사용됩니다. 여러 개의 상수를 하나의 타입으로 묶어서 관리할 수 있으며, **각각의 상수는 고유한 객체로 취급됩니다.** 이를 통해 코드를 더 가독성 있게 만들고, 실수를 줄일 수 있습니다.

---

## `enum` 타입을 사용하는 이유:

1. **코드의 가독성 향상**: 상수들을 그룹화하여 의미를 명확하게 전달하고, 코드의 가독성을 높입니다.
2. **타입 안전성 제공**: 상수를 직접 사용하는 대신 `enum` 타입을 사용하면, 잘못된 값을 사용하는 실수를 방지할 수 있습니다.
3. **보일러플레이트 코드 감소**: 각 상수를 클래스처럼 다룰 수 있어, 각 상수에 관련된 로직을 담을 수 있습니다.

---

### `enum`을 활용해 `else if` 문을 없애는 방법

`enum`을 사용하면 많은 경우에 `else if`나 `switch`문을 깔끔하게 대체할 수 있습니다. `enum`은 메서드를 정의할 수 있기 때문에, 각 상수에 대해 **행동(메서드)** 을 다르게 구현할 수 있습니다. 이를 통해 무분별한 조건문을 없앨 수 있습니다.

### 예시: `enum`으로 `else if` 제거하기

다음은 각 직업에 따라 서로 다른 행동을 수행하는 코드에서 `else if`문을 사용하는 방식과 `enum`을 사용하여 이를 깔끔하게 대체하는 방식입니다.

#### 1. `else if`를 사용하는 방식:

```java
public class Main {
	public static void main(String[] args) {         
		String occupation = "클라이언트";                  
		if (occupation.equals("클라이언트")) {             
			System.out.println("클라이언트 개발을 합니다.");         
		} else if (occupation.equals("서버")) {             
			System.out.println("서버 개발을 합니다.");         
		} else if (occupation.equals("디자인")) {             
			System.out.println("디자인 작업을 합니다.");         
		} else {             
			System.out.println("알 수 없는 직업입니다.");         
		}     
	} 
}
```

#### 2. `enum`을 사용하는 방식:

```java
public enum OccupationType {     
	CLIENT {         
		@Override         
		public void performTask() {             
			System.out.println("클라이언트 개발을 합니다.");         
		}     
	},     
	SERVER {         
		@Override         
		public void performTask() {             
			System.out.println("서버 개발을 합니다.");         
		}     
	},     
	DESIGN {         
		@Override         
		public void performTask() {             
			System.out.println("디자인 작업을 합니다.");         
		}     
	};      
	
	public abstract void performTask(); 
}
```


```java
public class Main {     
	public static void main(String[] args) {         
		OccupationType occupation = OccupationType.CLIENT;         
		occupation.performTask();  // "클라이언트 개발을 합니다." 출력     
	} 
}
````

### **`enum`을 사용한 장점:**

1. **가독성 향상**: 더 이상 `else if` 또는 `switch`로 복잡한 조건을 나열하지 않아도 됩니다. 각 `enum` 상수에 해당하는 동작이 명확하게 정의됩니다.
2. **유지보수 용이**: 새로운 상수를 추가하거나 기존 상수에 대한 동작을 변경할 때, `enum` 내부에만 해당 내용을 추가하면 되므로 유지보수가 쉽습니다.
3. **타입 안전성**: 상수로 관리할 때 실수로 잘못된 문자열을 전달할 위험이 있지만, `enum`을 사용하면 컴파일 타임에 타입 검사를 할 수 있어 실수를 방지할 수 있습니다.

이러한 방식으로 `enum`을 활용하면, 반복적인 조건문을 제거하고 코드의 가독성을 크게 향상시킬 수 있습니다.