# UI Test Cases — The Internet

## Применённые техники тест-дизайна

- **Equivalence Partitioning (EP)** — для полей логина делим данные на классы: валидные, невалидные, пустые
- **Boundary Value Analysis (BVA)** — проверяем граничные значения: пустые поля, максимально длинные строки
- **State Transition** — для dynamic_loading проверяем переходы состояний: до загрузки → загрузка → после загрузки

---

## Страница 1: Form Authentication (/login)

### TC-LOGIN-001
| Поле | Значение |
|---|---|
| Title | Успешный вход с валидными данными |
| Priority | High |
| Preconditions | Открыта страница /login |
| Steps | 1. Ввести username: tomsmith 2. Ввести password: SuperSecretPassword! 3. Нажать Login |
| Expected result | Редирект на /secure, сообщение "You logged into a secure area!" |
| Actual result | Редирект на /secure, сообщение "You logged into a secure area!" |
| Status | Pass |

### TC-LOGIN-002
| Поле | Значение |
|---|---|
| Title | Неверный пароль |
| Priority | High |
| Preconditions | Открыта страница /login |
| Steps | 1. Ввести username: tomsmith 2. Ввести password: wrongpassword 3. Нажать Login |
| Expected result | Сообщение об ошибке "Your password is invalid!" |
| Actual result | Сообщение об ошибке "Your password is invalid!" |
| Status | Pass |

### TC-LOGIN-003
| Поле | Значение |
|---|---|
| Title | Неверный username |
| Priority | High |
| Preconditions | Открыта страница /login |
| Steps | 1. Ввести username: wronguser 2. Ввести password: SuperSecretPassword! 3. Нажать Login |
| Expected result | Сообщение об ошибке "Your username is invalid!" |
| Actual result | Сообщение об ошибке "Your username is invalid!" |
| Status | Pass |

### TC-LOGIN-004
| Поле | Значение |
|---|---|
| Title | Пустые поля (BVA — граничное значение: минимум) |
| Priority | High |
| Preconditions | Открыта страница /login |
| Steps | 1. Оставить оба поля пустыми 2. Нажать Login |
| Expected result | Сообщение об ошибке валидации |
| Actual result | Сообщение "Your username is invalid!" |
| Status | Pass |

### TC-LOGIN-005
| Поле | Значение |
|---|---|
| Title | Выход из системы после входа |
| Priority | Medium |
| Preconditions | Пользователь залогинен на /secure |
| Steps | 1. Нажать кнопку Logout |
| Expected result | Редирект на /login, сообщение "You logged out of the secure area!" |
| Actual result | Редирект на /login, сообщение "You logged out of the secure area!" |
| Status | Pass |

### TC-LOGIN-006
| Поле | Значение |
|---|---|
| Title | SQL-инъекция в поле username (EP — невалидный класс) |
| Priority | Medium |
| Preconditions | Открыта страница /login |
| Steps | 1. Ввести username: ' OR '1'='1 2. Ввести любой пароль 3. Нажать Login |
| Expected result | Сообщение об ошибке, вход не выполнен |
| Actual result | Сообщение "Your username is invalid!" |
| Status | Pass |

---

## Страница 2: File Upload (/upload)

### TC-UPLOAD-001
| Поле | Значение |
|---|---|
| Title | Успешная загрузка файла |
| Priority | High |
| Preconditions | Открыта страница /upload, есть любой файл на компьютере |
| Steps | 1. Нажать Choose File 2. Выбрать любой файл 3. Нажать Upload |
| Expected result | Страница показывает "File Uploaded!" и имя файла |
| Actual result | Страница показывает "File Uploaded!" и имя файла |
| Status | Pass |

### TC-UPLOAD-002
| Поле | Значение |
|---|---|
| Title | Загрузка без выбора файла (BVA — граничное значение) |
| Priority | High |
| Preconditions | Открыта страница /upload |
| Steps | 1. Не выбирать файл 2. Нажать Upload |
| Expected result | Сообщение об ошибке или предупреждение |
| Actual result | Страница показывает "File Uploaded!" с пустым именем — БАГ |
| Status | Fail |

### TC-UPLOAD-003
| Поле | Значение |
|---|---|
| Title | Загрузка файла через drag and drop (EP — альтернативный способ) |
| Priority | Low |
| Preconditions | Открыта страница /upload |
| Steps | 1. Перетащить файл в зону загрузки |
| Expected result | Файл загружается успешно |
| Actual result | Файл загружается успешно |
| Status | Pass |

---

## Страница 3: Dynamic Loading (/dynamic_loading/1)

### TC-DYNLOAD-001
| Поле | Значение |
|---|---|
| Title | Элемент появляется после загрузки (State Transition) |
| Priority | High |
| Preconditions | Открыта страница /dynamic_loading/1 |
| Steps | 1. Нажать кнопку Start 2. Дождаться окончания загрузки |
| Expected result | Текст "Hello World!" появляется после исчезновения loader |
| Actual result | Текст "Hello World!" появляется |
| Status | Pass |

### TC-DYNLOAD-002
| Поле | Значение |
|---|---|
| Title | Loader исчезает после загрузки |
| Priority | Medium |
| Preconditions | Открыта страница /dynamic_loading/1 |
| Steps | 1. Нажать Start 2. Наблюдать за loader |
| Expected result | Loader виден во время загрузки и исчезает после |
| Actual result | Loader виден во время загрузки и исчезает после |
| Status | Pass |

### TC-DYNLOAD-003
| Поле | Значение |
|---|---|
| Title | Example 2 — элемент рендерится после загрузки |
| Priority | Medium |
| Preconditions | Открыта страница /dynamic_loading/2 |
| Steps | 1. Нажать Start 2. Дождаться загрузки |
| Expected result | Текст "Hello World!" появляется на странице |
| Actual result | Текст "Hello World!" появляется |
| Status | Pass |

---

## Найденные дефекты
### BUG-001
| Поле | Значение |
|---|---|
| Title | File Upload принимает пустой файл |
| Steps to reproduce | 1. Открыть /upload 2. Не выбирать файл 3. Нажать Upload |
| Expected | Сообщение об ошибке "Please select a file" |
| Actual | Страница показывает "File Uploaded!" с пустым именем файла |
| Severity | Medium |