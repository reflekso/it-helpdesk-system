# Спецификация структуры данных

## Описание

Настоящий документ содержит техническую спецификацию структуры данных, включая ER-диаграмму, детальное описание таблиц и схему их маппинга с API. Файл определяет архитектуру хранения и логику передачи информации в системе для обеспечения согласованности разработки.

## Изменения спецификации структуры данных

| Дата изменения | Версия | Автор | Описание |
|---|---|---|---|
| 14.09.2026 | 1.0 | Васильев М.А. | Первая версия требований для MVP |

## ER-диаграмма

![ER-диаграмма](er-diagram.png)

## Описание таблиц и полей

### Таблица 1. `users`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| user_id | INT32 | Да | Идентификатор пользователя | schemas.UserID.user_id |
| login | VARCHAR(128) | Да | Логин пользователя | schemas.UserAccount.login |
| password_hash | VARCHAR(255) | Да | Хэш пароля пользователя | - |

---

### Таблица 2. `user_profiles`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| user_id | INT32 | Да | Идентификатор пользователя (связь с `users.user_id`) | schemas.UserID.user_id |
| role | VARCHAR(10) | Да | Роль пользователя в системе (ENUM Admin, Auditor, Employee, Specialist) | schemas.UserAccount.role |
| first_name | VARCHAR(64) | Да | Имя пользователя | schemas.UserAccount.firstname |
| last_name | VARCHAR(64) | Да | Фамилия пользователя | schemas.UserAccount.lastname |
| department_id | INT32 | Да | Идентификатор рабочего отдела пользователя (связь с `departments.department_id`) | schemas.DepartmentItem.department_id |
| workplace | VARCHAR(32) | Нет | Рабочее место пользователя (только у сотрудника) | schemas.UserAccount.workplace |

---

### Таблица 3. `tickets`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| ticket_id | INT64 | Да | Идентификатор заявки | schemas.TicketID.ticket_id |
| employee_id | INT32 | Да | Идентификатор Сотрудника, создавшего заявку (связь с `users.user_id`) | - |
| specialist_id | INT32 | Нет | Идентификатор Специалиста, принявшего заявку в работу (связь с `users.user_id`). Изначально NULL до момента, пока Специалист не возьмет заявку в работу | - |
| chat_id | INT64 | Да | Идентификатор переписки Сотрудника и Специалиста по заявке (связь с `chats.chat_id`) | - |
| category_id | INT32 | Да | Идентификатор категории проблемы у Сотрудника (связь с `categories_problem.category_id`) | schemas.CategoryItem. |
| overview | TEXT | Да | Описание проблемы Сотрудником | schemas.TicketOverview.overview | 
| status | VARCHAR(40) | Да | Статус заявки (ENUM «Создана», «В работе», «Закрыта специалистом», «Отправлена на доработку», «Закрыта») | schemas.TicketInfo.status |
| created_at | TIMESTAMPZ | Да | Дата и время создания заявки | schemas.TicketInfo.created_at |
| closed_at | TIMESTAMPZ | Нет | Дата и время закрытия заявки (заявка перешла в статус «Закрыта») | schemas.TicketInfo.closed_at |

---

### Таблица 4. `departments`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| department_id | INT32 | Да | Идентификатор рабочего отдела пользователя |  schemas.DepartmentItem.department_id |
| name | VARCHAR(128) | Да | Название рабочего отдела | schemas.DepartmentItem.department_name |

---

### Таблица 5. `problem_categories`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| category_id | INT32 | Да | Идентификатор категории проблемы у Сотрудника | schemas.CategoryItem.category_id |
| name | VARCHAR(128) | Да | Название категории проблемы | schemas.CategoryItem.category_name |

---

### Таблица 6. `chats`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| chat_id | INT64 | Да | Идентификатор переписки Сотрудника и Специалиста по заявке | - |
| is_active | BOOLEAN | Да | Статус чата: активен или закрыт | - |

---

### Таблица 7. `chat_messages`

| Название атрибута | Тип | Обязательность | Описание | API Mapping |
|---|---|---|---|---|
| message_id | INT64 | Да | Идентификатор сообщения | schemas.ChatMessage.message_id |
| chat_id | INT64 | Да | Идентификатор переписки Сотрудника и Специалиста по заявке (связь с `chats.chat_id`) | - |
| text_message | TEXT | Да | Текст сообщения, отправленного Сотрудником или Специалистом | schemas.ChatMessage.text_message |
| message_from | VARCHAR(10) | Да | Отправитель сообщения (ENUM "Employee", "Specialist") | schemas.ChatMessage.message_from |
| sent_at | TIMESTAMPZ | Да | Дата и время отправки сообщения | schemas.ChatMessage.sent_at |

