# 7장 C# 2.0

- C# 2.0으로 개발한 응용 프로그램 = 닷넷 프레임워크 2.0 이상에서 실행

## 제네릭

- 기본 형식으로 컬렉션 객체를 사용하는 경우 박싱/언박싱 문제 발생
- 제네릭 사용  코드 빌드 후 실행 → CLR은 JIT 컴파일 시에 클래스가 타입에 따라 정의될 때 T에 대응되는 타입을 대체해서
- 박싱/언박싱으로 발생하는 비효율적인 힙 메모리 사용문제를 없앰
- 데이터 타입에 따른 코드 중복 문제 해결
- `where` 예약어 제약조건에 대한 문법 형식
    - `where T: ICollection` = 형식 매개변수에 제약 조건 명시
    - `where T : ICollection, IConvertible` = 형식 매개변수에 2개 이상의 제약조건 명시
    - `where K : ICollection where V : IComparable` = 형식 매개변수 2개에 대해 각각 제약조건 명시
- `where`예약어를 사용해 형식 매개변수에 대한 제약 조건을 걸 수 있음
    - `where T : struct` = 반드시 값 형식만
    - `where T : class` = 반드시 참조형식만
    - `where T : new()` = 반드시 매개변수 없는 공용 생성자가 포함되어 있어야 함
    - `where T : U`  = `U` 형식의 인수에 해당하는 타입 or 상속받은 클래스 or 형식 매개변수
- `System.Collections.Generic` : 기존 컬렉션에 대한 제네릭 버전
    
    → 제네릭이 문법적으로 구현되면서 MS는 박싱/언박싱 문제를 해결하는 타입을 2.0 BCL에 추가
    
    → 기존의 컬렉션과 사용방법 동일하지만 박싱/언박싱 문제 해결로 힙 메모리 부담X
    (참조형식을 사용했을때는 기존에도 박싱/언박싱 발생 X → 값 형식에서만 효과 나타남)
    
    - `ArrayList` = `List<T>`
    - `HashTable` = `Dictionary<TKey, TValue>`
    - `SortedList` = `SortedDicionary<TKey, TValue>`
    - `Stack` = `Stack<T>`
    - `Queue` = `Queue<T>`
    
    → 인터페이스 가운데 박싱/언박싱 문제 발생하는 경우도 존재해 제네릭 버전 제공
    
    - `IComparable` = `IComparable<T>`
    - `IComparer` = `IComparer<T>`
    - `IEnumerable` = `IEnumerable<T>`
    - `IEnumerator` = `IEnumberator<T>`
    - `ICollection` = `ICollection<T>`

## ?? 연산자 (`null` 병합 연산자)

- `??` 연산자 : `null`값을 가진 참조형 변수를 손쉽게 처리할 수 있는 연산자
    
    ```csharp
    피연산자1 ?? 피연산자2
    ```
    
    → 피연산자1이 `null`이 아니라면 그 값을 그대로 반환하고 `null`이라면 피연산자2의 값을 반환
    

## default 예약어

- 제네릭의 형식 매개변수로 전달된 경우 코드에서 미리 타입을 알 수 없기에 그에 대응되는 초기값 결정 불가
- 타입을 인자로 받아 `T` 형식에 따라 달라지는 값 →  컴파일러가 자동으로 결정할 수 있도록 임의의 타입을 지정하는 방식

## yield return/break 예약어

- `yield return`/`break`예약어 이용 : 기본의 `IEnumerable` `IEnumerator`인터페이스를 이용해 구현했던 열거기능 쉽게 구현 가능
- `IEnumable`인터페이스 이용 → 자연수의 요소를 필요한 만큼 구할 수 있어 무한 집합 표현 가능
- `IEnumbale` `IEnumerator`조합 : 현재 존재하지 않는 요소를 필요할 때마다 열거 가능
    
    → 구현 코드 번거로워 단점 보완 위해 `yield return`/`break`사용
    
- 메서드 호출 시
    - `yield return` 만나면 값 반환 + 메서드 실행 중지 → 내부적으로 `yield return` 실행 코드 위치 기억
    - 다시 메서드 호출 시 마지막 `yield return` 호출됐던 코드 다음줄부터 실행
    - `yield break` 만나면 열거 끝냄
- C# 컴파일러는 `yield` 문이 사용된 메서들를 컴파일시 `IEnumerable`/`IEnumerator`로 구현한 코드에 대한 간편 표기법에 지나지 않음

## 부분 클래스

- `partial` : 클래스의 소스코드를 2개 이상으로 나눌 수 있음
- 한 파일에 있어도 되고 다른 파일로 나누는 것도 가능
    
    → 반드시 같은 프로젝트에서 컴파일
    
    - C# 컴파일러는 빌드 시에 같은 프로젝트에 있는 `partial`클래스를 하나로 모아 단일 클래스로 빌드

## 익명 메서드

- 익명 메서드 : 이름이 없는 메서드로서 델리케이트에 전달되는 메서드가 일회성으로만 필요할 때 편의상 사용
    - 델리케이트에 사용
    - 변수에 담아 사용
- 내부적으로는 간편 표기법에 불과
- C# 컴파일러는 익명 메서드가 사용되면 빌드 시점에 중복되지 않을 특별한 문자열을 하나 생성해 메서드의 이름으로 사용하고 `delegate`예약어 다음의 코드를 분리해 해당 메서드의 본체로  넣음

## 정적 클래스

- 클래스의 정의에도 static예약어 사용
- 정적 클래스는 오직 정적 멤버만을 내부에 포함가능
- 인스턴스 멤버를 포함할 필요가 없다는 사실 명시 위해 클래스 정의를 static으로 정한것

## nullable 형식

- `System.Nullable<T>` 구조체 : 값 형식에 대해 `null`표현이 가능하게 하는 역할
    
    → 참조 형식은 미정이라는 상태를 `null`로 표현 가능
    
- 부가적으로 C#은 `Nullable<T>` 표기의 축양형으로 값 형식에 `?` 문자 붙이는 표현 지원
    - `Nullable<bool> _getMarried;` = `bool? _getMarried;`
    - `?` 문자를 값 형식에 붙이면 C# 컴파일러는 빌드 시 자동으로 `Nullable<T>`로 바꿔줌
- `HasValue` : 값 할당 여부 `bool return`
- `Value` : 값 존재 시 T 타입 값 반환
