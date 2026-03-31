# 👩‍💻 Татьяна Толмачева | QA Engineer

**Middle QA Engineer** с глубоким пониманием e-commerce, логистики и сквозных бизнес-процессов (Яндекс Маркет). Умею структурировать хаос, находить узкие места в интеграциях и писать понятную документацию. 

Активно развиваюсь в тестировании API, баз данных и автоматизации (UI + API).

📫 **Связь со мной:** [Telegram](https://t.me/tattolmacheva) | [Резюме на hh.ru](hh.ru/resume/252903ddff0d5eb2000039ed1f693776594c76) | **Email:** tan178@yandex.ru

---

## 🛠 Технический стек
* **База:** Функциональное, E2E, Регрессионное и Исследовательское тестирование. Техники тест-дизайна.
* **Web:** Chrome DevTools, HTML/CSS.
* **API:** Postman (Collections, Environments, Scripts), REST API, JSON.
* **Базы данных:** SQL (PostgreSQL/MySQL) — `JOIN`, `GROUP BY`, фильтрация.
* **Автоматизация:** Python 3, Pytest + Playwright.
* **Инструменты:** Jira, Confluence, Yandex Tracker/Wiki, TMS.

---

## 🚀 1. Автоматизация тестирования (UI + API Framework)
Разрабатываю фреймворк для автоматизированного тестирования (UI + API). В качестве тестового полигона использую сам GitHub.
* **Стек:** Python, Pytest, Playwright.
* **Что покрыто:** авторизация, создание репозитория через UI, проверка API-ответов.
* **Код проекта:** [Посмотреть репозиторий](https://github.com/ttolmacheva-qa/github-automation-framework)

```python
# Пример моего автотеста (сценарий логина)
def test_successful_login(browser):
    login_page = LoginPage(browser)
    login_page.open()
    login_page.enter_credentials("test_user", "password123")
    login_page.click_login_button()
    assert login_page.is_user_logged_in(), "Пользователь не авторизован"
```

---

## 🔌 2. Тестирование API (Postman)
Провожу тестирование REST API, проверяю статус-коды, структуру JSON и пишу базовые тесты (Assertions) внутри Postman.

* **Проект:** Тестирование публичного API магазина (Swagger).
* **Сценарий:** CRUD-операции для сущности "Заказ" (создание, получение статуса, изменение, удаление).

![Postman Screenshot](ВСТАВЬТЕ_СЮДА_СКРИНШОТ_POSTMAN)
> *Файл коллекции можно скачать [здесь](ССЫЛКА_НА_ФАЙЛ_В_РЕПОЗИТОРИИ.json)*

---

## 🗄 3. Работа с Базами Данных (SQL)
Уверенно пишу SQL-запросы для верификации тестовых данных. Понимаю структуру реляционных БД. 

**Пример моего запроса:** *Найти всех клиентов, у которых есть отмененные заказы на сумму более 5000 руб за последний месяц.*

```sql
SELECT 
    c.first_name, 
    c.last_name, 
    o.order_id, 
    o.total_amount
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.status = 'CANCELLED' 
  AND o.total_amount > 5000
  AND o.created_at >= CURRENT_DATE - INTERVAL '1 month'
ORDER BY o.total_amount DESC;
```

---

## 📝 4. Тестовая документация
Ниже представлены примеры моих артефактов тестирования для модуля "Корзина интернет-магазина".

### 🐛 Пример Баг-репорта
| Поле | Описание |
| :--- | :--- |
| **Summary** | [Корзина] Ошибка 500 при применении промокода с истекшим сроком действия |
| **Окружение** | Прод, Google Chrome 120.0, Windows 11 |
| **Шаги** | 1. Добавить товар в корзину.<br>2. Ввести промокод `EXPIRED24`.<br>3. Нажать "Применить". |
| **Фактический результат** | UI зависает, в DevTools (Network) падает ошибка `500 Internal Server Error`. |
| **Ожидаемый результат** | Появляется валидационное сообщение "Срок действия промокода истек". Статус-код ответа `400 Bad Request`. |

### 🧠 Mind-Map (Анализ требований)
Схема проверок логистического модуля (переходы статусов заказа).
![MindMap Screenshot](ВСТАВЬТЕ_СЮДА_СКРИНШОТ_МИРО_ИЛИ_XMIND)

---
*Спасибо за внимание! Открыта к предложениям и тестовым заданиям.*
