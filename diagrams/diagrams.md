# Диаграммы — Процесс бронирования

## 1. BPMN — Happy path + 2 исключения

```mermaid
flowchart TD
    A([Пользователь начинает]) --> B[Выбирает комнату и даты]
    B --> C{Комната доступна?}
    C -->|Нет| D[Показать сообщение\nКомната недоступна]
    D --> E([Конец])
    C -->|Да| F[Создать бронирование\nstatus: pending]
    F --> G[Перейти к оплате]
    G --> H{Оплата прошла?}
    H -->|Нет| I[Показать ошибку оплаты]
    I --> J[Отменить бронирование\nstatus: cancelled]
    J --> E
    H -->|Да| K[Подтвердить бронирование\nstatus: confirmed]
    K --> L[Отправить email подтверждение]
    L --> M([Бронирование завершено])
```

## 2. Sequence Diagram — Создание бронирования

```mermaid
sequenceDiagram
    actor User
    participant Frontend
    participant API
    participant Database
    participant EmailService

    User->>Frontend: Выбирает комнату и даты
    Frontend->>API: POST /booking (данные бронирования)
    API->>Database: Проверить доступность комнаты
    Database-->>API: Комната свободна
    API->>Database: Создать запись booking (status: pending)
    Database-->>API: booking_id: 123
    API->>Frontend: 200 OK {bookingid: 123}
    Frontend->>User: Показать форму оплаты
    User->>Frontend: Подтверждает оплату
    Frontend->>API: POST /payment {booking_id: 123}
    API->>Database: Обновить status: confirmed
    Database-->>API: OK
    API->>EmailService: Отправить подтверждение
    EmailService-->>User: Email с деталями бронирования
    API-->>Frontend: 200 OK
    Frontend->>User: Страница успешного бронирования
```

## 3. State Transition — Переходы состояний Booking

```mermaid
stateDiagram-v2
    [*] --> pending : POST /booking создан
    pending --> confirmed : Оплата прошла успешно
    pending --> cancelled : Оплата не прошла / пользователь отменил
    confirmed --> cancelled : Пользователь отменил до checkin
    cancelled --> [*]
    confirmed --> [*] : checkin выполнен
```

## Переходы которые нужно покрыть тестами

| Переход | Почему важно тестировать |
|---|---|
| [*] → pending | Основной happy path, создание бронирования |
| pending → confirmed | Критичный бизнес-переход, подтверждение оплаты |
| pending → cancelled | Негативный сценарий, откат при ошибке оплаты |
| confirmed → cancelled | Отмена подтверждённого бронирования, возврат средств |
| confirmed → [*] | Завершение бронирования после checkin |