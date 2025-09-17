# CRM API · Postman Portfolio

Небольшой репозиторий для демонстрации работы с **Postman** на примере CRM-API: структура коллекции, окружения и переменных, SMOKE и негативные проверки, а также data-driven прогоны через Runner.  
Запуск — **вручную в Postman**. Секреты не хранятся в коде.

---

## Как быстро посмотреть

1. Импортируйте коллекцию из `collections/CRM-start.postman_collection.json`.
2. Скопируйте окружение `env/CRM-Test.sample.postman_environment.json` → создайте локальное окружение и заполните **Current value** (плейсхолдеры ниже).
3. Откройте **Runner**:
   - запустите папку **Leads — smoke (seed)** или **Leads — create (on-demand)**;
   - для **list (csv)** подключите `datasets/leadList-cases.csv`.
4. Скриншоты успешных прогонов — в `assets/`.

---

## 📦 Содержимое репозитория

- collections/ — коллекции Postman
- env/ — sample-окружение (без секретов)
- datasets/ — CSV для data-driven запусков
- assets/ — скриншоты прогонов Runner
- .gitignore
- README.md

---

## 🔐 Переменные и конфиденциальность (важно)

- В репозитории лежит только **sample-окружение**: `env/CRM-Test.sample.postman_environment.json` с плейсхолдерами вида `<BASE_URL>`, `<AUTH_URL>`, `<LOGIN>` и т. п.  
- Реальные значения задаются **локально** в Postman в поле **Current value** и в репозиторий **не попадают**.  
- Токен авторизации сохраняется шагом `Auth (setup)` в переменную окружения и используется последующими запросами.  
- CI/Newman намеренно **не используются** в этом репозитории (цель — показать ручную работу в Postman).

### Переменные окружения (без примеров значений)

| Переменная           | Назначение                             |
|----------------------|----------------------------------------|
| `baseURL`            | Базовый URL API                        |
| `authURL`            | URL получения токена                   |
| `login` / `password` | Учётные данные тестового пользователя  |
| `userId`             | Идентификатор текущего пользователя    |
| `personId`           | Идентификатор персоны/контакта         |
| `responsibleUserId`  | Идентификатор ответственного           |
| `SEED_LEAD_ID`       | Тестовый лид для сценариев             |
| `BAD_PERSON_ID`      | Заведомо некорректный ID (negative)    |
| `stage`              | Значение стадии/фильтра                |
| `sortField`          | Поле сортировки по умолчанию           |
| `sortDirection`      | Направление сортировки                 |
| `DEFAULT_PAGE_SIZE`  | Размер страницы                        |

---

## 🧪 Что покрыто (по папкам коллекции)

### Leads — create (on-demand)
- **Auth (setup):** логин, сохранение токена в переменной окружения.
- **POST create:** создание лида; сохранение `leadId`; базовые проверки кода ответа и структуры.
- **GET verify:** чтение созданного лида по `leadId`; сверка ключевых атрибутов.
- **PUT update:** обновление комментария/полей; подтверждение изменённого значения повторным чтением.

### Leads — negative
- **GET без токена → 401:** проверка требования авторизации.
- **GET 422 на неверный `stage`:** валидация бизнес-параметра.
- **GET 404 (или 422) на плохой `id`:** корректный код и форма ошибки.
- **PUT Body.id ≠ path id:** конфликт идентификаторов → ожидаем 4xx.
- **POST create с неизвестным `personId` → 422/404:** контроль ссылочной целостности.

### list (csv)
- **Auth (setup)** → **GET leadList**.
- **Data-driven прогон** через `datasets/leadList-cases.csv`: для каждого набора параметров выполняется запрос списка; проверяются коды ответов и базовый контракт/метаданные списка.

### Leads — paging/sort
- **list — page 1 (desc)** / **page 2 (desc):** последовательная пагинация.
- **check — cross-page & границы:** отсутствие дублей/пропусков между страницами, корректные границы.
- **list — sort asc (page 1):** смена направления сортировки.
- **negative — page=0:** невалидная пагинация.
- **limit probe — limit=1:** минимальный размер страницы.

### Leads — smoke (seed)
- **Auth (setup)** → **GET seed:** чтение заранее существующего `SEED_LEAD_ID`.
- **PUT seed (update comment)** → **GET seed (verify):** обновление и верификация изменения.

---

## 🗺️ Карта коллекции

- Leads — create (on-demand)  
- Leads — negative  
- list (csv)  
- Leads — paging/sort  
- Leads — smoke (seed)

---

## ▶️ Как запустить (детально)

1) **Импорт**
- Postman → *Import* → `collections/CRM-start.postman_collection.json`  
- Postman → *Import* → `env/CRM-Test.sample.postman_environment.json`

2) **Создать локальное окружение**
- *Environments* → *Duplicate* sample → переименовать (например, `CRM Local`).  
- Заполнить **Current value** для переменных из таблицы выше.

3) **Программный запуск через Runner**
- Открыть *Runner* → выбрать коллекцию и локальное окружение.  
- Для папки **`list (csv)`**: *Data* → `datasets/leadList-cases.csv`.  
- Запустить: **SMOKE / NEGATIVE / paging/sort / create (on-demand)** по необходимости.

---

## 🖼 Скриншоты прогонов (assets)

- `Runner - runner-smoke-pass.png`  
- `Runner - runner-negative-pass.png`  
- `Runner - runner-negative-2.png`  

---

## 🧰 Навыки, которые демонстрирует репозиторий

- Построение и поддержка коллекций Postman (Collection / Folder / Request).  
- Работа с **Environments**, плейсхолдерами и безопасной конфигурацией (без секретов в коде).  
- Авторизация и переиспользование токена через скрипты.  
- Позитивные/негативные сценарии, базовый тест-дизайн.  
- **Data-driven** прогоны через Runner (CSV).  
- Мини-документация для ревьюера: как запустить и что ожидается.


## Контакты

Автор: Юлия — Manual QA (CRM/Real-Estate).  
Связь: через Issues репозитория или по запросу.

