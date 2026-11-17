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
        SS->>+P: findCopiesByBook(bookId)
        P-->>-SS: copies
        SS->>+V: findActiveLoansByCopies(copyIds)
        V-->>-SS: activeLoans
        loop Для кожної Бібліотеки з примірниками
            par Назва філії
                SS->>+B: getBranch(libraryId)
                B-->>-SS: назва_філії
            and Кількість вільних
                SS->>SS: countFreeCopies(copies, activeLoans, libraryId)
            end
        end
    end
    SS-->>-UI: results
    UI-->>-U: showResults(results)
```

## Пояснення
- Видача пов'язана лише з Примірником, тому активні видачі шукаємо за примірниками книги, після того як їх отримано.
- Паралельними (par) є тільки незалежні кроки: взяти назву філії та порахувати вільних.
- Смуги Інтерфейсу та Сервісу починаються до фрагментів і закінчуються після них.
