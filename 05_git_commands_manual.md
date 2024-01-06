- [Git documentation](https://git-scm.com/docs/git#_main_porcelain_commands)

<p align="center">~~~</p>

- [Создание/клонирование Git репозитория](#1)
- [Создать ветку](#2)
- [Запушить ветку](#3)
- [Удалить (local) ветку](#4)
- [Удалить (remote) ветку](#5)
- [Получить информацию о коммитах](#6)
- [Подтянуть состояние закомиченных файлов](#7)
- [Управление незакомиченными изменениями](#8)
- [Слияние двух веток в одну](#9)

---

<h3 id="1" align="center">Создание/клонирование Git репозитория</h3>

Лучший способ запустить новый проект - создать его на GitHub и затем клонировать его.

```sh
# В директорий где будет расположен проект сделать git clone.
git clone {repo_url}
git clone git@github.com:Deyvidas/test_repo.git

# Перейти в директорию проекта.
cd {repo_directory}
cd test_repo
```

<p align="center">~~~</p>

Но бывает и так, что необходимо загрузить проект с локального компа на GitHub.

```sh
# Инициализировать локально Git репозиторий с веткой {branch_name}.
git init -b {branch_name}
git init -b main

# Добавить в индекс все файлы и директории.
git add .

# Зафиксировать состояние всех файлов в индексе.
git commit -m '{some_message}'

# Затем необходимо создать проект на GitHub.
...

# Сохранить ссылку, на GitHub репозиторий с названием в переменную {repo_tag}.
git remote add {repo_tag} {repo_url}
git remote add origin git@github.com:Deyvidas/test_repo.git

# Запушить состояние ветки {branch_name} в репозиторий с тэгом {repo_tag}.
git push {repo_tag} -u {branch_name}
git push origin -u main

# Можно запушить состояние ветки на прямую через {repo_url} но в дальнейшем
# будет сильно не удобно каждый раз вбивать url репозитория, по этому лучше
# все-таки сохранять {repo_url} в переменную.
git push {repo_url} -u {branch_name}
git push git@github.com:Deyvidas/test_repo.git -u main
```

---

<h3 id="2" align="center">Создать ветку</h3>

```sh
# Создать новую ветку с названием {branch_name}.
git branch -c {branch_name}
git branch -c branch1

# Переключиться на ветку {branch_name}.
git switch {branch_name}
git switch branch1

# Создать и сразу-же переключиться на ветку {branch_name}.
git switch -c {branch_name}
git switch -c branch1

# Создать новую ветку с состоянием указанного коммита.
git switch -c {branch_name} {commit}
git switch -c branch1 45e78ad54193a2a7a5c7bbc69c8c3b3959783a24
# Получить список последних {n} коммитов:
git log -n {n}

# Создать новую ветку с состоянием {n} коммитов назад от текущего состояния ветки.
git switch -c {branch_name} @{n}
git switch -c branch1 @{1}
```

---

<h3 id="3" align="center">Запушить ветку</h3>

```sh
# Запушить одну или больше веток в репозитории {repo_tag}.
git push {repo_tag} -u {branch1} ... {branch_n}
git push origin -u branch1 branch2
# Просмотр всех доступных репозиториев {repo_tag}:
git remote -v
```

---

<h3 id="4" align="center">Удалить (local) ветку</h3>

```sh
# Удалить одну или больше веток.
git branch -d {branch1} ... {branch_n}
git branch -d branch1 branch2
```

---

<h3 id="5" align="center">Удалить (remote) ветку</h3>

```sh
# Удалить одну или больше веток из репозитория {repo_tag}.
git push {repo_tag} -d {branch1} ... {branch_n}
git push origin -d branch1 branch2
# Посмотреть какая ветка {branch_name} к какому репозиторию {repo_tag} относиться:
git branch -avv
```

---

<h3 id="6" align="center">Получить информацию о коммитах</h3>

```sh
# Получить информацию обо всех коммитах.
git log

# Получить информацию о последних {n} коммитах.
git log -n {n}
git log -n 2
```

---

<h3 id="7" align="center">Подтянуть состояние закомиченных файлов</h3>

```sh
# Подтянуть состояние из последнего коммита в ветке {branch_name}.
git restore --source={branch_name} --worktree {path1} ... {path_n}
git restore --source=branch1 --worktree ./bar.py ./baz.py
git restore --source=branch1 --worktree .

# Подтянуть состояние из указанного коммита {commit}.
git restore --source={commit} --worktree {path1} ... {path_n}
git restore --source=b70bdc28adc01055c44106347f363b1ccd0ff8d9 --worktree ./bar.py ./baz.py
git restore --source=b70bdc28adc01055c44106347f363b1ccd0ff8d9 --worktree .
```

---

<h3 id="8" align="center">Управление незакомиченными изменениями</h3>

<p align="center">Создание</p>

```sh
# Сохранить все незакомиченные изменения всех директорий/файлов.
git stash

# Сохранить все незакомиченные изменения всех указанных директорий/файлов.
git stash -- {path1} ... {path_n}
git stash -- ./bar.py ./foo.py
```

<p align="center">Просмотр</p>

```sh
# Вывести список всех stash-ей:
git stash list

# Вывести разницу между определенным stash-ом и текущим состоянием.
git stash show -p stash@{stash_id}
git stash show -p stash@{0}
```

<p align="center">Применение</p>

```sh
# Применить изменения в stash-е под индексом {stash_id} С УДАЛЕНИЕМ stash-а.
git stash pop stash@{stash_id}
git stash pop stash@{0}

# Применить изменения в stash-е под индексом {stash_id} БЕЗ УДАЛЕНИЯ stash-а.
git stash apply stash@{stash_id}
git stash apply stash@{0}
```

<p align="center">Удаление</p>

```sh
# Удалить все stash-ы в stash list.
git stash clear

# Удалить stash с индексом {stash_id}.
git stash drop stash@{stash_id}
git stash drop stash@{0}
```

---

<h3 id="9" align="center">Слияние двух веток в одну</h3>

Перед слиянием двух веток желательно, что-бы обе ветки не имели "висящих" изменений.
(Перед слиянием сделать push всех изменений, в обеих ветках)

```sh
# Слить в принимающую ветку все изменения отдающей ветки.
[ user@ubuntu ] [ ~/.../some_project ] [ {accept_branch} ]
$ git merge {give_branch}

# Пример - в ветку 'main' слить ветку 'feature2'.
[ user@ubuntu ] [ ~/.../some_project ] [ main ]
$ git merge feature2
```

Во время слияния могут возникнуть коллизии (конфликты) например:

- в ветке `main` в файле `foo.py:9` содержится строка `def function_of_main():`
- в ветке `feature2` в файле `foo.py:9` содержится строка `def function_of_feature2():`

В данном случае придется выбрать что-то одно, и при этом нужно учитывать, что
данная функция может использоваться где-то и если её переименовать то приложение
может сломаться.

После решения конфликта необходимо закоммитить изменения (после тщательной проверки).

---

<h3 id="-" align="center">-</h3>

```sh
```