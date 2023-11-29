- [Шаг 1 — Установка PostgreSQL](#1)
- [Шаг 2 — Вход в оболочку PostgreSQL](#2)
- [Шаг 3 — Создание пользователя (роли)](#3)
- [Шаг 3.1 — Создание БД под пользователя](#3.1)
- [Шаг 3.2 — Вход в оболочку PostgreSQL новым пользователем](#3.2)
- [Шаг 4 — Изменение пароля, прав и опции существующей роли](#4)
- [Проверка статуса и перезагрузка сервера PostgreSQL](#5)

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="1" align="center">Шаг 1 — Установка PostgreSQL</h3>

Обновить индекс пакетов, установить пакет postgresql вместе с пакетом
postgresql-contrib, который содержит дополнительные утилиты и функциональные
возможности.

```bash
sudo apt update && sudo apt install postgresql postgresql-contrib
```

В ходе установки была создана учетную запись пользователя postgres, которая
связана с используемой по умолчанию ролью postgres.


<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="2" align="center">Шаг 2 — Вход в оболочку PostgreSQL</h3>

Так как после установки postgres, на сервере PostgreSQL зарегистрирована только
одна роль (postgres), то войти в консоль PostgreSQL можно только от имени
данного пользователя, и есть два способа это сделать:

<p align="center">~~~</p>

1 - Сменить пользователя сеанса, на пользователя postgres, затем войти в
консоль PostgreSQL:

```bash
SomeUser@ubuntu:
$ sudo -i -u postgres
```

___(После этой команды - пользователь сеанса смениться с SomeUser на postgres)___

```bash
postres@ubuntu:
$ psql
```

<p align="center">~~~</p>

2 - запустить команду входа в консоль PostgreSQL, от имени пользователя postgres:

```bash
sudo -u postgres psql
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="3" align="center">Шаг 3 — Создание пользователя (роли)</h3>

__Создание новой роли (пользователя) не является необходимым шагом, так как
после установки postgres автоматически создается роль (пользователь) postgres,
которым можно спокойно пользоваться, за одним исключением у пользователя
по умолчанию отсутствует пароль.__

<p align="center">~~~</p>

Перед тем как создать нового пользователя в PostgreSQL нужно __убедиться, в том,
что пользователь с соответствующим именем зарегистрирован в ОС Linux__, и если
соответствующего пользователя Linux не существует, то вы можете создать его с
помощью команды adduser.

- [how to add and delete user - ubuntu](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-18-04)

```bash
sudo adduser some_user
```

<p align="center">~~~</p>

__Создание роли (пользователя) с необходимыми правами:__
- [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html)

```sql
postgres=# CREATE ROLE some_user
                       PASSWORD 'some_password'
                       SUPERUSER
                       CREATEDB
                       CREATEROLE
                       INHERIT
                       LOGIN
                       REPLICATION
                       BYPASSRLS;
```

<p align="center">~~~</p>

__Удаление указанной роли (пользователя):__
- [DROP ROLE](https://www.postgresql.org/docs/current/sql-droprole.html)
- __Важно!__ После удаления роли (пользователя), БД созданная под неё
автоматически не удаляется!

```sql
postgres=# DROP ROLE IF EXISTS some_user;
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="3.1" align="center">Шаг 3.1 — Создание БД под пользователя</h3>

__Для любого пользователя, используемого для входа, должна существовать база
данных с тем же именем, что и имя пользователя. Без неё новый пользователь
не сможет подключиться к серверу PostgreSQL.__

Это означает, что если созданный вами в последнем разделе пользователь будет
иметь имя some_user, эта роль попытается подключиться к базе данных, которая
также называется some_user по умолчанию.

<p align="center">~~~</p>

__Создать базу данных:__
- [CREATE DATABASE](https://www.postgresql.org/docs/current/sql-createdatabase.html)

```sql
postgres=# CREATE DATABASE some_user;
```

<p align="center">~~~</p>

__Удалить указанную базу данных:__
- [DROP DATABASE](https://www.postgresql.org/docs/current/sql-dropdatabase.html)
- __Важно!__ После удаления БД созданной под роль (пользователя), сама роль не
удаляется!

```sql
postgres=# DROP DATABASE IF EXISTS some_user;
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="3.2" align="center">Шаг 3.2 — Вход в оболочку PostgreSQL новым пользователем</h3>

После создания новой роли, на сервере PostgreSQL кроме роли postgres появится
роль (some_user), и теперь войти в консоль PostgreSQL можно не только от имени
postgres но и от имени нового пользователя (some_user), и так-же как и выше есть
два способа это сделать:

<p align="center">~~~</p>

1 - Сменить пользователя сеанса, на пользователя some_user, затем войти в
консоль PostgreSQL:

```bash
OldUser@ubuntu:
$ sudo -i -u some_user
```

___(После этой команды - пользователь сеанса смениться с OldUser на some_user)___

```bash
some_user@ubuntu:
$ psql
```

<p align="center">~~~</p>

2 - запустить команду входа в консоль PostgreSQL, от имени пользователя some_user:

```bash
sudo -u some_user psql
```

<p align="center">~~~</p>

3 - Узнать под каким пользователем мы вошли в консоль PostgreSQL можно по строке
слева от знака =

```sql
some_user=# ...
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="4" align="center">Шаг 4 — Изменение пароля, прав и опции существующей роли</h3>

- [ALTER ROLE](https://www.postgresql.org/docs/current/sql-alterrole.html)
- [Все права и опции роли](https://www.postgresql.org/docs/current/sql-createrole.html)

<p align="center">~~~</p>

Поменять пароль:

```sql
postgres=# ALTER ROLE some_user WITH PASSWORD 'new_password';
postgres=# ALTER ROLE some_user WITH PASSWORD NULL;  -- Отключить пароль;
```

<p align="center">~~~</p>

Поменять права:

```sql
postgres=# ALTER ROLE some_user NOCREATEROLE;  -- Запретить пользователю создавать роли;
postgres=# ALTER ROLE some_user NOCREATEDB;    -- Запретить пользователю создавать БД;
...
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---
<h3 id="5" align="center">Проверка статуса и перезагрузка сервера PostgreSQL</h3>

```bash
sudo systemctl status postgresql
```

```bash
sudo systemctl restart postgresql
```