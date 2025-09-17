CRM — Postman API tests (Leads)

Коллекция Postman для CRM Leads API: smoke, negative, paging/sort.
Собрано так, чтобы не светить приватные URL и учётки: в репозитории лежит только sample-окружение с плейсхолдерами.

📦 Состав репозитория
collections/
  CRM-start.postman_collection.json
env/
  CRM-Test.sample.postman_environment.json   # только плейсхолдеры, без секретов
assets/                                      # скрины (опционально)
README.md
CASE_STUDY.md                                # короткая история "как делала" (опц.)
.gitignore

🧭 Что проверяет коллекция

Leads — smoke (seed)
Авторизация → чтение существующего лида → обновление поля (комментарий) → верификация обновления.

Leads — negative
401 без токена, 422 на неверный stage, 404/422 на плохой id, конфликт body.id ≠ path id, create с невалидным personId.

Leads — paging/sort
Сортировка createdAt (asc/desc), page=1/page=2 без дублей, «мягкая» граница при одинаковых createdAt, поведение limit.

Auth стоит на уровне коллекции (Pre-request Script подставляет Bearer-токен перед запросами). На негативы локально убираем Authorization.

🧩 Переменные окружения (что нужно задать локально)

В CRM-Test.sample.postman_environment.json все значения — плейсхолдеры:

<AUTH_URL> — адрес auth-сервиса

<BASE_URL> — адрес CRM backend

<LOGIN> / <PASSWORD> — тестовая учётка

<USER_ID>, <PERSON_ID>, <RESPONSIBLE_USER_ID> — валидные ID для стенда

<SEED_LEAD_ID> — ID существующего лида для smoke/negative

stage=process, sortField=createdAt, sortDirection=desc — можно оставить как есть

В sample-env нет динамики: token, leadId, uniq, P1_IDS и пр. — убраны.

▶️ Как запустить локально (без засвета URL)

Установи инструменты:

npm i -g newman newman-reporter-htmlextra


Создай локальный env из образца и подставь свои значения:

cp env/CRM-Test.sample.postman_environment.json env/CRM-Test.local.postman_environment.json
# открой env/CRM-Test.local.postman_environment.json и замени <...> на реальные значения


Примеры запусков Newman:

Smoke

npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — smoke (seed)" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_smoke.html \
  --reporter-htmlextra-title "Leads Smoke"


Negative

npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — negative" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_negative.html \
  --reporter-htmlextra-title "Leads Negative"


Paging/Sort

npx newman run collections/CRM-start.postman_collection.json \
  -e env/CRM-Test.local.postman_environment.json \
  --folder "Leads — paging/sort" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/leads_paging_sort.html \
  --reporter-htmlextra-title "Leads Paging & Sort"


Отчёты в reports/ можно приложить к отклику или показать на собесе.

🔒 Безопасность данных

В репозитории — только коллекция и sample-env с плейсхолдерами.

Реальный локальный env (CRM-Test.local...) держи у себя и не коммить — он в .gitignore.

В скринах/репортах не показывай реальные URL/логин/пароль/токены (замазывай).

🛠 Полезные утилиты в коллекции

Utils → Env — scrub: чистит мусорные переменные окружения после прогонов (чтобы env оставался «стройным»).

🧷 Траблшутинг (коротко)

422 на /lead/list — проверь stage (должен быть валидный, например process).

401 — токен протух: коллекционный Pre-request сам перелогинится при валидных <LOGIN>/<PASSWORD>.

Дубли между page=1/2 — у одинаковых createdAt допускается «мягкая» граница (это зашито в тестах).

📝 Для ревьюера

Коллекция: collections/CRM-start.postman_collection.json

Переменные: env/CRM-Test.sample.postman_environment.json

(Опц.) Скрины: assets/

Отчёты: можно сгенерировать локально командами выше.
