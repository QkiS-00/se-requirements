# S2: Книга є в каталозі, але вільних примірників немає

```mermaid
sequenceDiagram
    actor U as Шукач книги
    participant UI as Інтерфейс пошуку
    participant SS as Сервіс пошуку
    participant K as Книга
    participant P as Примірник
    participant V as Видача
    participant B as Бібліотека

    U->>+UI: submitQuery(query)
    UI->>+SS: searchBooks(query)
    SS->>+K: findByTitleOrAuthor(query)
    K-->>-SS: books
    loop Для кожної знайденої Книги
        SS->>+P: findCopiesByBook(bookId)
        P-->>-SS: copies
        SS->>+V: findActiveLoansByCopies(copyIds)
        V-->>-SS: activeLoans
        loop Для кожної Бібліотеки з примірниками
            SS->>+B: getBranch(libraryId)
            B-->>-SS: назва_філії
            SS->>SS: countFreeCopies(copies, activeLoans, libraryId)
        end
        SS->>SS: sumFreeCopies(counts)
        opt У цієї Книги вільних примірників немає ніде
            SS->>SS: addMessage("Вільних примірників немає")
        end
    end
    SS-->>-UI: results (для кожної Книги нулі по філіях і повідомлення)
    UI-->>-U: showResults(results)
```

## Пояснення
- Пошук такий самий, як у S1, але всі примірники видані, тому лічильники дорівнюють 0 (FR-3).
- Перевірка «вільних немає ніде» виконується всередині циклу, окремо для кожної Книги, і додає повідомлення (FR-4).
- Смуга Сервісу пошуку триває через увесь цикл і закінчується після нього.
