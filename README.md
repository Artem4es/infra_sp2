### Что это за проект?:smiley_cat:
Всё просто: Api_YaMDb - это бэкенд для сбора отзывов и оценок о произведениях (книги, фильмы, музыка) + API реализованный на Django Rest Framework.
Он предоставляет интерфейс для удобного взаимодействия с бизнес-логикой сервиса в формате JSON.

### Как заполнять файл .env и зачем? :alien:
.env - это файл, содержащий данные для запуска проекта, такие как имя базы данных, пароль от неё и прочее. Для старта проекта необходимо создать файл .env в директории ```infra_sp2/infra/``` либо после запуска проекта использовать переменные среды. Создайте файл со следующим наполнением

```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=имя базы
POSTGRES_USER=имя пользователя
POSTGRES_PASSWORD=пароль от базы
DB_HOST=db или на Ваш выбор
DB_PORT=5432 или на Ваш выбор
```

### Как запустить проект в Docker? :dizzy:
- Откройте терминал Linux, перейдите в папку, куда нужно загрузить проект
- Выполните команду ```docker pull artem4es/apiyamdb:v1```
- Перейдите в папку ```/infra_sp2/infra/```
- Выполните команду ```docker-compose up -d --build```
- После этого проект будет доступен по адресу http://localhost/
- Чтобы работали файлы статики и было доступен полный функционал выполните команды применения миграции и сбора статики:
```
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py createsuperuser
docker-compose exec web python manage.py collectstatic --no-input 
```
### Заполнение БД своими данными :hole:
После проведения миграций БД можно наполнить данными из файла формата json. Для этого:
- Cкопируйте файл из вашей среды в контейнер с Django:
```docker cp <название файла.json> <ID контейнера>:/app/```
- Заполните БД:
```docker-compose exec web python manage.py loaddata <название файла>.json```

### Остановка проекта
```docker-compose down -v```

### Начало взаимодействия с API :old_key:
После запуска проекта, в версии V1 доступны для GET-запросов все [эндпоинты](http://127.0.0.1:8000/api/v1/) кроме списка пользователей http://127.0.0.1:8000/api/v1/users/. (Доступен только администратору)
Для получения полного доступа к интерфейсу необходимо: 

1. Зарегистровать нового пользователя, отправив POST запрос 
с именем пользователя и почтой в формате:

```
{
"email": "user@example.com",
"username": "string"
}
```
на адрес http://127.0.0.1:8000/api/v1/auth/signup/. В ответ на указанную почту придёт письмо с кодом активации.

2. Получить JWT-токен, отправив POST запрос в формате: 
```
{
"username": "string",
"confirmation_code": "string"
}
```
на эндпоинт http://127.0.0.1:8000/api/v1/auth/token/. В ответ придёт токен в формате JSON.
Токен из строки "token" необходимо отправлять в headers запроса с ключом Authorization. Значение ключа в виде Bearer "ваш токен без ковычек".
Срок действия токена - 24 часа. Необходимо обновление по истечении срока.

### Документация проекта: :blue_book:
После запуска проекта документация со списком эндпоинтов доступна по ссылке:
http://127.0.0.1:8000/redoc
