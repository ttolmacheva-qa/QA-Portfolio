# QA Portfolio — Татьяна Толмачева

Middle QA Engineer • Яндекс Маркет (Логистика, OMS, E2E)  
[Telegram](https://t.me/tattolmacheva) • [hh.ru](https://hh.ru/resume/252903ddff0d5eb2000039ed1f693776594c76) • tan178@yandex.ru

---

## 🛠 Технический стек

**Тестирование:** Функциональное, E2E, Регрессионное, Исследовательское, техники тест-дизайна  
**API:** Postman (Collections, Environments, Scripts), REST API, JSON  
**Автоматизация:** Python 3, Pytest, Playwright  
**Базы данных:** SQL — PostgreSQL/MySQL (`JOIN`, подзапросы, оконные функции)  
**Web:** Chrome DevTools, HTML/CSS  
**Инструменты:** Jira, Confluence, Yandex Tracker/Wiki, TMS, Git

---

## 🚀 Автоматизация тестирования (UI + API)

Фреймворк для автоматизированного тестирования на Python. Тестовый полигон — GitHub.

**Стек:** Python, Pytest, Playwright  
**Код проекта:** [github-automation-framework](https://github.com/ttolmacheva-qa/github-automation-framework)

**Структура проекта:**

```
├── api/
│   └── github_client.py    # API-клиент (обёртка над requests)
├── pages/
│   ├── base_page.py         # Базовый Page Object
│   ├── login_page.py        # Авторизация
│   ├── profile_page.py      # Профиль пользователя
│   └── repo_page.py         # Страница репозитория
├── tests/
│   ├── conftest.py          # Фикстуры (setup/teardown)
│   ├── test_repo_crud.py    # E2E: создание/удаление репо
│   └── test_profile.py      # UI: проверки профиля
├── .github/workflows/       # CI/CD (GitHub Actions)
├── pytest.ini
└── requirements.txt
```

**Пример: создание репозитория через API + проверка в UI**

```python
@pytest.fixture
def repo_via_api(api_client):
    """Создаём репозиторий через API, проверяем через UI, чистим после."""
    repo = api_client.create_repo(name=f"test-{uuid4().hex[:8]}")
    yield repo
    api_client.delete_repo(repo.name)

def test_new_repo_visible_in_profile(browser, repo_via_api):
    profile = ProfilePage(browser).open()
    assert profile.has_repo(repo_via_api.name), \
        f"Репозиторий {repo_via_api.name} не отображается в профиле"
```

---

## 🔌 Тестирование API (Postman)

Коллекция запросов для GitHub API с автоматическими проверками на JavaScript.

**Что покрыто:**

- CRUD-операции: GET, POST, PUT/PATCH, DELETE
- Валидация статус-кодов и JSON-схем
- Chaining запросов (результат одного → вход следующего)
- Переменные окружения (Environment)

<p align="center">
  <img src="https://github.com/user-attachments/assets/88cbaa5c-1139-4473-b58f-54ce40ddc444" width="45%" />
  <img src="https://github.com/user-attachments/assets/ae5c1d6d-3a96-4db6-aef9-a6fcbb611aa2" width="45%" />
</p>

> [📂 Скачать коллекцию (.json)](https://github.com/ttolmacheva-qa/QA-Portfolio/blob/main/GitHub%20API%20Portfolio.postman_collection.json)

---

## 🗄 Работа с Базами Данных (SQL)

SQL-запросы для верификации данных в логистическом контексте. PostgreSQL.

**Пример 1.** Клиенты с отменёнными заказами на сумму > 5000 ₽ за последний месяц:

```sql
SELECT c.first_name, c.last_name, o.order_id, o.total_amount
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.status = 'CANCELLED'
  AND o.total_amount > 5000
  AND o.created_at >= CURRENT_DATE - INTERVAL '1 month'
ORDER BY o.total_amount DESC;
```

**Пример 2.** Среднее время доставки по регионам с выявлением аномалий:

```sql
SELECT
    r.region_name,
    COUNT(o.order_id) AS total_orders,
    ROUND(AVG(EXTRACT(EPOCH FROM (o.delivered_at - o.shipped_at)) / 3600), 1) AS avg_delivery_hours,
    ROUND(MAX(EXTRACT(EPOCH FROM (o.delivered_at - o.shipped_at)) / 3600), 1) AS max_delivery_hours
FROM orders o
JOIN regions r ON o.region_id = r.region_id
WHERE o.status = 'DELIVERED'
  AND o.delivered_at IS NOT NULL
GROUP BY r.region_name
HAVING AVG(EXTRACT(EPOCH FROM (o.delivered_at - o.shipped_at)) / 3600) > 48
ORDER BY avg_delivery_hours DESC;
```

**Пример 3.** Ранжирование курьеров по количеству доставок (оконная функция):

```sql
SELECT
    courier_id,
    courier_name,
    deliveries_count,
    RANK() OVER (ORDER BY deliveries_count DESC) AS rank
FROM (
    SELECT
        c.courier_id,
        c.name AS courier_name,
        COUNT(d.delivery_id) AS deliveries_count
    FROM couriers c
    JOIN deliveries d ON c.courier_id = d.courier_id
    WHERE d.status = 'COMPLETED'
      AND d.completed_at >= CURRENT_DATE - INTERVAL '1 month'
    GROUP BY c.courier_id, c.name
) sub;
```

---

## 📝 Тестовая документация

### 🐛 Баг-репорт

|Поле|Описание|
|:--|:--|
|**Summary**|[Корзина] Ошибка 500 при применении промокода с истекшим сроком действия|
|**Severity**|Critical|
|**Окружение**|Прод, Google Chrome 120.0, Windows 11|
|**Предусловие**|В корзине есть хотя бы 1 товар|
|**Шаги**|1. Открыть корзину. 2. Ввести промокод `EXPIRED24`. 3. Нажать «Применить».|
|**Фактический результат**|UI зависает на ~5 сек. В DevTools → Network: `POST /api/cart/promo` возвращает `500 Internal Server Error`. Тело ответа: `{"error": "NullPointerException"}`.|
|**Ожидаемый результат**|Сообщение «Срок действия промокода истёк». Статус `400 Bad Request`.|
|**Приложение**|Скриншот Network-вкладки, curl-запрос для воспроизведения|

---

## 📦 Продуктовый кейс: Яндекс Маркет — Логистика

Тестировала ключевые логистические процессы на одном из крупнейших e-commerce-сервисов России.

**Зона ответственности:** сквозное тестирование логистического модуля — переходы статусов заказа, управление партнёрами, интеграции со службами доставки, расчёт сроков и слотов, трекинг, возвраты и отмены.

### Ключевой результат

Написала регрессионный набор из 590 тест-кейсов с нуля для внутреннего логистического сервиса. До этого регресс не был формализован — тестирование проводилось ad hoc.

|Метрика|До|После|
|:--|:--|:--|
|Покрытие критичных путей регрессом|Не формализовано|~85% основных сценариев|
|Время полного регресса|Не регламентировано|~3 рабочих дня (1 QA)|
|Критичные баги в проде (за квартал)|3–4|≤ 1|
|Онбординг нового QA на регресс|~2 недели с наставником|~3 дня по документации|

### Чем это ценно

- Регресс стал воспроизводимым — любой QA мог провести его без потери покрытия.
- Снизился риск пропуска дефектов при частых релизах в критичном для бизнеса сервисе.
- Появилась прозрачная картина того, что именно проверяется перед каждым релизом.

---

_Открыта к предложениям и тестовым заданиям._
