# Хранилище топов мероприятий

Данный документ содержит описание работы и информацию о развертке микросервиса-обертки для хранилища топов мероприятий пользователей

Название сервиса: `top_das`

Структура сервиса:

| Файл                 | Описание                                                        |
| -------------------- | --------------------------------------------------------------- |
| `top_das.py`         | Код микросервиса                                                |
| `config.yml`         | Конфигурационный файл со строкой подключения к RabbitMQ и Redis |
| `run.sh`             | Файл для запуска сервиса из Docker контейнера                   |
| `requirements.txt`   | Верхнеуровневые зависимости                                     |
| `Dockerfile`         | Описание сборки контейнера сервиса                              |
| `docker-compose.yml` | Изолированная развертка сервиса вместе с (RabbitMQ, Redis)      |
| `README.md`          | Описание микросервиса                                           |

## API

### RPC

Обновить топ мероприятий пользователя:

```bat
n.rpc.top_das.update_top(user_id, new_top)

Args:
    user_id,
    new_top - list of IDs [id1, id2, ..] of top events
Returns nothing
```

Получить топ мероприятий пользователя:

```bat
n.rpc.top_das.get_top(user_id)

Args: user_id
Return: list of IDs [id1, id2, ..] of top events
```

Удалить мероприятия (например, прошедшие):

```bat
n.rpc.top_das.delete_events(event_ids)

Args: event_ids - list of ids of events should be deleted
Returns nothing
```

### HTTP

Обновить топ мероприятий пользователя:

```rst
POST http://localhost:8000/update HTTP/1.1
Сontent-Type: application/json

{
    "user": <user_id>,
    "top": ['event_1_id', ..., 'event_n_id']
}
```

Получить топ мероприятий пользователя:

```rst
GET http://localhost:8000/top HTTP/1.1
```

Удалить мероприятия (например, прошедшие):

```rst
DELETE http://localhost:8000/delete HTTP/1.1
Сontent-Type: application/json

{
    [id_1, id_2, ..., id_n]
}
```

## Развертывание и запуск

### Локальный запуск

Для локального запуска микросервиса требуется запустить контейнер с RabbitMQ и Redis.

```bat
docker-compose up -d
```

Затем из папки микросервиса вызвать

```bat
nameko run top_das
```

Для проверки `rpc` запустите в командной строке:

```bat
nameko shell
```

После чего откроется интерактивная Python среда. Обратитесь к сервису одной из команд, представленных выше в разделе `rpc`

### Запуск в контейнере

Чтобы запустить микросервис в контейнере вызовите команду:

```bat
docker-compose up
```

> если вы не хотите просмотривать логи, добавьте флаг `-d` в конце

Микросервис запустится вместе с RabbitMQ и Redis в контейнерах.
