## 정규 표현식이란?

**정규 표현식**`(正規表現式, regular expression, 간단히 regexp 또는 regex, rational expression) 또는 정규식(正規式)`은 특정한 규칙을 가진 문자열의 집합을 표현하는 데 사용하는 **형식 언어**입니다다. `정규 표현식`은 많은 **텍스트 편집기와 프로그래밍 언어**에서 `문자열의 검색과 치환`을 위해 지원하고 있으며, 특히 [펄](https://ko.wikipedia.org/wiki/%ED%8E%84 "펄")과 [Tcl](https://ko.wikipedia.org/wiki/Tcl "Tcl")은 언어 자체에 강력한 정규 표현식을 구현하고 있습니다.

정규 표현식이라는 표현식을 이용하여 **패턴이 매칭하는지 검사** 할 수 있습니다. 예를 들어 어떤 문자열이 A부터 C사이에 글자로 시작하면서 5글자이내의 글자인지를 검사하거나, 해당 문자열이 숫자로만 이루어지고 10글자 이내의 글자인자를 검사할 수 있습니다다. 
정규표현식을 잘 작성하면 해당 문자열이 전화번호형식인지 이메일 형식인지 등을 쉽게 검사할 수 있다. 구글링을 통해 해당 정규표현식은 쉽게 찾을 수 있습니다.

---

## 정규 표현식 메타 문자

#### POSIX 기본 및 확장 표준 문법

문자 클래스, `[` 와 `]` 사이에 포함된 문자 집합 외부에서는 12개의 문자가, 내부에서는 오직 4개의 문자(`\`, `^`, `-`, `]`, 자바은 ""를 포함)만 **특수문자**를 의미합니다. 아래는 POSIX 기본 및 확장 표준의 문법입니다.


| 메타문자   | 기능          | 설명                                                                                                                                                                        |
| ------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `\n`   | 일치하는 n번째 패턴 | - n에 숫자가 오면, 일치하는 패턴들 중 n번째를 선택하며, 여기에서 n은 1에서 9 중 하나가 올 수 있다.<br><br>- n에 문자가 오면, 단어 문자를 의미합니다. `.`을 인식시키려면 `\.`을 써야 합니다.                                                |
| .      | 문자          | 1개의 문자와 일치한다. 단일행 모드에서는 새줄 문자(newline)를 제외한다.<br><br>예제 : `A.B` 패턴은 AAB(일치), A1B(일치), AAB(불일치), AB(불일치) 이다.                                                               |
| ?      | 문자          | 아무 문자도 없거나 하나의 문자가 있는 것을 의미한다.<br><br>예제 : `A?B` 패턴은 AAB(일치), AB(일치), AAA(불일치), AAAB(불일치) 이다.                                                                             |
| *      | 0회 이상       | 0개 이상의 문자를 포함한다. <br><br>예제 : `A*B` 패턴은 AB(일치), AAAAAAAAAAAB(일치), A123B(일치), AAAAAAAAAAAA(불일치) 이다.                                                                        |
| +      | 문자 반복       | `+`앞에있는 문자가 **한번이상 반복됨**을 의미한다.<br><br>예제 : AB+ 패턴은 AB(일치), ABBBB(일치), A(불일치), ABC(불일치) 이다.                                                                               |
| \|     | OR          | **의미**: **OR** 조건을 의미합니다. 둘 중 하나가 일치하면 됩니다.<br><br>**예시**: `"a\|b"`는 **a** 또는 **b**와 일치합니다.                                                                               |
| `[^]`  | NOT         | 문자 클래스 안의 문자를 제외한 나머지를 선택한다. 예를 들면 `[^abc]d`는 ad, bd, cd는 포함하지 않고 ed, fd 등을 포함한다. `[^a-z]`는 알파벳 소문자로 시작하지 않는 모든 문자를 의미한다.                                                 |
| ^      | 처음          | 문자열이나 행의 처음을 의미한다.<br><br>**예시**: `"^hello"`는 문자열이 **hello**로 시작해야 일치합니다                                                                                                  |
| $      | 끝           | 문자열이나 행의 끝을 의미한다.<br><br>**예시**: `"world$"`는 문자열이 **world**로 끝나야 일치합니다                                                                                                    |
| [ ]    | 문자 클래스      | `[`과 `]` 사이의 문자 중 하나를 선택한다. `¦`를 여러 개 쓴 것과 같은 의미이다. 예를 들면 `[abc]d`는 ad, bd, cd를 뜻한다. 또한, `-` 기호와 함께 쓰면 범위를 지정할 수 있다. `[a-z]`는 a부터 z까지 중 하나, `[1-9]`는 1부터 9까지 중의 하나를 의미한다. |
| {m, n} | m회 이상 n회 이하 | `a{1,3}b`는 ab, aab, aaab를 포함하지만, b나 aaaab는 포함하지 않는다.                                                                                                                      |
| ( )    | 하위식         | 여러 식을 하나로 묶을 수 있다. `abc¦adc`와 `a(b¦d)c`는 같은 의미를 가진다.                                                                                                                      |


많은 프로그래밍 언어에서는 이를 확장한 문법을 가지고 있습니다.

---

## Pattern, Matcher 클래스 in Java

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExpExample {
    public static void main(String[] args) {
        // 정규 표현식 패턴
        String regex = "\\d+";  // 숫자 하나 이상
        String input = "My phone number is 1234 and my zip is 5678.";
        
        // 패턴 컴파일
        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(input);
        
        // 입력 문자열이 정규 표현식과 일치하는지 검사하는 if 문
        if (matcher.matches()) {
            System.out.println("입력 문자열이 정규 표현식에 맞습니다.");
        } else {
            System.out.println("입력 문자열이 정규 표현식에 맞지 않습니다.");
        }
        
        // 패턴과 일치하는 부분을 찾기
        while (matcher.find()) {
            System.out.println("Found: " + matcher.group());
        }
    }
}

// 출력
// 입력 문자열이 정규 표현식에 맞습니다.
// Found: 1234
// Found: 5678

```

---

## Regexp와 enum을 사용한 객체 분류

`switch ~ case`문이나 `if, else if, else`로 굉장히 길어진 코드는 반복성이 늘어나, 새로운 케이스가 늘어날 때마다, 동일한 구문을 추가해야 합니다. 때문에 `case`나 `else if`로 코드를 만들 경우 생산성이 떨어지게 되기 때문에, 그에 대한 해결책으로 아래와 같이 정규표현식과 enum을 통해 객체 분류가 존재합니다.

해당 코드의 경우, 추가 타입이 생길 때 구문을 추가할 필요 없이, `ObjectType` `enum` 클래스에 타입을 추가하면 됩니다. 

```java
import java.util.regex.Pattern;

public class ObjectClassifier {

    public enum ObjectType {
        EMAIL(Pattern.compile("^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+$")),
        PHONE(Pattern.compile("^\\d{3}-\\d{3,4}-\\d{4}$")),
        URL(Pattern.compile("^(http|https)://[\\w.-]+\\.[a-z]{2,6}(/\\S*)?$")),
        UNKNOWN(Pattern.compile(""));
        // 추가 타입이 필요할 경우 이 부분만 추가하면 됩니다.

        private final Pattern pattern;

        ObjectType(Pattern pattern) {
            this.pattern = pattern;
        }

        // 해당 정규 표현식과 입력 문자열이 일치하는지 검사하는 메소드
        public boolean matches(String input) {
            return pattern.matcher(input).matches();
        }

        // 입력 문자열이 어떤 객체 타입인지 판별하는 메소드
        public static ObjectType classify(String input) {
            for (ObjectType type : ObjectType.values()) {
                if (type.matches(input)) {
                    return type;
                }
            }
            return UNKNOWN;
        }
    }

    public static void main(String[] args) {
        String[] inputs = {
	        "test@example.com", 
	        "010-1234-5678", 
	        "https://example.com", 
	        "invalid"
		};
		
        for (String input : inputs) {
            ObjectType type = ObjectType.classify(input);
            System.out.println("Input: " + input + " is classified as: " + type);
        }
    }
}

// 출력 결과
// Input: test@example.com is classified as: EMAIL
// Input: 010-1234-5678 is classified as: PHONE
// Input: https://example.com is classified as: URL
// Input: invalid is classified as: UNKNOWN
```