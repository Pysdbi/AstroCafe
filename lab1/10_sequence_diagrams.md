# Диаграммы последовательности

## 1. Регистрация участника программы

```plantuml
@startuml
actor "Участник программы" as participant
participant "Система" as system
participant "База данных" as db

participant -> system: Запрос на регистрацию
system -> participant: Запрос email и пароля
participant -> system: Ввод email и пароля
system -> system: Проверка корректности данных
system -> db: Сохранение данных участника
db --> system: Подтверждение сохранения
system --> participant: Уведомление об успешной регистрации
@enduml
```

## 2. Выпуск карты лояльности

```plantuml
@startuml
actor "Участник программы" as participant
participant "Система" as system
participant "База данных" as db

participant -> system: Запрос на выпуск карты
system -> system: Проверка возможности выпуска
system -> db: Создание новой карты
db --> system: Подтверждение создания карты
system --> participant: Уведомление об успешном выпуске карты
@enduml
```

## 3. Пополнение карты

```plantuml
@startuml
actor "Участник программы" as participant
participant "Система" as system
participant "База данных" as db

participant -> system: Запрос на пополнение карты
system -> participant: Запрос суммы пополнения
participant -> system: Ввод суммы
system -> db: Создание операции пополнения
system -> db: Обновление баланса карты
db --> system: Подтверждение обновления
system --> participant: Уведомление об успешном пополнении
@enduml
```

## 4. Получение вознаграждения

```plantuml
@startuml
actor "Участник программы" as participant
participant "Система" as system
participant "База данных" as db

participant -> system: Запрос на получение вознаграждения
system -> db: Получение списка доступных вознаграждений
db --> system: Список вознаграждений
system --> participant: Отображение списка вознаграждений
participant -> system: Выбор вознаграждения
system -> system: Проверка возможности получения
system -> db: Создание вознаграждения
system -> db: Списание фишек с карты
db --> system: Подтверждение операций
system --> participant: Уведомление об успешном получении вознаграждения
@enduml
``` 