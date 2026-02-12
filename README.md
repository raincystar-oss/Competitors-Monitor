# Competitors Monitor

MVP-приложение для конкурентного анализа:
- анализ текста конкурента через OpenAI,
- анализ изображений (включая визуальный стиль и дизайн),
- парсинг сайта через Selenium + Chrome со скриншотом и AI-анализом,
- хранение истории последних запросов.

Проект включает:
- `backend` (FastAPI API),
- `frontend` (web-интерфейс),
- `desktop` (PyQt6-клиент).

## Описание

Приложение позволяет:

- Анализировать текст конкурентов — получать структурированную аналитику с сильными/слабыми сторонами, уникальными предложениями и рекомендациями.
- Анализировать изображения — баннеры, скриншоты сайтов, упаковки товаров с оценкой визуального стиля.
- Парсить сайты — автоматически извлекать и анализировать контент по URL.
- Хранить историю — последние 10 запросов сохраняются для быстрого доступа.

## 1. Возможности

### Text Analysis (/analyze_text)
- Принимает текст конкурента (минимум 10 символов).
- Возвращает:
  - Сильные стороны
  - Слабые стороны
  - Уникальные предложения
  - Рекомендации по улучшению
  - Оценку информативности контента (0-10)
  - Комментарий по информативности контента
  - Общее резюме

### Image Analysis (/analyze_image)
- Принимает изображения: PNG, JPG, GIF, WEBP.
- Возвращает:
  - Описание изображения
  - Маркетинговые инсайты
  - Оценку визуального стиля (0-10)
  - Комментарий по визуальному стилю
  - Оценку дизайна (0-10)
  - Комментарий по дизайну
  - Рекомендации

### Website Parsing (/parse_demo)
- Принимает URL сайта.
- Извлекает: title, h1, первый абзац.
- Запускает Chrome через Selenium и делает скриншот страницы.
- Передает скриншот и извлеченный контент на анализ ИИ.

### History (/history)
- Хранит последние 10 запросов.
- Сохраняет тип запроса, краткое описание и время.

## 2. Требования

- Python 3.9+
- Google Chrome (для Selenium-парсинга)
- Доступ в интернет
- Ключ OpenAI API

## 3. Установка

Из корня проекта:

```powershell
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
```

Для desktop-клиента отдельно:

```powershell
cd desktop
pip install -r requirements.txt
cd ..
```

## 4. Настройка `.env`

Создайте файл `.env` в корне проекта:

```env
OPENAI_API_KEY=your_openai_api_key

# Опционально: для совместимого провайдера
# OPENAI_BASE_URL=https://api.openai.com/v1

OPENAI_MODEL=gpt-4o-mini
OPENAI_VISION_MODEL=gpt-4o-mini
```

Дополнительно (опционально) можно настроить в коде:
- `api_host`, `api_port`,
- `parser_timeout`,
- `max_history_items`.

## 5. Запуск

### Backend + Web

```powershell
python .\run.py
```

После запуска:
- Web UI: `http://127.0.0.1:8000`
- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

### Альтернативный запуск через uvicorn

```powershell
uvicorn backend.main:app --reload
```

### Desktop клиент

1. Запустите backend.
2. В новом терминале:

```powershell
cd desktop
python .\main.py
```

## 6. Сборка `.exe` desktop

```powershell
cd desktop
python .\build.py
```

Результат: `desktop/dist/CompetitorMonitor.exe`.

Важно: текущий desktop-клиент работает с backend на `http://localhost:8000`, поэтому backend должен быть запущен.

## 7. API (кратко)

### `POST /analyze_text`
Пример запроса:

```json
{
  "text": "Текст конкурента для анализа..."
}
```

### `POST /analyze_image`
`multipart/form-data` с полем `file`.

### `POST /parse_demo`
Пример запроса:

```json
{
  "url": "https://example.com"
}
```

### `GET /history`
Получить историю запросов.

### `DELETE /history`
Очистить историю.

### `GET /health`
Проверка доступности сервиса.

## 8. Структура проекта

```text
competitor-monitor/
├── backend/
│   ├── __init__.py
│   ├── main.py              # FastAPI приложение
│   ├── config.py            # Конфигурация
│   ├── models/
│   │   ├── __init__.py
│   │   └── schemas.py       # Pydantic модели
│   └── services/
│       ├── __init__.py
│       ├── openai_service.py    # Работа с OpenAI API
│       ├── parser_service.py    # Парсинг веб-страниц
│       └── history_service.py   # Управление историей
├── frontend/
│   ├── index.html           # HTML страница
│   ├── styles.css           # Стили
│   └── app.js               # JavaScript логика
├── requirements.txt         # Зависимости Python
├── env.example.txt          # Пример .env файла
├── history.json             # Файл истории (создаётся автоматически)
├── README.md                # Этот файл
└── docs.md                  # Документация API
```

## 11. Технологии

- Python, FastAPI, Pydantic
- OpenAI Python SDK
- Selenium + ChromeDriver (webdriver-manager)
- Vanilla JS + CSS
- PyQt6
