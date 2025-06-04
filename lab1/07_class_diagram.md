# Диаграмма классов

```mermaid
classDiagram
    class ProgramParticipant {
        -String id
        -String email
        -String password
        -String fullName
        -String phoneNumber
        -Date registrationDate
        -Status status
        +register()
        +login()
        +updateProfile()
        +requestCard()
        +getCards()
        +getRewards()
        +redeemReward()
    }

    class Card {
        -String id
        -String cardNumber
        -Integer balance
        -Date issueDate
        -Date expiryDate
        -Status status
        +activate()
        +block()
        +getBalance()
        +addTokens()
        +spendTokens()
        +getOperations()
    }

    class Operation {
        -String id
        -OperationType type
        -Integer amount
        -DateTime date
        -Status status
        -String description
        +create()
        +process()
        +cancel()
        +getStatus()
        +updateStatus()
    }

    class Reward {
        -String id
        -String name
        -String description
        -Integer cost
        -Status status
        -Date issueDate
        -Date expiryDate
        +create()
        +issue()
        +use()
        +cancel()
        +checkValidity()
        +getStatus()
    }

    class Rule {
        -String id
        -String name
        -String description
        -String conditions
        -Status status
        -Date startDate
        -Date endDate
        +create()
        +activate()
        +deactivate()
        +checkConditions()
        +apply()
        +getStatus()
    }

    class RewardType {
        -String id
        -String name
        -String description
        -String category
        -Status status
        +create()
        +activate()
        +deactivate()
        +getRules()
        +addRule()
        +removeRule()
    }

    class Status {
        <<enumeration>>
        ACTIVE
        BLOCKED
        INACTIVE
    }

    class OperationType {
        <<enumeration>>
        DEPOSIT
        PURCHASE
    }

    ProgramParticipant "1" -- "1..*" Card : has
    ProgramParticipant "1" -- "0..*" Reward : receives
    Card "1" -- "0..*" Operation : performs
    Rule "1" -- "0..*" Reward : applies to
    RewardType "1" -- "1..*" Rule : has
```

## Описание классов

### ProgramParticipant (Участник программы)
Класс, представляющий участника программы лояльности. Содержит информацию о пользователе и методы для управления его данными.

### Card (Карта)
Класс, представляющий карту лояльности. Содержит информацию о балансе и методы для работы с картой.

### Operation (Операция)
Класс, представляющий операции с картой (пополнение, покупка). Содержит информацию о типе и сумме операции.

### Reward (Вознаграждение)
Класс, представляющий вознаграждение. Содержит информацию о стоимости и статусе вознаграждения.

### Rule (Правило)
Класс, представляющий правило начисления вознаграждений. Содержит условия применения правила.

### RewardType (Вид вознаграждения)
Класс, представляющий вид вознаграждения. Содержит информацию о категории вознаграждения.

### Status (Статус)
Перечисление, определяющее возможные статусы объектов системы.

### OperationType (Тип операции)
Перечисление, определяющее возможные типы операций с картой. 