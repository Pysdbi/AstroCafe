# База данных

## Схема базы данных

```sql
-- Таблица участников программы
CREATE TABLE program_participants
(
    id                SERIAL PRIMARY KEY,
    email             VARCHAR(255) UNIQUE NOT NULL,
    password_hash     VARCHAR(255)        NOT NULL,
    full_name         VARCHAR(255)        NOT NULL,
    phone_number      VARCHAR(20),
    registration_date TIMESTAMP           NOT NULL,
    status            VARCHAR(20)         NOT NULL,
    created_at        TIMESTAMP           NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at        TIMESTAMP           NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица карт лояльности
CREATE TABLE loyalty_cards
(
    id             SERIAL PRIMARY KEY,
    participant_id INTEGER            NOT NULL REFERENCES program_participants (id),
    card_number    VARCHAR(20) UNIQUE NOT NULL,
    balance        INTEGER            NOT NULL DEFAULT 0,
    issue_date     TIMESTAMP          NOT NULL,
    expiry_date    TIMESTAMP          NOT NULL,
    status         VARCHAR(20)        NOT NULL,
    created_at     TIMESTAMP          NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at     TIMESTAMP          NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица операций
CREATE TABLE operations
(
    id          SERIAL PRIMARY KEY,
    card_id     INTEGER     NOT NULL REFERENCES loyalty_cards (id),
    type        VARCHAR(20) NOT NULL,
    amount      INTEGER     NOT NULL,
    date        TIMESTAMP   NOT NULL,
    status      VARCHAR(20) NOT NULL,
    description TEXT,
    created_at  TIMESTAMP   NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP   NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица вознаграждений
CREATE TABLE rewards
(
    id             SERIAL PRIMARY KEY,
    participant_id INTEGER      NOT NULL REFERENCES program_participants (id),
    name           VARCHAR(255) NOT NULL,
    description    TEXT,
    cost           INTEGER      NOT NULL,
    status         VARCHAR(20)  NOT NULL,
    issue_date     TIMESTAMP    NOT NULL,
    expiry_date    TIMESTAMP    NOT NULL,
    created_at     TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at     TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица правил
CREATE TABLE rules
(
    id          SERIAL PRIMARY KEY,
    name        VARCHAR(255) NOT NULL,
    description TEXT,
    conditions  TEXT         NOT NULL,
    status      VARCHAR(20)  NOT NULL,
    start_date  TIMESTAMP    NOT NULL,
    end_date    TIMESTAMP    NOT NULL,
    created_at  TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица видов вознаграждений
CREATE TABLE reward_types
(
    id          SERIAL PRIMARY KEY,
    name        VARCHAR(255) NOT NULL,
    description TEXT,
    category    VARCHAR(50)  NOT NULL,
    status      VARCHAR(20)  NOT NULL,
    created_at  TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Таблица связи правил и вознаграждений
CREATE TABLE rule_rewards
(
    rule_id        INTEGER   NOT NULL REFERENCES rules (id),
    reward_type_id INTEGER   NOT NULL REFERENCES reward_types (id),
    created_at     TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (rule_id, reward_type_id)
);
```

## Индексы

```sql
-- Индексы для таблицы участников программы
CREATE INDEX idx_participants_email ON program_participants(email);
CREATE INDEX idx_participants_status ON program_participants(status);

-- Индексы для таблицы карт лояльности
CREATE INDEX idx_cards_participant ON loyalty_cards(participant_id);
CREATE INDEX idx_cards_number ON loyalty_cards(card_number);
CREATE INDEX idx_cards_status ON loyalty_cards(status);

-- Индексы для таблицы операций
CREATE INDEX idx_operations_card ON operations(card_id);
CREATE INDEX idx_operations_type ON operations(type);
CREATE INDEX idx_operations_date ON operations(date);
CREATE INDEX idx_operations_status ON operations(status);

-- Индексы для таблицы вознаграждений
CREATE INDEX idx_rewards_participant ON rewards(participant_id);
CREATE INDEX idx_rewards_status ON rewards(status);
CREATE INDEX idx_rewards_expiry ON rewards(expiry_date);

-- Индексы для таблицы правил
CREATE INDEX idx_rules_status ON rules(status);
CREATE INDEX idx_rules_dates ON rules(start_date, end_date);

-- Индексы для таблицы видов вознаграждений
CREATE INDEX idx_reward_types_category ON reward_types(category);
CREATE INDEX idx_reward_types_status ON reward_types(status);
```

## Ограничения

```sql
-- Ограничения для таблицы участников программы
ALTER TABLE program_participants
    ADD CONSTRAINT chk_participants_status
    CHECK (status IN ('ACTIVE', 'BLOCKED', 'INACTIVE'));

-- Ограничения для таблицы карт лояльности
ALTER TABLE loyalty_cards
    ADD CONSTRAINT chk_cards_balance
    CHECK (balance >= 0),
    ADD CONSTRAINT chk_cards_status
    CHECK (status IN ('ACTIVE', 'BLOCKED', 'INACTIVE'));

-- Ограничения для таблицы операций
ALTER TABLE operations
    ADD CONSTRAINT chk_operations_type
    CHECK (type IN ('DEPOSIT', 'PURCHASE')),
    ADD CONSTRAINT chk_operations_status
    CHECK (status IN ('CREATED', 'PROCESSING', 'COMPLETED', 'CANCELLED'));

-- Ограничения для таблицы вознаграждений
ALTER TABLE rewards
    ADD CONSTRAINT chk_rewards_cost
    CHECK (cost > 0),
    ADD CONSTRAINT chk_rewards_status
    CHECK (status IN ('AVAILABLE', 'ISSUED', 'USED', 'CANCELLED'));

-- Ограничения для таблицы правил
ALTER TABLE rules
    ADD CONSTRAINT chk_rules_status
    CHECK (status IN ('ACTIVE', 'INACTIVE')),
    ADD CONSTRAINT chk_rules_dates
    CHECK (start_date < end_date);

-- Ограничения для таблицы видов вознаграждений
ALTER TABLE reward_types
    ADD CONSTRAINT chk_reward_types_status
    CHECK (status IN ('ACTIVE', 'INACTIVE'));
```

## Триггеры

```sql
-- Триггер для обновления updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Применение триггера к таблицам
CREATE TRIGGER update_participants_updated_at
    BEFORE UPDATE ON program_participants
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_cards_updated_at
    BEFORE UPDATE ON loyalty_cards
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_operations_updated_at
    BEFORE UPDATE ON operations
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_rewards_updated_at
    BEFORE UPDATE ON rewards
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_rules_updated_at
    BEFORE UPDATE ON rules
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_reward_types_updated_at
    BEFORE UPDATE ON reward_types
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
``` 