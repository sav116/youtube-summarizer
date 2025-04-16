# YouTube Summarizer Bot

Telegram бот для суммаризации YouTube видео с использованием OpenAI API.

## Функциональность

- Получает субтитры из YouTube видео
- Работает с видео на разных языках (с возможностью выбора предпочтительных языков)
- Создает краткое резюме содержания видео с помощью OpenAI API
- Отправляет результат пользователю в Telegram
- Имеет систему доступа: только администратор имеет полный доступ, остальные пользователи должны запрашивать доступ
- Поддерживает модели с большим контекстным окном (1M+ символов), что позволяет обрабатывать даже очень длинные видео за один запрос

## Требования

- Python 3.8+
- Docker
- Токен Telegram бота (от @BotFather)
- Ключ API OpenAI
- ID администратора в Telegram

## Установка и запуск

### Подготовка

1. Клонировать репозиторий:
   ```bash
   git clone https://github.com/yourusername/youtube-summarizer.git
   cd youtube-summarizer
   ```

2. Создать файл `.env` на основе `.env.example`:
   ```bash
   cp .env.example .env
   ```

3. Отредактировать `.env` и добавить:
   - TELEGRAM_BOT_TOKEN: токен вашего Telegram бота
   - OPENAI_API_KEY: ваш ключ API OpenAI
   - ADMIN_USER_ID: ваш числовой ID в Telegram
   - LOG_LEVEL: уровень логирования (по умолчанию INFO)
   - DEFAULT_MODEL: модель OpenAI по умолчанию (по умолчанию gpt-4.1-nano)

### Запуск с помощью Docker

Для деплоя используйте скрипт `deploy.sh`:

```bash
chmod +x deploy.sh
./deploy.sh
```

Скрипт выполнит следующие операции:
- Проверит наличие .env файла
- Соберет Docker образ
- Остановит и удалит предыдущий контейнер (если он есть)
- Запустит новый контейнер с вашими переменными окружения

### Проверка логов

Для просмотра логов выполните:

```bash
docker logs -f youtube-summarizer-bot
```

## Как это работает

1. Бот принимает YouTube ссылку от пользователя
2. Проверяет доступ пользователя:
   - Если пользователь является администратором, доступ предоставляется без ограничений
   - Если пользователь имеет одобренный доступ, используется его лимит запросов
   - В противном случае, пользователю будет предложено запросить доступ
3. Извлекает ID видео из ссылки
4. Получает субтитры видео (в первую очередь на предпочитаемом языке пользователя)
5. Отправляет текст субтитров в OpenAI API для суммаризации
6. Возвращает пользователю краткое резюме видео

## Модели и обработка контекста

По умолчанию бот использует модель gpt-4.1-nano, которая имеет контекстное окно до 1 миллиона символов. 
Это позволяет обрабатывать даже очень длинные видео без разделения на части.

Доступные модели и их контекстные окна:
- gpt-3.5-turbo: 4K символов
- gpt-3.5-turbo-16k: 16K символов
- gpt-4: 8K символов
- gpt-4-turbo: 128K символов
- gpt-4o: 128K символов
- gpt-4.1-nano: 1M символов

Бот автоматически определяет оптимальный размер чанка для каждой модели и, при необходимости, 
разделяет длинные тексты на части с учетом границ предложений.

## Администрирование

Администратор бота имеет следующие возможности:
- Одобрять или отклонять запросы доступа от пользователей
- Устанавливать количество запросов для каждого пользователя
- Отзывать доступ у пользователей
- Просматривать список всех пользователей
- Менять используемую модель AI для обработки видео

## Архитектура

- `src/bot/` - Телеграм бот (основная логика и обработчики сообщений)
- `src/config/` - Конфигурация и настройки бота
- `src/models/` - Модели данных и классы для работы с пользователями
- `src/youtube_processor.py` - Получение субтитров из YouTube видео
- `src/summarizer.py` - Суммаризация текста через OpenAI
- `src/ai_agent.py` - AI агент для взаимодействия с OpenAI API
- `src/logger.py` - Настройка логирования
- `src/app.py` - Точка входа в приложение

## Лицензия

MIT 