Вот пример файла `README.md`, который описывает все функции и параметры системы:

```markdown
# Система видеонаблюдения с обнаружением движения и объектов

Это система видеонаблюдения, которая использует камеры с RTSP-потоками для обнаружения движения и объектов. Система может отправлять уведомления в Telegram, записывать видео и фото, а также отображать обработанное видео в реальном времени.

## Основные функции

1. **Обнаружение движения**:
   - Система анализирует кадры и обнаруживает движение на основе разницы между текущим и предыдущим кадрами.
   - Порог чувствительности к движению можно настроить.

2. **Обнаружение объектов**:
   - Используется модель YOLOv8 для обнаружения объектов (например, людей, автомобилей, животных).
   - Можно настроить типы объектов для обнаружения и порог уверенности.

3. **Запись видео и фото**:
   - При обнаружении движения или объектов система может записывать видео и делать снимки.
   - Видео записывается с учетом буфера кадров, что позволяет захватить события до обнаружения.

4. **Отправка уведомлений в Telegram**:
   - Система может отправлять фото и видео в Telegram при обнаружении движения или объектов.
   - Можно настроить чат для уведомлений.

5. **Отображение обработанного видео**:
   - Система может отображать видео с наложенными результатами обработки (рамки вокруг объектов, зоны движения).

6. **Поддержка нескольких камер**:
   - Система поддерживает работу с несколькими камерами одновременно.
   - Для каждой камеры можно настроить индивидуальные параметры.

## Установка и запуск

1. Установите зависимости:
   ```bash
   pip install -r requirements.txt
   ```

2. Настройте конфигурационные файлы:
   - `config/cameras.json` — настройки камер.
   - `config/telegram.json` — настройки Telegram.

3. Запустите систему:
   ```bash
   python main.py
   ```

## Конфигурационные файлы

### `config/cameras.json`

Пример конфигурации для камер:

```json
[
  {
    "id": 1,
    "name": "Балкон",
    "rtsp_url": "rtsp://192.168.2.5:554/user=video_password=video_channel=0_stream=1&onvif=0.sdp?real_stream",
    "width": 800,
    "height": 448,
    "enabled": true,
    "detect_motion": true,
    "detect_objects": true,
    "object_types": ["dog", "cat", "person", "bird", "car"],
    "object_confidence": 0.2,
    "telegram_chat_id": "-1002267390790",
    "send_photo": true,
    "send_video": true,
    "draw_boxes": true,
    "codec": "hevc",
    "test_frame_on_start": true,
    "test_video_on_start": true,
    "motion_sensitivity": 1,
    "record_audio": true,
    "reconnect_attempts": 3,
    "reconnect_delay": 10,
    "show_live_feed": false
  }
]
```

#### Параметры камеры:

- **id**: Уникальный идентификатор камеры.
- **name**: Название камеры.
- **rtsp_url**: URL RTSP-потока камеры.
- **width**: Ширина кадра (опционально).
- **height**: Высота кадра (опционально).
- **enabled**: Включена ли камера.
- **detect_motion**: Включить обнаружение движения.
- **detect_objects**: Включить обнаружение объектов.
- **object_types**: Типы объектов для обнаружения (например, ["person", "car"]).
- **object_confidence**: Порог уверенности для обнаружения объектов (от 0 до 1).
- **telegram_chat_id**: ID чата в Telegram для отправки уведомлений.
- **send_photo**: Отправлять фото при обнаружении движения или объектов.
- **send_video**: Отправлять видео при обнаружении движения или объектов.
- **draw_boxes**: Рисовать рамки вокруг обнаруженных объектов.
- **codec**: Кодек для записи видео (например, "h264" или "hevc").
- **test_frame_on_start**: Захватить и отправить тестовый кадр при запуске.
- **test_video_on_start**: Записать и отправить тестовое видео при запуске.
- **motion_sensitivity**: Чувствительность к движению (чем меньше, тем чувствительнее).
- **record_audio**: Записывать аудио в видео.
- **reconnect_attempts**: Количество попыток переподключения при ошибках.
- **reconnect_delay**: Задержка между попытками переподключения (в секундах).
- **show_live_feed**: Отображать обработанное видео в реальном времени.

### `config/telegram.json`

Пример конфигурации для Telegram:

```json
{
  "bot_token": "YOUR_TELEGRAM_BOT_TOKEN"
}
```

#### Параметры Telegram:

- **bot_token**: Токен вашего Telegram-бота.

## Основные классы и функции

### `CameraHandler`

Класс для обработки видео с камеры.

#### Основные методы:

- **`__init__(self, camera_config, telegram_config)`**:
  Инициализация камеры с настройками из `camera_config` и `telegram_config`.

- **`run_async(self)`**:
  Основной цикл обработки кадров. Запускает захват и обработку видео.

- **`capture_frame_async(self)`**:
  Асинхронный захват кадра с помощью FFmpeg.

- **`show_processed_feed(self)`**:
  Отображение обработанного видео в реальном времени.

- **`record_video_with_buffer(self, duration=6)`**:
  Запись видео с учетом буфера кадров (10 секунд до обнаружения движения).

- **`send_telegram_photo_async(self, frame, message="Движение обнаружено!")`**:
  Асинхронная отправка фото в Telegram.

- **`send_telegram_video_async(self, video_path, message="Движение обнаружено!")`**:
  Асинхронная отправка видео в Telegram.

- **`stop(self)`**:
  Остановка потока обработки камеры.

### `ObjectDetector`

Класс для обнаружения объектов с использованием YOLO.

#### Основные методы:

- **`__init__(self, config_path, weights_path, classes_path)`**:
  Инициализация детектора объектов с использованием YOLO.

- **`detect_objects(self, frame)`**:
  Обнаружение объектов на кадре с помощью YOLO.

## Зависимости

- `opencv-python-headless>=4.5.5`
- `numpy>=1.21.0`
- `requests>=2.26.0`
- `aiohttp>=3.8.1`
- `ultralytics>=8.0.0`
- `ffmpeg-python>=0.2.0`

## Лицензия

Этот проект распространяется под лицензией MIT. См. файл `LICENSE` для получения дополнительной информации.
```

### Описание структуры:
1. **Введение**: Краткое описание системы и её возможностей.
2. **Установка и запуск**: Инструкции по установке зависимостей и запуску системы.
3. **Конфигурационные файлы**: Описание параметров для `cameras.json` и `telegram.json`.
4. **Основные классы и функции**: Описание ключевых классов и их методов.
5. **Зависимости**: Список необходимых библиотек.
6. **Лицензия**: Информация о лицензии.

Этот `README.md` предоставляет полное описание системы, её функций и параметров, что поможет пользователям быстро разобраться в работе системы.
