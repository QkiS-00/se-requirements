# S3: Книги з таким запитом немає

```mermaid
sequenceDiagram
    actor U as Шукач книги
    participant UI as Інтерфейс пошуку
    participant SS as Сервіс пошуку
    participant K as Книга

    U->>+UI: submitQuery(query)
    UI->>+SS: searchBooks(query)
    SS->>+K: findByTitleOrAuthor(query)
    K-->>-SS: books (порожній список)
    SS-->>-UI: results (порожній)
    opt Результат порожній
        UI->>U: showMessage("Нічого не знайдено")
        UI->>U: suggestChangeQuery()
    end
    UI-->>-U: екран пошуку
```

## Пояснення
- Книг за запитом немає, тому Сервіс повертає порожній результат, а не помилку.
- Інтерфейс показує реакцію на порожній результат у фрагменті opt (FR-5). Гілку з результатами тут не дублюємо, вона є в S1. Його смуга активності не обривається всередині фрагмента.
