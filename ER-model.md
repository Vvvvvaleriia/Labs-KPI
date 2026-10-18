```mermaid
erDiagram

    USER ||--o{ RESERVATION : "has"
    TABLE ||--o{ RESERVATION : "has"
    TABLE_TYPE ||--o{ TABLE : "defines"
    USER ||--o{ REVIEW : "writes"

    USER {
        UUID id PK
        VARCHAR_100 name
        VARCHAR_255 email UK
        VARCHAR_20 phone
        VARCHAR_255 password_hash
    }

    TABLE {
        UUID id PK
        INT table_number
        INT seats
        UUID table_type_id FK
    }

    TABLE_TYPE {
    UUID id PK
    VARCHAR_10 name
    TEXT description
    DECIMAL price
    }

    RESERVATION {
        UUID id PK
        UUID user_id FK
        UUID table_id FK
        TIMESTAMP reservation_datetime
        INT guests
    }

    REVIEW {
        UUID id PK
        UUID user_id FK
        INT rating
        TEXT comment
        TIMESTAMP created_at
    }
```
