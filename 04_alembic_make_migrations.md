- [Генерация папок и скриптов для выполнения миграций](#1)
- [Указание url для подключения к БД вовремя миграций](#2)
- [Передача переменных в alembic.ini через Python](#2.1)
- [Передача метаданных о таблицах или ОРМ моделях](#3)
- [Генерация миграции](#4)
- [Применение/выполнение миграции](#5)

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="1" align="center">Генерация папок и скриптов для выполнения миграций</h3>

```bash
alembic init directory_name
```

```text
./
├── directory_name/
│   ├── versions/       # Директория в которой хранятся все миграции.
│   ├── env.py          # Файл через который передаются переменные в alembic.ini.
│   ├── README
│   └── script.py.mako
└── alembic.ini         # Файл конфигурации alembic.
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="2" align="center">Указание url для подключения к БД вовремя миграций</h3>

__alembic.ini__

```text
~ 63 | sqlalchemy.url = driver://user:pass@localhost/dbname
```

Данный параметр, необходим для указания url адреса, через который alembic будет
подключаться к БД, во время создания/выполнения миграций. Но так как там
указываются конфиденциальные данные, их лучше передавать через переменные
окружения, для этого нужно изменить значение.

__Например:__

```text
~ 63 | sqlalchemy.url = %(DB_URL)s
```

```text
~ 63 | sqlalchemy.url = %(DRIVER)s:%(DB_API)://%(DB_USER)s:%(DB_PASS)s@%(DB_HOST)s:%(DB_PORT)s/%(DB_NAME)s
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="2.1" align="center">Передача переменных в alembic.ini через Python</h3>

После чего, необходимо передать переменную/ые из переменных окружения, это
делается в файле env.py:

```python
~ 10 | config = context.config
```

В этой переменной хранится объект через который можно взаимодействовать с файлом
alembic.ini через Python:

```python
~ 10 | config = context.config
~ 11 | 
~ 12 | section = config.config_ini_section  # Создание секции.
~ 13 | config.set_section_option(
~ 14 |     section=section,                 # Указание секции.
~ 15 |     name='DB_URL',                   # Указываем имя переменной в alembic.ini.
~ 16 |     value=settings.DB_URL,           # Указываем значение которое будет присвоено.
~ 17 | )
```
__В value можно помещать как литералы, так и методы или функции (главное что-бы
они возвращали какое-то значение).__

Для передачи более одной переменной используется следующий синтаксис:

```python
~ 10 | config = context.config
~ 11 | 
~ 12 | section = config.config_ini_section
~ 13 | config.set_section_option(section, 'VAR1', VAL1)
~ 14 | config.set_section_option(section, 'VAR2', VAL2)
~ 15 | config.set_section_option(section, 'VAR3', VAL3)
~ 16 | ...
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="3" align="center">Передача метаданных о таблицах или ОРМ моделях</h3>

Когда alembic будет выполнять/генерировать миграции он будет сравнивать текущее
состояние БД с метаданными в sqlalchemy, для этого нужно передать основной
объект в котором хранятся все метаданные о таблицах или ОРМ моделях:

```python
~ 21 | target_metadata = None
```

В данном примере у меня ОРМ модели по этому я указываю Base(DeclarativeBase)
(так как от неё наследуются все остальные модели и соответственной в ней точно
хранятся все метаданные о моделях)

```python
~  6 | from ... import Base
 ... | ...
~ 21 | target_metadata = Base.metadata
```

__Все необходимые параметры модуля alembic настроены!__

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="4" align="center">Генерация миграции</h3>

__ВАЖНО! Перед выполнением миграций БД должна быть синхронизирована с
метаданным sqlalchemy__!

```bash
alembic revision --autogenerate -m 'Created client and status_desc tables.'
```

`-m 'message'` - Флаг для комментария к миграции, как `git commit -m 'message'`

После данной команды если не произошло исключений:
- в директорий migrations/version появится новый Python файл с описанием
  миграции (файл можно переименовать);
- в БД появится новая таблица (она пока пустая так как никаких миграций мы еще
  не выполнили).

<details><br><summary><b>Таблица alembic_version</b></summary>

```sql
banking=# select * from alembic_version;
```

|version_num|
|-----------|
|.|

<hr style="margin-left: 25%; margin-right: 25%;"></details>

<details><br><summary><b>7da164889db9_created_client_and_status_desc_tables.py</b></summary>

```python
"""Created client and status_desc tables.

Revision ID: 7da164889db9
Revises: 
Create Date: 2023-12-02 00:40:28.932545

"""
from typing import Sequence, Union

from alembic import op
import sqlalchemy as sa
from sqlalchemy.dialects import postgresql

# revision identifiers, used by Alembic.
revision: str = '7da164889db9'
down_revision: Union[str, None] = None
branch_labels: Union[str, Sequence[str], None] = None
depends_on: Union[str, Sequence[str], None] = None


def downgrade() -> None:
    op.drop_table('client')
    op.drop_table('status_desc')


def upgrade() -> None:
    op.create_table(
        'status_desc',
        sa.Column('status', sa.INTEGER(), server_default=sa.text("nextval('status_desc_status_seq'::regclass)"), autoincrement=True, nullable=False),
        sa.Column('description', sa.VARCHAR(length=100), autoincrement=False, nullable=False),
        sa.PrimaryKeyConstraint('status', name='status_desc_pkey'),
        postgresql_ignore_search_path=False
    )
    op.create_table(
        'client',
        sa.Column('client_id', sa.INTEGER(), autoincrement=True, nullable=False),
        sa.Column('full_name', sa.VARCHAR(length=255), autoincrement=False, nullable=False),
        sa.Column('reg_date', postgresql.TIMESTAMP(), autoincrement=False, nullable=False),
        sa.Column('doc_num', sa.VARCHAR(length=10), autoincrement=False, nullable=False),
        sa.Column('doc_series', sa.VARCHAR(length=10), autoincrement=False, nullable=False),
        sa.Column('phone', sa.VARCHAR(length=10), autoincrement=False, nullable=False),
        sa.Column('VIP_flag', sa.BOOLEAN(), autoincrement=False, nullable=False),
        sa.Column('birth_date', postgresql.TIMESTAMP(), autoincrement=False, nullable=False),
        sa.Column('sex', postgresql.ENUM('MALE', 'FEMALE', name='sex'), autoincrement=False, nullable=False),
        sa.Column('status', sa.INTEGER(), autoincrement=False, nullable=False),
        sa.ForeignKeyConstraint(['status'], ['status_desc.status'], name='client_status_fkey', ondelete='CASCADE'),
        sa.PrimaryKeyConstraint('client_id', name='client_pkey')
    )

```

<hr style="margin-left: 25%; margin-right: 25%;"></details><br>

__ВАЖНО! Лучше перепроверить содержание и логику миграции, так как иногда бывают
неточности например downgrade() и upgrade() меняются местами.__

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="5" align="center">Применение/выполнение миграции</h3>

Для выполнения миграции нужно указать ID миграции (строка из 12 символов)

```bash
alembic upgrade 7da164889db9
```

Для отката:

```bash
alembic downgrade ...
```

После выполнения и применения миграции в alembic_version запишется примененная
миграция её ID.

```sql
banking=# select * from alembic_version;
```

| version_num  |
|--------------|
| 7da164889db9 |
