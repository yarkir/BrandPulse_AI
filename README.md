# BrandPulse AI —  MVP

Анализ комментариев YouTube: тональность, темы, дашборд, PR-дайджест и сравнение моделей.

## Состав
- `app.py` — Streamlit MVP; SQLite хранит комментарии, историю сборов и ручную разметку.
- `BrandPulse_AI_Colab.ipynb` — запуск Streamlit в Colab через Cloudflare Tunnel.
- `BrandPulse_Qwen_Comparison_Colab.ipynb` — эксперимент Baseline / Transformer / Qwen.
- `requirements.txt` — зависимости приложения.

## Быстрый запуск в Colab
1. Скачайте репозиторий как ZIP. Создайте ZIP с папкой `brandpulse_colab` (внутри `app.py`, `requirements.txt`); либо в Colab клонируйте репозиторий в `/content/brandpulse_colab` и пропустите ячейку распаковки.
2. Откройте `BrandPulse_AI_Colab.ipynb` и выполняйте ячейки по порядку. Подключите Google Drive для сохранения SQLite.
3. Необязательно: добавьте секрет `YOUTUBE_API_KEY` в Colab Secrets. Без ключа используйте демоданные или импорт CSV.
4. Откройте временную ссылку Cloudflare Tunnel и пройдите разделы приложения.
5. Для сравнения моделей откройте второй ноутбук в отдельной сессии Colab и загрузите CSV с `comment_id,gold_sentiment,gold_topic`. В базе должны существовать соответствующие `comment_id` и тексты.

## Локальный запуск
```bash
pip install -r requirements.txt
streamlit run app.py
```
По умолчанию SQLite создаётся как `brandpulse.db` в текущей директории; можно задать `BRANDPULSE_DB_PATH` для другого пути. `YOUTUBE_API_KEY` — необязательная переменная окружения. Для режима LLM нужен запущенный Ollama и скачанная модель `qwen2.5:1.5b`; в Streamlit Ollama опциональна. Модель Transformer при первом запуске скачивается из Hugging Face.

## Формат CSV
- Импорт новых комментариев: обязательный `text`, необязательные `comment_id,gold_sentiment,gold_topic`.
- Импорт разметки к существующим комментариям: `comment_id,gold_sentiment,gold_topic`.
- Категории тональности: Позитив, Негатив, Нейтрально (принимаются positive, negative, neutral).
- Темы: Качество, Цена, Дизайн, Функции, Поддержка, Доставка, Конкуренты, Другое (принимаются английские эквиваленты).

## Методика
Baseline — словарные правила. Transformer `cardiffnlp/twitter-xlm-roberta-base-sentiment` — тональность, тематика по тем же правилам. Qwen `qwen2.5:1.5b` — тональность и тема по инструкции с JSON-ответом. Macro F1 рассчитывается по ручной разметке. При сравнении используйте один и тот же набор комментариев, не подавайте gold-метки в модель. Эксперимент не является дообучением.

