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
        SS->>+V: findActiveLoans(bookId)
        V-->>-SS: activeLoans
        loop Для кожної Бібліотеки з примірниками
            SS->>+B: getBranch(libraryId)
            B-->>-SS: назва_філії
            SS->>SS: countFreeCopies(copies, activeLoans)
        end
    end
    SS->>SS: sumFreeCopies(counts)
    SS-->>-UI: results з totalFree
    alt Вільних примірників немає ніде, totalFree = 0
        UI->>U: showResults(results з нулями для кожної філії)
        UI->>U: showMessage("Вільних примірників немає")
    else Є хоча б один вільний примірник
        UI->>U: showResults(results)
    end
    UI-->>-U: відображено
```

## Пояснення
- Пошук такий самий, як у S1, але всі примірники видані, тому лічильники дорівнюють 0.
- Після циклів Сервіс рахує загальну кількість вільних. Якщо вона 0, Інтерфейс показує нулі по філіях і повідомлення (FR-3, FR-4).
- Смуга Інтерфейсу триває через увесь фрагмент alt і закінчується після нього.
