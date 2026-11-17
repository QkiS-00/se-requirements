# S1: Книга знайдена, є вільні примірники

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
    Note right of SS: Вільний примірник: немає Видачі з порожньою дата_повернення, стан не «втрачений»
    loop Для кожної знайденої Книги
        par Примірники книги
            SS->>+P: findCopiesByBook(bookId)
            P-->>-SS: copies
        and Активні видачі книги
            SS->>+V: findActiveLoans(bookId)
            V-->>-SS: activeLoans
        end
        loop Для кожної Бібліотеки з примірниками
            SS->>+B: getBranch(libraryId)
            B-->>-SS: назва_філії
            SS->>SS: countFreeCopies(copies, activeLoans)
        end
    end
    SS-->>-UI: results
    UI-->>-U: showResults(results)
```

## Пояснення
- Сервіс пошуку шукає Книги, а потім для кожної паралельно (par) бере Примірники та активні Видачі.
- У вкладеному циклі по Бібліотеках рахується кількість вільних примірників.
- Смуги Інтерфейсу та Сервісу починаються до фрагментів і закінчуються після них.
