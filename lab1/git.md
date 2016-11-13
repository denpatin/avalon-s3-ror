Основные команды для работы с Git
=============================

## git config

Перед тем, как работать с Git'ом, необходимо его персонализировать. Для этого нужны ваше имя и email. Выполните для задания этих значений две команды:
```
git config --global user.name "YOUR NAME"
git config --global user.email YOUR@EMAIL.COM
```

Без этих данных Git, конечно, будет работать, но ваши коммиты не будут нести информацию об авторе, а без неё Github не сможет соотнести вас с вашей учётной записью.

## git init

Создаёт основу репозитория, а именно в текущей директории, откуда запущена команда, создаётся служебная директория `.git`, в которой Git будет хранить данные об изменениях в проекте.

Примерный вывод команды, если её запустить из директории `/Users/denpatin/test`:
```
Initialized empty Git repository in /Users/denpatin/test/.git/
```

## git clone

Если вы начинаете проект не с нуля, а берёте, например, с Github'а, можно воспользоваться этой командой. Параметром она принимает (в случае с Github'ом) ссылку на репозиторий.

Например, `git clone https://github.com/denpatin/avalon-s3-ror` создаст в текущей директории директорию `avalon-s3-ror` (по последнему слову в URL) и в неё склонирует все изменения, которые есть на Github. Скрытая директория `.git` тоже будет.

Примерный вывод команды:
```
Cloning into 'avalon-s3-ror'...
remote: Counting objects: 31, done.
remote: Compressing objects: 100% (27/27), done.
remote: Total 31 (delta 4), reused 10 (delta 0), pack-reused 0
Unpacking objects: 100% (31/31), done.
```

## git status

Узнать статус рабочей копии.

Если изменений не было, то вывод примерно такой:
```
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working tree clean
```

Для дальнейшей работы давайте сделаем некоторые изменения:

* изменим файл `linux.md`
* удалим файл `ssh-key.md`
* добавим файл `gpg-key.md`

По команде `git status` вывод будет уже примерно таким:
```
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   lab1/linux.md
	deleted:    lab1/ssh-key.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	lab1/gpg-key.md

no changes added to commit (use "git add" and/or "git commit -a")
```

## git add

Когда в проекте сделаны изменения, надо «сообщить» Git'у о том, что надо взять их под версионный контроль. Командой `git add .` можно добавить все сделанные изменения под контроль Git'а.

Например, сделаем `git add .` на изменениях, которые мы сделали выше:
```
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   lab1/gpg-key.md
	modified:   lab1/linux.md
	deleted:    lab1/ssh-key.md
```

Теперь Git знает об изменениях, но они ещё не зафиксированы.

> PS. Не обязательно добавлять все файлы под контроль Git'а. Как параметр команды, `.` означает, что мы добавляем под контроль Git'а всю текущую директорию. Но если нам нужно добавить только несколько файлов, вместо `.` надо их перечислить.

## git commit

Фиксирует изменения, т.е. создаёт некую точку доступа к сделанным изменениям. Каждый коммит имеет уникальный хэш, по которому он доступен. Каждый коммит должен также быть с сообщением; добавляется оно после ключа `-m`:
```
git commit -m "Modified files for lab 1"
```

Выводом на эту команду будет следующее:
```
[master 90649c0] Modified files for lab 1
 3 files changed, 15 deletions(-)
 create mode 100644 lab1/gpg-key.md
 delete mode 100644 lab1/ssh-key.md
```

`90649c0` — и есть тот самый хэш коммита, точнее, его часть.

Git также указывает, сколько файлов было изменено, что было создано, что удалено. Очень подробно.

## git log

Вывести информацию о последних коммитах. В случае с моим репозиторием вывод примерно следующий:

```
commit 90649c03b056819abb7a4d94d6b5915561ee142c
Author: Den Patin <denpatin@gmail.com>
Date:   Sun Nov 13 22:44:33 2016 +0300

    Modified files for lab 1

commit 591e2cebbeeb02aa3747ff4a4c7595a85556dee7
Author: Den Patin <denpatin@gmail.com>
Date:   Sun Nov 13 15:32:08 2016 +0300

    Update ssh-key.md

commit 6271566a88b5fb374b4f0768d30be8ea8271fa92
Author: Den Patin <denpatin@gmail.com>
Date:   Sun Nov 13 15:31:46 2016 +0300

    Create ssh-key.md
```

Каждый коммит имеет, повторяюсь, хэш, автора, дату, когда он был сделан, и название.

## git push

Отправляет коммиты в remote-репозиторий (в нашем случае — Github).

Если репозиторий не настроен, его надо добавить:
```
git remote add origin https://github.com/YOUR_LOGIN/YOUR_REPO
```

Проверить, что репозиторий добавлен, можно командой `git remote -v`.

Отправить данные можно командой `git push origin master`, с учётом, что remote-репозиторий называется `origin` и вы находитесь на ветке `master`.

## git pull

Данные можно как отправлять на remote-репозиторий, так и получать их. Делается это противоположной командой — `git pull origin master`, например.

> NB! Перед тем, как делать эту команду, обязательно проверьте `git status`'ом, что у вас чистая рабочая копия, иначе могут возникнуть конфликты изменений файлов!

## git fetch

Эта команда является частью команды `git pull`. Точнее, `git pull` состоит из `git fetch` («изъять все изменения с remote-репозитория») и `git merge` («применить изъятые изменения к текущему состоянию»). Эта команда безопаснее `git pull`'а, для неё не требуется наличие чистой рабочей копии.

С ключом `--all` — то есть `git fetch --all` — команда изымает из remote-репозитория изменения не с определённой ветки, а со всех сразу, включая установленные теги.

## git checkout

Перейти на другую ветку (например, на ветку `test`): `git checkout test`.

Чтобы перейти на новую ветку (т.е. создать новую ветку и сразу перейти на неё): `git checkout -b new_branch`.

Чтобы отменить изменения, сделанные в рабочей копии (например, отменить изменения в файле `linux.md` и отменить удаление файла `ssh-key.md`, т.е. вернуть его):
```
git checkout -- lab1/linux.md lab1/ssh-key.md
```

> NB! Эти изменения реально отменить только до того, как они зафиксированы (до выполнения команды `git commit`).

## git reset

(Продолжая предыдущий пункт...)  
Если же изменения были закоммичены, но их надо отменить, есть команда `reset`.

Например, мы знаем, что надо отменить только _самый последний_ коммит (информация о коммите удалится, а в рабочую копию вернутся изменённый файлы, т.е. восстановится состояние, которое было до `git commit`):
```
git reset HEAD~1
```

Примерный вывод результатов работы команды:
```
Unstaged changes after reset:
M	lab1/linux.md
D	lab1/ssh-key.md
```
