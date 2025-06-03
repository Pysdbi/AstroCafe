# API системы

## Базовый URL
```
https://api.loyalty-program.com/v1
```

## Аутентификация
Все запросы должны содержать заголовок `Authorization` с JWT-токеном:
```
Authorization: Bearer <token>
```

## Участники программы

### Регистрация участника
```
POST /participants
```
**Тело запроса:**
```json
{
    "email": "string",
    "password": "string",
    "fullName": "string",
    "phoneNumber": "string"
}
```
**Ответ:**
```json
{
    "id": "integer",
    "email": "string",
    "fullName": "string",
    "phoneNumber": "string",
    "registrationDate": "string",
    "status": "string"
}
```

### Авторизация
```
POST /auth/login
```
**Тело запроса:**
```json
{
    "email": "string",
    "password": "string"
}
```
**Ответ:**
```json
{
    "token": "string",
    "expiresIn": "integer"
}
```

### Получение профиля
```
GET /participants/me
```
**Ответ:**
```json
{
    "id": "integer",
    "email": "string",
    "fullName": "string",
    "phoneNumber": "string",
    "registrationDate": "string",
    "status": "string"
}
```

### Обновление профиля
```
PUT /participants/me
```
**Тело запроса:**
```json
{
    "fullName": "string",
    "phoneNumber": "string"
}
```
**Ответ:**
```json
{
    "id": "integer",
    "email": "string",
    "fullName": "string",
    "phoneNumber": "string",
    "registrationDate": "string",
    "status": "string"
}
```

## Карты лояльности

### Получение списка карт
```
GET /cards
```
**Ответ:**
```json
{
    "items": [
        {
            "id": "integer",
            "cardNumber": "string",
            "balance": "integer",
            "issueDate": "string",
            "expiryDate": "string",
            "status": "string"
        }
    ],
    "total": "integer"
}
```

### Выпуск новой карты
```
POST /cards
```
**Тело запроса:**
```json
{
    "type": "string"
}
```
**Ответ:**
```json
{
    "id": "integer",
    "cardNumber": "string",
    "balance": "integer",
    "issueDate": "string",
    "expiryDate": "string",
    "status": "string"
}
```

### Пополнение карты
```
POST /cards/{cardId}/deposit
```
**Тело запроса:**
```json
{
    "amount": "integer"
}
```
**Ответ:**
```json
{
    "id": "integer",
    "cardNumber": "string",
    "balance": "integer",
    "issueDate": "string",
    "expiryDate": "string",
    "status": "string"
}
```

### Блокировка карты
```
POST /cards/{cardId}/block
```
**Ответ:**
```json
{
    "id": "integer",
    "cardNumber": "string",
    "balance": "integer",
    "issueDate": "string",
    "expiryDate": "string",
    "status": "string"
}
```

## Операции

### Получение истории операций
```
GET /operations
```
**Параметры запроса:**
- cardId (опционально)
- type (опционально)
- startDate (опционально)
- endDate (опционально)
- page
- size

**Ответ:**
```json
{
    "items": [
        {
            "id": "integer",
            "cardId": "integer",
            "type": "string",
            "amount": "integer",
            "date": "string",
            "status": "string",
            "description": "string"
        }
    ],
    "total": "integer"
}
```

### Отмена операции
```
POST /operations/{operationId}/cancel
```
**Ответ:**
```json
{
    "id": "integer",
    "cardId": "integer",
    "type": "string",
    "amount": "integer",
    "date": "string",
    "status": "string",
    "description": "string"
}
```

## Вознаграждения

### Получение списка вознаграждений
```
GET /rewards
```
**Параметры запроса:**
- category (опционально)
- minCost (опционально)
- maxCost (опционально)
- page
- size

**Ответ:**
```json
{
    "items": [
        {
            "id": "integer",
            "name": "string",
            "description": "string",
            "cost": "integer",
            "category": "string",
            "status": "string"
        }
    ],
    "total": "integer"
}
```

### Получение вознаграждения
```
POST /rewards/{rewardId}/issue
```
**Ответ:**
```json
{
    "id": "integer",
    "name": "string",
    "description": "string",
    "cost": "integer",
    "status": "string",
    "issueDate": "string",
    "expiryDate": "string"
}
```

### Отмена вознаграждения
```
POST /rewards/{rewardId}/cancel
```
**Ответ:**
```json
{
    "id": "integer",
    "name": "string",
    "description": "string",
    "cost": "integer",
    "status": "string",
    "issueDate": "string",
    "expiryDate": "string"
}
```

## Коды ошибок

### 400 Bad Request
- Неверный формат данных
- Отсутствуют обязательные поля
- Неверные значения полей

### 401 Unauthorized
- Отсутствует токен
- Неверный токен
- Истекший токен

### 403 Forbidden
- Недостаточно прав
- Заблокированный аккаунт
- Заблокированная карта

### 404 Not Found
- Ресурс не найден
- Карта не найдена
- Вознаграждение не найдено

### 409 Conflict
- Карта уже существует
- Email уже используется
- Операция уже выполнена

### 500 Internal Server Error
- Внутренняя ошибка сервера
- Ошибка базы данных
- Ошибка внешнего сервиса 