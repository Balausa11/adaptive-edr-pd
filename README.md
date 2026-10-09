# Adaptive EDR/DLP for Personal Data Protection

MVP-система защиты персональных данных (ПД): агент на Windows-компьютере находит файлы с ПД, блокирует их копирование на USB и отправляет в карантин; сервер собирает события, формирует оповещения и показывает их в веб-панели.

## Возможности

**Агент (`agent/`)**
- Сканирование txt, csv, json, xml, docx, xlsx, pptx, pdf
- Поиск ИИН (с проверкой контрольной суммы), банковских карт (алгоритм Луна), номеров документов, телефонов, email, дат рождения, ФИО (regex + spaCy)
- Оценка риска HIGH / MEDIUM / LOW
- Блокировка копирования ПД на USB, карантин и восстановление файлов
- Мониторинг процессов и буфера обмена, скриншоты при инциденте
- Локальный буфер событий, если сервер недоступен

**Сервер (`server/`)**
- FastAPI, WebSocket, JWT-авторизация, PostgreSQL (SQLAlchemy async)
- Движок правил (`detection_engine.py`) превращает события в оповещения
- Карантин, статистика, недельные и месячные отчёты, email-уведомления

**ML (`server/ml_model/`)**
- Генерация синтетического датасета казахстанских ПД, обучение TF-IDF + LogisticRegression / RandomForest / GradientBoosting, выбор лучшей модели по F1. См. `server/ml_model/README.md`.
- Статус: модуль обучается отдельно, в агент пока не подключён.

## Запуск

Нужны Python 3.11+, PostgreSQL и Windows (для агента).

```bash
# сервер
cd server
pip install -r requirements.txt
cp .env.example .env        # заполните значения
uvicorn api:app --host 0.0.0.0 --port 8088
# панель: http://127.0.0.1:8088

# агент
cd agent
pip install -r requirements.txt
python -m spacy download ru_core_news_sm
cp config.ini.example config.ini   # укажите адрес сервера
python agent.py
```

## Безопасность
Файлы `.env`, `config.ini`, `email_settings.json`, ключи шифрования и базы данных не хранятся в репозитории. Датасеты и обученные модели не включены: воссоздаются скриптами в `server/ml_model/`.

## Автор
Balausa Tansykbaeva
