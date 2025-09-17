# CRM — Postman API tests (Leads)

Коллекция Postman для Leads API: **smoke**, **negative**, **paging/sort**.  
Проект собран так, чтобы **не светить приватные URL и учётки**: в репозитории лежит только **sample-окружение** с плейсхолдерами.

## Структура
collections/
CRM-start.postman_collection.json
env/
CRM-Test.sample.postman_environment.json # только плейсхолдеры
assets/ # скриншоты (опционально)

## Что умеет коллекция
- **Leads — smoke (seed)**: логин → чтение лида → обновление → верификация.
- **Leads — negative**: 401 без токена, 422 на неверный `stage`, 404/422 на плохой `id`, конфликт `body.id ≠ path id`, create с некорректным `personId`.
- **Leads — paging/sort**: проверка сортировок и постранички, учёт «мягкой» границы по одинаковому `createdAt`.

## Как запустить локально (без засвета URL)
1. Установить Node 20+ и Newman:
   ```bash
   npm i -g newman newman-reporter-htmlextra
Создать локальное окружение из образца и подставить свои значения (файл в репозиторий не добавляем):

cp env/CRM-Test.sample.postman_environment.json env/CRM-Test.local.postman_environment.json
# отредактируйте env/CRM-Test.local.postman_environment.json:
# <AUTH_URL>, <BASE_URL>, <LOGIN>, <PASSWORD>, <USER_ID>, <PERSON_ID>, <RESPONSIBLE_USER_ID>, <SEED_LEAD_ID>
Примеры запусков:

# Smoke
npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — smoke (seed)" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_smoke.html

# Negative
npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — negative" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_negative.html

# Paging/Sort
npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — paging/sort" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_paging_sort.htm

  Безопасность

В репозитории нет реальных URL и учёток.

Локальный файл env/CRM-Test.local.postman_environment.json храните у себя и не коммитьте (см. .gitignore).

Что посмотреть ревьюеру

collections/CRM-start.postman_collection.json — структура и тесты.

env/CRM-Test.sample.postman_environment.json — какие переменные нужны.

(Опционально) скриншоты в assets/.


# Case Study: CRM Leads API (Postman)

**Цель.** Покрыть базу по Leads API: авторизация, листинг, получение по id, создание/обновление, негативы, пагинация.

**Подход.**
- Вынесла авторизацию в Pre-request коллекции; для негативов локально убираю `Authorization`.
- Переменные окружения: urls/креды/идентификаторы.
- Пагинация: учитываю дубли на границе при одинаковом `createdAt` (мягкая проверка).
- Запуск: Runner/Newman с HTML-отчётом.

**Итог.**
- 3 набора прогонов (smoke/negative/paging), все зелёные на тестовом стенде.
- Репозиторий без секретов, с понятным README.

**Дальше.**
- Добавить Persons API и E2E-цепочку (create person → create lead → update → verify).