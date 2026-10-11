# RAG-ассистент на OpenAI API

Консольный RAG-ассистент (Retrieval-Augmented Generation), который отвечает на вопросы по собственной базе знаний.

- **OpenAI API** — генерация ответов (`gpt-4o-mini`) и эмбеддинги (`text-embedding-3-small`);
- **ChromaDB** — векторное хранилище для поиска релевантных фрагментов;
- **SQLite** — кэш ответов на повторяющиеся вопросы;
- **RAGAS** — оценка качества ответов (Faithfulness, Context Precision).

## Структура

```
5-8-final/
├── assistant_api/
│   ├── app.py              # консольное приложение
│   ├── rag_pipeline.py     # кэш → поиск → промпт → OpenAI → ответ
│   ├── vector_store.py     # ChromaDB, разбиение на чанки, эмбеддинги
│   ├── cache.py            # SQLite-кэш
│   ├── evaluate_ragas.py   # оценка качества через RAGAS
│   └── data/docs.txt       # база знаний
├── .env.example
├── requirements.txt
└── README.md
```

## Установка (Windows, Python 3.11)

```powershell
# Python 3.11 (если не установлен)
winget install Python.Python.3.11

# Виртуальное окружение
py -3.11 -m venv venv_py311
.\venv_py311\Scripts\Activate.ps1

# Зависимости
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## API-ключ

Скопируйте `.env.example` в `.env` в **корне проекта** и укажите свой ключ:

```
OPENAI_API_KEY=sk-...
```

Файл `.env` добавлен в `.gitignore` и не попадает в репозиторий.

## Запуск ассистента

Запускать нужно **из папки `assistant_api`**, так как пути к базе знаний и хранилищам относительные:

```powershell
cd assistant_api
python app.py
```

При первом запуске документы из `data/docs.txt` разбиваются на чанки и загружаются в ChromaDB (папка `assistant_api/chroma_db`).

Команды в чате:
- `stats` — статистика хранилища и кэша;
- `clear` — очистить кэш;
- `exit` / `quit` — выход.

## Оценка качества (RAGAS)

```powershell
cd assistant_api
python evaluate_ragas.py
```

Скрипт задаёт тестовые вопросы ассистенту и считает метрики Faithfulness и Context Precision. Для оценки также используется OpenAI API.

## Своя база знаний

1. Замените `assistant_api/data/docs.txt` своим текстом (UTF-8, абзацы разделяются пустой строкой).
2. Удалите папку `assistant_api/chroma_db` — иначе останутся старые документы.
3. Удалите `assistant_api/api_rag_cache.db` (или выполните `clear`), чтобы не получать старые ответы из кэша.
4. При необходимости обновите тестовые вопросы `EVALUATION_QUESTIONS` в `evaluate_ragas.py`.
