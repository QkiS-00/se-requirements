# S5: Перегляд адреси філії з вільним примірником

```mermaid
sequenceDiagram
    actor U as Шукач книги
    participant UI as Інтерфейс пошуку
    participant SS as Сервіс пошуку
    participant B as Бібліотека

    Note over U,B: Передумова: книга знайдена, у філії «Центральна» є вільний примірник
    U->>+UI: viewResults()
    UI-->>U: results зі списком філій
    opt Шукач обирає філію з результатів
        U->>UI: selectBranch(libraryId)
        UI->>+SS: getBranchInfo(libraryId)
        SS->>+B: findById(libraryId)
        B-->>-SS: назва_філії, адреса
        SS-->>-UI: branchInfo
        UI-->>U: showBranch(назва_філії, адреса)
    end
    UI-->>-U: екран результатів
```

## Пояснення
- Перегляд адреси необов'язковий (extend до UC-1), тому його показано фрагментом opt (FR-7).
- Інтерфейс працює і до фрагмента, і після нього, тому його смуга активності не обривається.
