# 📙 Factory Method

## ✅ 학습 내용

### 💎 ****Factory Method Pattern****

✏️ **정의**

- 생성(Creational) 패턴의 하나로, 객체 생성 처리를 서브 클래스로 분리해 처리하도록 캡슐화하는 패턴
- 쉽게 말해 객체 생성을 전담하는 struct/class를 만들어 구체적인 생성과정을 그 안에 구현하는 패턴이다.
- 객체의 생성 코드를 별도의 클래스나 메서드로 분리함으로써 객체 생성의 변화에 대비해는 데 유용하다.
- 특정 기능 구현은 개별 클래스를 통해 제공되는 것이 바람직한 설계이다.

✏️ **역할**

![팩토리 메서드.drawio.png](https://i.imgur.com/ixFQPWE.png)

- Product
    - 팩토리 메서드로 생성될 객체의 공통 인터페이스
    - Creator와 이를 채택한 객체가 생성할 수 있는 모든 객체의 공통 인터페이스를 선언
- ConcreteProduct
    - 구체적으로 객체가 생성되는 클래스
    - Product가 선언한 인터페이스로 만든 실제 객체
- Creator
    - 팩토리 메서드를 갖는 프로토콜
    - 새로운 객체를 반환하는 팩토리 메서드를 선언
    - 여기서 반환하는 객체는 Product 인터페이스를 준수하고 있어야 함
- ConcreteCreator
    - 팩토리 메서드를 구현하는 객체로 ConcreteProduct 객체를 생성

✏️ **예시**

- 화폐를 예를 들어 설명해보겠다.

### **Product**

```swift
protocol CurrencyDescribing {
    var symbol: String { get }
    var code: String { get }
}
```

- 모든 화폐에는 symbol과 문자표기법(code)이 있다.
- 심볼은 ₩, $, € 같은 것을 의미하고 code는 KRW, USD, EUR 같은 것을 의미한다.
- 이를 프로토콜을 통해 만들어질 객체의 필수 조건으로 설정했다.

### Concrete **Product**

```swift
struct Won: CurrencyDescribing {
    let symbol: String = "₩"
    let code: String = "KRW"
}

struct Dollar: CurrencyDescribing {
    let symbol: String = "$"
    let code: String = "USD"
}

struct Euro: CurrencyDescribing {
    let symbol: String = "€"
    let code: String = "EUR"
}
```

- 실제로 만들어야할 Product 객체들을 선언하였다.

### Creator

```swift
protocol CurrencyCreator {
    func makeCurrency(for country: Country) -> CurrencyDescribing
}

enum Country {
		case korea
		case unitedStates
		case spain
}
```

- 팩토리 메서드를 만들기 위한 Creator 프로토콜을 만들었다.
- 나라를 구분하기 위한 열거형도 추가적으로 정의하였다.

### Concrete Creator

```swift
struct CurrencyFactory: CurrencyCreator {
    func makeCurrency(for country: Country) -> CurrencyDescribing {
        switch country {
        case .korea:
            return Won()
        case .unitedStates:
            return Dollar()
        case .spain:
            return Euro()
        }
    }
}

let factory = CurrencyFactory()

let won *= factory.makeCurrency(for: .korea)*
let doller = factory.makeCurrency(for: .unitedStates)
let euro = factory.makeCurrency(for: .spain)
```

- 실제 팩토리 메서드를 실행하는 객체이다.

✏️ **사용 이유**

- 팩토리 패턴은 굳이 이렇게 만들 필요가 있나? 라는 의문이 들기도 한다.
- 하지만 팩토리 패턴은 두가지의 이점이 있다.

### 객체 생성을 대신하는 객체

- 실제 코드에서 객체를 생성하는 일이 많아질수록 이니셜라이저를 많이 호출하게 될 것이다.
- 만약, 그렇게 여러개의 이니셜라이저를 호출한 코드에서 이니셜라이저의 형식을 바꾸게 된다면 이니셜라이저를 호출한 곳 모두의 코드를 변경해주어야 한다.
- 이렇게 객체를 생성하는 이니셜라이저가 많아질수록 이니셜라이저를 변경하거나 사용되는 객체 자체를 변경하는데 부담이 커질 수 있다.
- 하지만 팩토리 메서드를 사용해 객체를 초기화하고 있었다면, 그 팩토리 객체의 메서드만 수정해주면 된다.

### 개발 협업에서의 장점

- 팩토리 메서드에 조건에 따라 객체를 다르게 생성하고 가져오는 일을 팩토리 객체에게 위임함으로써, 실제 Concrete Product를 사용하는 개발자들이 객체를 팩토리를 통해 전달받음으로써 실수를 줄이고 효율을 높힐 수 있다.
- 특정 종류의 기능들에 사용될 수 있는 클래스들의 종류가 많고 복잡할 때 이를 사용하는 개발자 측에서는 이들을 다 알 필요 없이 사용할 객체의 조건들만 인자로 넘겨주면, 이에 적절한 클래스를 찾아 객체로 생성해 넘겨주는 일은 팩토리가 처리하게 하는 것이다.
- 쉽게 생각해 회사에서 자주 사용하는 컴포넌트나 UI가 몇가지의 조건별 디자인으로 존재할 때, 그에 맞는 팩토리 메서드를 만들어 조건만 넘겨주면 그에 맞는 컴포넌트를 반환하게 하여서 개발 효율을 높힐 수 있다.
