# 8장 C# 3.0

- C# 3.0으로 개발한 응용 프로그램은 닷넷 프레임워크 3.5이상에서 실행

## var 예약어

- 타입추론 기능이 추가되면서 메서드의 지역변수 선언을 타입에 관계없이 `var`예약어로 사용 가능
- C# 컴파일러는 빌드 시점에 타입 결정 (변수의 형식이 고정됨)
- 장점 : 코드 간결 / 단점 : 코드의 가독성 떨어짐

## 자동 구현 속성

- 자동 구현 속성 : C# 컴파일러는 내부 코드 없이 `get`/`set`만 지정된 속성을 빌드 시 확장해서 컴파일
    - `public string Name { get; set; }`
    - OOP의 캡슐화를 따르기 위해 속성 정의 시 필드 대응 작업에 유용
- 독특한 특징이 있다면 `get`/`set`에 서로 다른 접근 제한자를 지정하는 경우 가능
    - `public string Name {get; protected set;}`

## 객체 초기화 (Object Initializer)

- 객체를 생성하는 시점에 내부의 상태를 원하는 값으로 초기화 가능
- C#에서는 `public`접근자가 명시된 멤버 변수를 `new` 구문에서 이름과 값을 지정하는 형태로 초기화하는 구문 지원
    - `Person p1 = new Person(10) { _name = "Anders" };`
        
        → 생성자와 함께 객체 초기화 사용 가능
        
    - `Person p2 = new Person { Name = "Anders", Age = 10};`
        
        → `new`구문에서 중괄호 사용해 공용 속성의 이름과 값을 지정해 초기화 가능
        

## 컬렉션 초기화

- 컬렉션 초기화 구문을 사용하면 `new` 구문과 함께 값을 설정하는 것 가능
    - `List<int> numbers = new List<int> {0,1,2,3,4};`
- C# 컴파일러가 대신 `Add` 메서드를 호출하는 코드를 넣어줌
    
    → 컬렉션 초기화 구문이 적용되려면 해당 타입이 반드시 `ICollection<T>` 인터페이스 구현해야 함 (`ICollection<T>`는 `Add`메서드를 포함하고 있음)
    

## 익명 타입

- 타입에도 이름을 지정하지 않는 방식 지원
- 사용법 : 객체 초기화 구문에서 타입명 제외
    - `var p = new { Count = 10, Title = "Anders" };`
- C# 컴파일러는 컴파일 시점에 임의의 고유 문자열 생성해 타입명 사용 + `new`중괄호 내의 속성 가진 클래스 생성해 어셈블리에 포함
    - `internal sealed class AnonymousType0<T1, T2>`
    - `var p = new AnonymousType0<int ,string>(10, "test");`

## 부분 메서드

- 부분 메서드 : 코드 분할을 하지 않고 단지 메서드의 선언과 구현부를 분리
    - 메서드의 선언 : `partial void Log(object obj);`
    - 메서드의 구현 : `partial void Log(object obj) { ... }`
- 구현부가 정의돼 있지 않아도 컴파일에 영향 X
    
    → 컴파일 시 구현부가 없는 경우 해당 `partial`메서드 호출한 모든 코드는 컴파일 시 지워짐
    
- 분리돼 정의되지만 같은 클래스여야 하기에 반드시 `partial`클래스 내에서만 정의
    - 다른 파일로 분리돼 있어도 반드시 동일한 프로젝트
- 제약 사항
    - 반환값을 가질 수 없음
    - `ref`매개변수는 사용할 수 있지만 `out`매개변수는 사용할 수 없음
    - `private`접근자만 허용됨

## 확장 메서드

- 확장 메서드 : 기존 클래스의 내부 구조를 전혀 바꾸지 않고 마치 새로운 인스턴스 매서드를 정의하는 것처럼 추가
    - `sealed`로 봉인된 클래스는 확장 할 수 없기 때문에 → `System.String` 타입은 `sealed`로 상속 분가
    - 클래스를 상속받아 확장 하면 기존 소스 코드를 새롭게 상속받은 클래스명으로 바꿔야 하기 때문에

```csharp
static class ExtensionMethodSample
{
	public static int GetWordCount(this string txt)
	{
		...
	}
}
```

→ 확장 메서드는 `ststic` 클래스에 정의돼야 함

→ 확장 메서드는 반드시 `static` 이어야 함

→ 확장하려는 타입의 매개변수를 `this` 예약어와 함께 명시

→ 네임 스페이스 하에 정의된다면 확장 메서드를 사용하는 소스코드에서 해당 네임스페이스 `using` 선언

- 장점 : 기존 타입에 이미 정의돼 있던 공용 메서드르 호출하는 것과 같이 사용 가능 → 직관적인 코드 작성
- 한계 :  C# 컴파일러가 빌드 시 추가적인 작업을 해주는 것에 불과
    
    → 결국 `static`메서드 호출을 인스턴스 메서드를 호출하듯이 문법적인 지원해주는 것
    
    - 클래스 상속에서는 가능했던 부모 클래스의 `protected`멤버 호출이나 메서드 재정의 불가

## 람다식

- 람다 대수의 형식을 C#에서 구현한 문법으로 함수의 표현을 다루기 위해 프로그래밍에도 적용
    - 코드로서의 람다식 : 익면 메서드의 간편 표기 용도
    - 데이터로서의 람다식 : 람다식 자체가 데이터가 되어 구문 분석의 대상
- `delegate`, `lambda`
    - 메서드를 인자로 전달하려는 목적으로 탄생
    - `delegate` : 함수에 대한 참조를 보유하는 객체
    - `lambda` : 이름이 없는 함수로 함수를 실행하는 유일한 방법은 대리자가 함수를 가리키는 것

### 코드로서의 람다식

- ⇒ 연산자 사용
- C# 컴파일러는 내부적으로 익명 메서드와 완전히 동일하게 확장해 컴파일
- `;` 을 사용해 여러 줄의 코드를 넣을 수 없음
- 람다식은 델리게이트에 대응
    
    → 자주 사용되는 델리게이트의 형식을 제네릭의 도움으로 일반화해 BCL에 `Action`과 `Func`로 포함
    
    - `public delegate void Action<T>(T obj);` : 반환값이 없는 델리게이트
    - `public delegate TResult Func<TResult>();` : 반환값이 있는 델리게이트
    - 인자를 16개까지 받는 범위 내에서 사용자가 직접 델리게이트를 정의할 필요 없이 `Action`, `Func`을 이용해 람다 식 사용 가능
- 컬렉션 + 람다식
    - `forEach`메서드 : `Action<T>` 델리게이트를 인자로 받아 컬렉션의 모든 요소를 열람하면서 하나씩 `Action<T>`의 인자로 요소 값을 전달 → 요소 수만큼 `Action<T>` 델리게이트 수행
    - `Array ForEach`, `List<T> forEach`
- 지연 평가
    - `IEnumerable<T>`를 반환하는 모든 메서드는 지연된 평가 방식으로 동작
        - `FindAll`의 지연 평가 = `Where`, `ConvertAll`의 지연 평가 = `Select`
        - 코드가 편가만 되고 `foreach`등의 메서드를 호출하지 않는 한 실행되지 않음
    - 장점 : 최초 메서드 호출로 인한 직접적인 성능 손실 발생 X, 실제로 데이터가 필요한 순간에만 코드가 CPU에 의해 실행
    - `Where`메서드 반환 : `IEnumerable<T>`
        - 메서드 실행시 코드를 실행하지 않고 열거자를 통해 요소 순회식 람다식 실행
        
        → 지연된 평가(lazy evaluation)
        
    - `FindAll`메서드 반환 : `List<T>`
        - 메서드 실행이 완료되는 순간 람다식이 모든 요소 대상으로 실행

### 데이터로서의 람다 식

- CPU에 의해 실행되는 코드가 아닌 식을 표현한 데이터로 사용 = 식트리
- 식 트리로 담긴 람다 식은 익명 메서드의 대체물이 아니기에 델리게이트 타입으로 전달되는 것 X 식에 대한 구문 분석 할 수 있는 `Expression`인스턴스
    
    → 람다 식이 코드가 아닌 `Expression`객체의 인스턴스 데이터 역할
    
- 식 트리 : 코드 데이터를 트리 자료구조의 형태로 담고 있음
- 팩터리 메서드를 이용하면 일반적인 메서드 내부의 C# 코드를 `Expression`의 조합마능로도 프로그램 실행 시점에 만들어 내는 것 가능
    - 팩터리 메서드 : 객체지향 디자인 패턴으로 부모 클래스에 알려지지 않은 구체 클래스를 생성하는 패턴으로 자식 클래스가 어떤 객체를 생성할지를 결정하도록 하는 패턴

## LINQ

- LINQ : C# 컴파일러는 자주 사용되는 정보의 선택/열거 작업을 일관된 방법으로 다루기 위해 기존 분법을 확장시킨 것
    - 기존 언어 체계에 쿼리가 통합 → 갖가지 다양한 데이터 원본에 대한 접근법을 단일화
    - `from person in people` = `foreach(var person in people)`
    - `select person;` = `yield return person;`
- 반환 값 = `IEnumberable<T>`
- 대상 = `IEnumerable<T>`타입이거나 그것을 상속한 객체
    - C# 컴파일러는 내부적으로 `IEnumerable<T>`확장 메서드로 변경해 소스코드 빌드 = 표준 쿼리 연산자
- C# 컴파일러에 의해 빌드 시에 원래의 확장 메서드를 사용하는 코드로 변경되어 컴파일
    - 결국 LINQ 쿼리도 간편 표기법
    - `select`예약어 = `Select`확장 메서드 호출하는 또 다른 문법
    - 컬렉션에 대해 LINQ질의를 수행하는 것 = `IEnumerable<T>`의 확장 메서드를 호출하는것
    - `Select` = `Select`
    - `Where` = `Where`
    - `order by ascending`(기본값) = `OrderBy`
    - `order by descending` = `OrderByDescending`
    - `group by` = `GroupBy`
    - `join in on equals` = `Join`
    - `join in on equals into` = `GroupJoin`
- `where` : `IEnumerable<T>` 타입인 컬렉션에서 특정 조건을 만족하는 데이터 필터링 가능
    - 반환 타입이 `bool`형식만 만족하면 됨
- `order by` : `IComparable` 인터페이스가 구현된 타입
- `group by` : 컬렉션의 항목을 주소별로 그룹핑하는 방법
    - `select` 구문과 함께 올 수 없음 → `group by`의 기능이 `select`를 담당하고 있음
    - 개념적으로보면 `group by` == `select by`
- `join` : 2개의 데이터 컬렉션 중에서 `on equals` 조건을 만족하는 요소끼리 묶어 반환
    - `on equals` 조건을 만족하는 모든 레코드를 찾음
    - `on equals` 조건을 만족하는 레코드가 없다면 제외시킴

### 표준 쿼리 연산자(standard query operators)

- 표준 쿼리 연산자 : `IEnumberable<T>`에 정의된 확장 메서드
- LINQ 쿼리에 대응하지 않는 표준 연산자는 `IEnumerable<T>`타입을 대상으로 동작하기에 LINQ 쿼리의 결과와 함께 사용 가능
- 단일 값을 반환하는 메서드 : LINQ 쿼리가 수행되자마자 결괏값이 생성 = 식이 평가
- 표준 쿼리 연산자 가운데 `IEnumerable<T>`, `IOrderedEnumerable<TElement>`를 반환하는 메서드를 제외한 다른 모든 메서드 = LINQ 식이 평가되면서 곧바로 실행
    
    → LINQ 쿼리라고 해서 모두 지연된 연산, 지연 실행 방식으로 동작 X
    
    →  `IEnumerable<T>`, `IOrderedEnumerable<TElement>`반환 메서드 제외하고 모든 메서드 그 즉시 실행되어 실행 결과 반환
    

### 일관된 데이터 조회

- 배열, `List<T>`, `Dictionary<TKey, TValue>` 등 `IEnumerable<T>`타입이거나 상속받은 타입
    - LINQ 코드실행 가능
- 데이터 원본에서도 `IEnumerable<T>`상속받아 정의 → LINQ 쿼리 수행 가능
- LINQ 제공자 : 특정 자료형에 대해 LINQ를 사용할 수 있게 별도로 타입을 정의해둔 것
    - `System.Xml.Linq` - LINQ to XML
- LINQ 기술이 의미있는 이유 = 갖가지 다양한 데이터 원본에 대한 접근법을 단일화했음
