## Краткое описание
Изза небольшого размера базы данных было решено использовать всроенную функцию резервного копирования `pd_dumpall`, а так же хранилище `Yandex Disc`.


`pg_dumpall` — это утилита `PostgreSQL` для создания резервных копий всех баз данных кластера, включая глобальные объекты (роли, табличные пространства).
Основные возможности :

Резервирует все базы данных кластера одной командой.

Сохраняет глобальные настройки (например, пользователей и права доступа).

Восстанавливается через `psql`, подходит для миграции или полного восстановления.

Требует прав суперпользователя `PostgreSQL`.

Используется для полноты бэкапа, особенно если в кластере несколько баз данных.


***
## Создание `backup` внутри контейнера.

`docker cp zubaev:/backup_all.sql /home/magomed/backup/backup_all.sql`

- выполняем резервное копирование всех баз данных `PostgreSQL` из контейнера `Docker`:

- `docker exec zubaev` — запускает команду внутри контейнера с именем `zubaev`.
- `pg_dumpall` — утилита для полного бэкапа кластера (все базы + глобальные объекты).
- `-U magamed` — подключение к `PostgreSQL` под пользователем `magamed` (должен иметь права суперпользователя).
- `-f /backup_all.sql` — сохраняет дамп в файл `/backup_all.sql` внутри контейнера .
***
## Копируем файл дампа из контейнера на хост.

`docker cp zubaev:/backup_all.sql home/magomed/backup/backup_all.sql`

![image](https://github.com/user-attachments/assets/b14f2969-9c5f-4757-a21f-838664e8d42a)

Файл удачно создан:

![image](https://github.com/user-attachments/assets/44d98ba0-4d5d-4502-9b7f-2e7ff8645f14)

Данные в Postgres во время создания backup

![image](https://github.com/user-attachments/assets/ae373e78-730e-4794-b0b1-7905ff8662f5)]

Удаляем таблицы, а так же пользователей.

![image](https://github.com/user-attachments/assets/77ca681a-3abe-41da-a7a8-f51aeb0e20e2)

***
## Востановление базы данных


***
Копируем файл дампа с хоста обратно в контейнер

`docker cp home/magomed/backup/backup_all.sql zubaev:/backup_all.sql`

![image](https://github.com/user-attachments/assets/841b3fae-2229-4340-b4a3-217ea13e9caa)

Восстанавливаем дамп через psql внутри контейнера

`docker exec zubaev psql -U magamed -d story_db -f /backup_all.sql`

Используем пользователя magamed (должен иметь права суперпользователя)

![image](https://github.com/user-attachments/assets/99eec0ca-53fc-4c01-8789-78498bfefba7)

Все данные востановлены

![image](https://github.com/user-attachments/assets/b013ca5c-6639-486c-99c0-b8ab78c18566)


***
## Копируем backup на Yandex Disc

`rclone copy /home/magomed/backup/backup_all.sql yandex:/backup/`

![image](https://github.com/user-attachments/assets/45eb9f35-00eb-463c-b899-cb3fa57d00cc)
***
