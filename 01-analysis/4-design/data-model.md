# Технический дизайн — модель данных

## ER-модель

```mermaid
erDiagram
    CLIENT ||--o{ BOOKING : creates
    SLOT ||--o{ BOOKING : contains
    TRAINING_FORMAT ||--o{ SLOT : classifies
    INSTRUCTOR ||--o{ SLOT : leads
    SLOT ||--|| RENTAL_AVAILABILITY : has

    CLIENT {
      string id
      string phone
      string name
    }
    TRAINING_FORMAT {
      string id
      string name
      enum type
      int capacity_cap
      int duration_min
    }
    INSTRUCTOR {
      string id
      string name
    }
    SLOT {
      string id
      datetime start_at
      enum status
      int total_seats
      int free_seats
      int price_amount
      string currency
      string address
      string cancellation_reason
    }
    RENTAL_AVAILABILITY {
      int free_rental_sets
      int rental_price_amount
    }
    BOOKING {
      string id
      string client_id
      string slot_id
      int seats_count
      int rental_count
      enum status
      int total_amount
      datetime created_at
      datetime cancelled_at
    }
```

## Mutable/read-only границы

| Сущность | Клиент читает | Клиент меняет |
|---|---:|---:|
| Slot | да | нет |
| TrainingFormat | да | нет |
| Instructor | да | нет |
| RentalAvailability | да | нет |
| Booking | да | создаёт/отменяет свою |
| Client | да | только свои контактные данные, если это поддержано API |

## Инварианты

- `seats_count` в MVP: 1–3.
- `rental_count <= seats_count`.
- `rental_count <= slot.free_rental_sets`.
- Запись разрешена только если `slot.status == scheduled` и есть свободные места.
- Backend решает гонки и двойные брони, клиент обрабатывает отказ.
- Отмена скалодромом не удаляет бронь клиента, а меняет статус на `cancelled_by_club` и добавляет причину.
