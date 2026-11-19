# S4: Порожній запит

```mermaid
sequenceDiagram
    actor U as Шукач книги
    participant UI as Інтерфейс пошуку
    participant SS as Сервіс пошуку

    U->>+UI: submitQuery(query)
    UI->>UI: validateQuery(query)
    alt Запит порожній або лише з пробілів
        UI->>U: showMessage("Заповніть запит")
        Note right of UI: Сервіс пошуку не викликається, результати не показуються
    else Запит заповнений
        UI->>+SS: searchBooks(query)
        SS-->>-UI: results
        UI->>U: showResults(results)
    end
    UI-->>-U: екран пошуку
```

## Пояснення
- Інтерфейс сам перевіряє запит (self-call validateQuery), тому в помилковій гілці до Сервісу пошуку нічого не йде (FR-6).
- Смуга Інтерфейсу триває до фрагмента, через увесь alt і закінчується після нього.
