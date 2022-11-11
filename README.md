# Проект API для yatube

### Описание:
API для социальной сети, который позволяет пользователям, публиковать записи, комментировать записи, подписываться на авторов.

### Технологии:
Python, Django, DRF, JWT + djoser

### Как запустить проект:

Клонировать репозиторий и перейти в него в командной строке:
```bash
git clone https://github.com/IlianL/api_final_yatube.git
cd api_final_yatube
```

Cоздать и активировать виртуальное окружение:
```bash
python -m venv venv
source venv/scripts/activate
```
Установить зависимости из файла requirements.txt:
```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Выполнить миграции:
```bash
cd yatube_api
python manage.py migrate
```

Запустить проект:
```bash
python3 manage.py runserver
```


## Пример работы API для неавторизованного пользователя:
Неавторизованные пользователи могут только читать контент, они не могут создавать или менять что-то. Для неавторизованных пользователей недоступен эндпоинт follow.

GET http://127.0.0.1:8000/api/v1/posts/ Получение всех записей.\
Пример ответа:
```
{
"text": "string",
"image": "string",
"group": 0
}
```
GET http://127.0.0.1:8000/api/v1/posts/?limit=2&offset=4 Звпрос с параметрами limit  и offset \
Такой GET-запрос вернёт два объекта, с пятого по шестой (или меньше, если в результате запроса менее 6 объектов).\
Пример ответа:
```
{
"count": 123,
"next": "http://127.0.0.1:8000/api/v1/posts/?limit=2&offset=7",
"previous": "http://127.0.0.1:8000/api/v1/posts/?limit=2&offset=2",
"results": [{}]
}
```
GET http://127.0.0.1:8000/api/v1/posts/{id}/ Получение публикации по id. \
Пример ответа:
```
{
"text": "string",
"image": "string",
"group": 0
}
```
GET http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/ Получение всех комментариев для публикации. \
Пример ответа:
```
[
{
"id": 0,
"author": "string",
"text": "string",
"created": "2019-08-24T14:15:22Z",
"post": 0
}
]
```
GET http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/{id}/ Получение комментария к публикации по id комментария. \
Пример ответа:
```
{
"id": 0,
"author": "string",
"text": "string",
"created": "2019-08-24T14:15:22Z",
"post": 0
}

```

GET http://127.0.0.1:8000/api/v1/groups/ Получение списка доступных сообществ. \
Пример ответа:
```
[
  {
    "id": 0,
    "title": "string",
    "slug": "string",
    "description": "string"
  }
]
```
GET http://127.0.0.1:8000/api/v1/groups/{id}/ Получение информации о сообществе по id.\
Пример ответа:
```
{
"id": 0,
"title": "string",
"slug": "string",
"description": "string"
}
```

## Регистрация пользователя:

POST http://127.0.0.1:8000/api/v1/auth/users/ Создаём пользователя.\
Пример запроса:
```
{
"username": "string",
"password": "string"
}
```
Пример ответа:
```
{
    "email": "string",
    "username": "string",
    "id": integer
}
```

POST http://127.0.0.1:8000/api/v1/jwt/create/ Получаем токен.\
Пример запроса:
```
{
"username": "string",
"password": "string"
}
```
Пример ответа:
```
{
"refresh": "string",
"access": "string"
}
```
Для авторизации вы должны передавать ключ access в заголовке, key - "Authorization", value "Bearer и ваше значение access"

POST http://127.0.0.1:8000/api/v1/jwt/refresh/ Обновление JWT-токена.\
refresh получаем во время создания токена по эндпоинту jwt/create/\
Пример запроса:
```
{
"refresh": "refresh value"
}
```
Пример ответа:
```
{
"access": "string"
}
```

POST http://127.0.0.1:8000/api/v1/jwt/verify/ Проверка JWT-токена.\
Пример запроса:
```
{
"access": "string access valuse"
}
```
Пример ответа:
```
{
"token": "string"
}
```


## Пример работы API авторизованного пользователя:
POST http://127.0.0.1:8000/api/v1/posts/ Создание новой записи, поле text - обязательное.
Пример запроса:
```
{
"text": "string, required field",
"image": "string", 
"group": integer 
}
```
Пример ответа:
```
{
"id": 0,
"author": "string",
"text": "string",
"pub_date": "2019-08-24T14:15:22Z",
"image": "string",
"group": 0
}
```
PUT http://127.0.0.1:8000/api/v1/posts/{id}/ Обновление публикации по id. Обновить публикацию может только автор публикации. Анонимные запросы запрещены. \
PATCH http://127.0.0.1:8000/api/v1/posts/{id}/ Частичное обновление публикации по id. Обновить публикацию может только автор публикации. Анонимные запросы запрещены.\
DELETE http://127.0.0.1:8000/api/v1/posts/{id}/ Удаление публикации по id. Удалить публикацию может только автор публикации. Анонимные запросы запрещены.\

POST http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/ Добавление нового комментария к публикации. Анонимные запросы запрещены.\
Пример запроса:
```
{
"text": "string,required field"
}
```
Пример ответа:
```
{
"id": 0,
"author": "string",
"text": "string",
"created": "2019-08-24T14:15:22Z",
"post": 0
}
```
PUT http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/ Обновление комментария к публикации по id. Обновить комментарий может только автор комментария. Анонимные запросы запрещены.\
PATCH http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/ Частичное обновление комментария к публикации по id. Обновить комментарий может только автор комментария. Анонимные запросы запрещены.\
DELETE http://127.0.0.1:8000/api/v1/posts/{post_id}/comments/ Удаление комментария к публикации по id. Обновить комментарий может только автор комментария. Анонимные запросы запрещены.\

GET http://127.0.0.1:8000/api/v1/follow/ Возвращает все подписки пользователя, сделавшего запрос. Анонимные запросы запрещены.\
POTS http://127.0.0.1:8000/api/v1/follow/ Подписка пользователя от имени которого сделан запрос на пользователя переданного в теле запроса. Анонимные запросы запрещены.\
Пример запроса:
```
{
"following": "string author username"
}
```
Пример ответа:
```
{
"user": "stringm your name",
"following": "string, author name"
}
```

Авторизованные пользователи могут:
- Создавать посты, комментировать их и подписываться на других пользователей.
- Изменять(удалять) контент, автором которого они являются.


## Создание группы:
Добавить группу в можно через админ панель Django:

Для этого нужно создать супервользователя
```bash
python manage.py createsuperuser
перейти по адресу
http://127.0.0.1:8000/admin
```

Автор - [Илиан Ляпота](https://github.com/IlianL)



