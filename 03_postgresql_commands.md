- [Вход в консоль psql](#1)
- [Создание дамп файла для БД](#2)

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<table style="font-family:monospace;">
    <tr>
        <td>Get all psql commands</td>
        <td>\?</td>
    </tr>
    <tr>
        <td>Get help</td>
        <td>\h</td>
        <td>\help</td>
    </tr>
    <tr>
        <td>Check postgres version</td>
        <td>SELECT VERSION()</td>
    </tr>
    <tr>
        <td>-</td>
    </tr>
    <tr>
        <td>List all databases</td>
        <td>\l</td>
        <td>\list</td>
    </tr>
    <tr>
        <td>Change database</td>
        <td>\c <i>db_name</i></td>
        <td>\connect <i>db_name</i></td>
    </tr>
    <tr>
        <td>List all tables</td>
        <td>\dt</td>
        <td>\dt+</td>
    </tr>
    <tr>
        <td>Describe all tables</td>
        <td>\d</td>
        <td>\d+</td>
    </tr>
    <tr>
        <td>Describe a specific table</td>
        <td>\d <i>tbl_name</i></td>
        <td>\d+ <i>tbl_name</i></td>
    </tr>
    <tr>
        <td>List all users</td>
        <td>\du</td>
        <td>\du+</td>
    </tr>
    <tr>
        <td>-</td>
    </tr>
    <tr>
        <td>Change output format</td>
        <td>\pset format all</td>
        <td>\pset format <i>format</i></td>
    </tr>
    <tr>
        <td>Save query result in file</td>
        <td>\o <i>file_name</i></td>
    </tr>
    <tr>
        <td>Run psql commands/queries from file</td>
        <td>\i <i>file_name</i></td>
    </tr>
    <tr>
        <td>Show query execution time</td>
        <td>\timing</td>
    </tr>
    <tr>
        <td>-</td>
    </tr>
    <tr>
        <td>List all schemas</td>
        <td>\dn</td>
        <td>\dn+</td>
    </tr>
    <tr>
        <td>List all views</td>
        <td>\dv</td>
        <td>\dv+</td>
    </tr>
    <tr>
        <td>List all functions</td>
        <td>\df</td>
        <td>\df+</td>
    </tr>
    <tr>
        <td>Show commands history</td>
        <td>\s</td>
    </tr>
    <tr>
        <td>Execute previous command</td>
        <td>\g</td>
    </tr>
</table>

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="1" align="center">Вход в консоль psql</h3>

```bash
psql -U some_user -h some_host -d db_name
```

<!-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ -->

---

<h3 id="2" align="center">Создание дамп файла для БД</h3>

- [Backup and Restore](https://www.postgresql.org/docs/8.1/backup.html)

```bash
pg_dump -U some_user -h some_host -d db_name > /path/to/dump.sql
```