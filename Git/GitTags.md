# Git Tags

**Tag (тег)** — это имя, которое указывает на определённый объект Git, чаще всего на конкретный commit.

Главное применение тегов — **помечать важные точки в истории проекта**, обычно это версии приложения или релизы.

Например:

```text
A --- B --- C --- D --- E
          ↑
        v1.0.0
```

Здесь `v1.0.0` указывает на commit `C`.

В отличие от branch, tag обычно не перемещается при создании новых коммитов.

---

## 1. Tag и branch

### Branch

Branch указывает на последний commit ветки и перемещается при создании новых коммитов:

```text
A --- B --- C
          ↑
         main
```

Создаём новый commit:

```bash
git commit -m "Add login"
```

Получаем:

```text
A --- B --- C --- D
                  ↑
                 main
```

`main` переместился с `C` на `D`.

### Tag

Tag указывает на конкретный commit и сам по себе не перемещается:

```text
A --- B --- C --- D
          ↑       ↑
       v1.0.0    main
```

После нового commit:

```text
A --- B --- C --- D --- E
          ↑             ↑
       v1.0.0          main
```

`v1.0.0` всё ещё указывает на `C`.

**Запомнить:**

* `branch` — указатель на текущую точку разработки;
* `tag` — метка определённой точки истории.

---

# 2. Как Git понимает, на какой commit ставить tag

Когда мы работаем с Git, `HEAD` указывает на текущее положение.

Например, посмотрим историю:

```bash
git log --oneline
```

Получим:

```text
e52ab91 Add dashboard
93f4a21 Add authentication
71ca123 Initial project
```

Если сейчас мы находимся на ветке `main`, то `HEAD` указывает на последний commit:

```text
71ca123? 
```

В данном примере правильная схема:

```text
71ca123 --- 93f4a21 --- e52ab91
                           ↑
                          HEAD
                           ↑
                          main
```

То есть `HEAD` сейчас указывает на commit `e52ab91`.

Если выполнить:

```bash
git tag v1.0.0
```

Git создаст tag на **текущем commit**, то есть на том, куда сейчас указывает `HEAD`:

```text
71ca123 --- 93f4a21 --- e52ab91
                           ↑
                      v1.0.0
                           ↑
                          HEAD
                           ↑
                          main
```

Поэтому команда:

```bash
git tag v1.0.0
```

означает:

> Создай tag `v1.0.0` на текущем commit.

---

# 3. Как создать tag на конкретном commit

Можно поставить tag не на текущий commit, а на любой существующий commit.

Сначала смотрим историю:

```bash
git log --oneline
```

Например:

```text
e52ab91 Add dashboard
93f4a21 Add authentication
71ca123 Initial project
```

Мы хотим пометить `93f4a21` как версию `v1.0.0`.

Выполняем:

```bash
git tag v1.0.0 93f4a21
```

Теперь:

```text
71ca123 --- 93f4a21 --- e52ab91
             ↑             ↑
          v1.0.0          main
```

Здесь мы явно указали commit:

```text
git tag <tag> <commit>
```

Например:

```bash
git tag v1.0.0 93f4a21
```

---

# 4. Lightweight tag

Самый простой tag создаётся так:

```bash
git tag v1.0.0
```

Такой tag просто указывает на commit.

Схематично:

```text
v1.0.0
   ↓
commit
```

Это называется **lightweight tag**.

---

# 5. Annotated tag

Для релизов обычно используют **annotated tag**.

Создание:

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

Здесь:

* `-a` — создать annotated tag;
* `v1.0.0` — имя tag;
* `-m` — сообщение tag.

Annotated tag содержит дополнительную информацию:

* имя tag;
* автора;
* дату;
* сообщение;
* commit, на который он указывает.

Например:

```text
Tag: v1.0.0
Tagger: John
Date: ...

Release version 1.0.0
```

Для релизов рекомендуется использовать именно annotated tags:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

---

# 6. Посмотреть существующие tags

```bash
git tag
```

Например:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

Чтобы найти tags определённого формата:

```bash
git tag -l "v1.*"
```

Результат:

```text
v1.0.0
v1.1.0
v1.2.0
```

---

# 7. Посмотреть информацию о tag

Используется:

```bash
git show v1.0.0
```

Для annotated tag Git покажет информацию о самом tag и commit:

```text
tag v1.0.0
Tagger: John
Date: ...

Release v1.0.0

commit 93f4a21
Author: John
Date: ...

    Add authentication
```

---

# 8. Посмотреть commit, на который указывает tag

Можно использовать:

```bash
git rev-parse v1.0.0
```

Например:

```text
93f4a21...
```

То есть:

```text
v1.0.0 → 93f4a21
```

Также удобно:

```bash
git show v1.0.0
```

---

# 9. Посмотреть tags вместе с историей

```bash
git log --oneline --decorate
```

Например:

```text
e52ab91 (HEAD -> main) Add dashboard
93f4a21 (tag: v1.0.0) Add authentication
71ca123 Initial project
```

`(tag: v1.0.0)` означает, что commit `93f4a21` помечен этим tag.

---

# 10. Tag не отправляется обычным git push

Очень важный момент.

После создания:

```bash
git tag v1.0.0
```

tag существует **только локально**.

Например:

```text
Локальный репозиторий          GitHub

v1.0.0                         нет
   ↓
commit
```

Обычный:

```bash
git push
```

не обязательно отправит созданный tag.

Чтобы отправить конкретный tag:

```bash
git push origin v1.0.0
```

Теперь:

```text
Локальный репозиторий          GitHub

v1.0.0  ───────────────────→  v1.0.0
```

---

# 11. Отправить все tags

Чтобы отправить все локальные tags:

```bash
git push origin --tags
```

Например, если локально есть:

```text
v1.0.0
v1.1.0
v1.2.0
```

то:

```bash
git push origin --tags
```

отправит их все.

---

# 12. Получить tags с remote

Если tags появились на GitHub после того, как ты уже клонировал репозиторий:

```bash
git fetch --tags
```

Git скачает информацию о tags.

После этого:

```bash
git tag
```

покажет полученные tags.

---

# 13. Удаление tag

Удалить tag локально:

```bash
git tag -d v1.0.0
```

Это удаляет только локальный tag.

Если tag уже был отправлен на GitHub, его нужно удалить отдельно:

```bash
git push origin --delete v1.0.0
```

Таким образом:

```bash
# Локально
git tag -d v1.0.0

# На remote
git push origin --delete v1.0.0
```

---

# 14. Переключение на tag

Можно перейти к состоянию проекта на момент конкретной версии:

```bash
git switch --detach v1.0.0
```

Например:

```text
A --- B --- C --- D
          ↑
       v1.0.0
```

После:

```bash
git switch --detach v1.0.0
```

`HEAD` будет указывать на commit `C`.

```text
A --- B --- C --- D
          ↑
          HEAD
          ↑
       v1.0.0
```

При этом `HEAD` не находится на branch.

Это называется **detached HEAD**.

Если нужно продолжить разработку от этого состояния, можно создать branch:

```bash
git switch -c hotfix
```

Получится:

```text
A --- B --- C --- D
          ↑
       v1.0.0
          ↑
        hotfix
```

---

# 15. Tag не перемещается автоматически

Допустим, мы создали:

```bash
git tag v1.0.0
```

Получилось:

```text
A --- B --- C
          ↑
       v1.0.0
          ↑
         main
```

После нового commit:

```bash
git commit -m "Add profile"
```

получится:

```text
A --- B --- C --- D
          ↑       ↑
       v1.0.0    main
```

Tag остался на `C`.

Если нужно создать новую версию, создаётся новый tag:

```bash
git tag v1.1.0
```

Теперь:

```text
A --- B --- C --- D
          ↑       ↑
       v1.0.0  v1.1.0
```

---

# 16. Изменение существующего tag

Технически tag можно переместить:

```bash
git tag -f v1.0.0 <commit>
```

`-f` означает **force**.

Однако опубликованные release tags лучше не перемещать.

Например, сначала:

```text
v1.0.0 → commit A
```

А затем ты изменил его:

```text
v1.0.0 → commit B
```

Теперь одно и то же имя версии означает другой код.

Это плохо для:

* CI/CD;
* Docker images;
* production;
* воспроизводимости сборок.

Поэтому хорошая практика:

> После публикации release tag не изменять.

Если в `v1.0.0` обнаружили ошибку, обычно создают:

```text
v1.0.0
v1.0.1
```

---

# 17. Semantic Versioning

Tags часто используют для обозначения версий по принципу **Semantic Versioning (SemVer)**:

```text
MAJOR.MINOR.PATCH
```

Например:

```text
v2.4.7
```

Где:

* `2` — MAJOR;
* `4` — MINOR;
* `7` — PATCH.

### PATCH

Исправление ошибок:

```text
v1.2.0 → v1.2.1
```

### MINOR

Добавление новой функциональности без нарушения обратной совместимости:

```text
v1.2.1 → v1.3.0
```

### MAJOR

Несовместимые изменения:

```text
v1.3.0 → v2.0.0
```

Также могут использоваться pre-release версии:

```text
v2.0.0-alpha
v2.0.0-beta
v2.0.0-rc.1
v2.0.0
```

---

# 18. Tag и GitHub Release

**Tag и GitHub Release — разные вещи.**

Tag является частью Git:

```text
commit
   ↓
tag v1.0.0
```

GitHub Release — это функция GitHub, которая может быть создана на основе tag:

```text
commit
   ↓
tag v1.0.0
   ↓
GitHub Release
   ↓
Release notes
   ↓
files / assets
```

То есть tag можно создать и использовать без GitHub Release.

---

# 19. Tags в CI/CD

Tags особенно полезны в DevOps.

Например, GitHub Actions можно настроить так, чтобы workflow запускался при push tag:

```yaml
on:
  push:
    tags:
      - 'v*'
```

Теперь:

```bash
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin v1.2.0
```

может запустить pipeline:

```text
Git tag v1.2.0
      ↓
CI/CD
      ↓
Tests
      ↓
Security scans
      ↓
Build
      ↓
Docker image
      ↓
Deploy
```

Например:

```text
v1.2.0
   ↓
myapp:1.2.0
   ↓
Production
```

Таким образом tag может однозначно связывать:

```text
исходный код
    ↓
версию
    ↓
Docker image
    ↓
production deployment
```

---

# 20. Полный пример от создания проекта до release

Предположим, у нас новый проект.

Создаём репозиторий:

```bash
git init
```

Добавляем файлы:

```bash
git add .
```

Создаём первый commit:

```bash
git commit -m "Initial release"
```

Проверяем историю:

```bash
git log --oneline
```

Получаем:

```text
a31f82c Initial release
```

Сейчас:

```text
a31f82c
   ↑
  HEAD
   ↑
  main
```

Создаём annotated tag:

```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

Теперь:

```text
a31f82c
   ↑
   ├── main
   └── v1.0.0
```

Отправляем branch:

```bash
git push origin main
```

Отправляем tag:

```bash
git push origin v1.0.0
```

Теперь и branch, и tag находятся на remote:

```text
Local                         GitHub

main ─────────────────────→ main
v1.0.0 ───────────────────→ v1.0.0
```

После дальнейшей разработки:

```text
A --- B --- C --- D
          ↑       ↑
       v1.0.0    main
```

Когда готов новый релиз:

```bash
git tag -a v1.1.0 -m "Release v1.1.0"
git push origin v1.1.0
```

Получаем:

```text
A --- B --- C --- D
          ↑       ↑
       v1.0.0  v1.1.0
```

---

# 21. Полезные команды

| Команда                           | Назначение                                          |
| --------------------------------- | --------------------------------------------------- |
| `git tag`                         | Показать tags                                       |
| `git tag v1.0.0`                  | Создать lightweight tag на текущем commit           |
| `git tag -a v1.0.0 -m "..."`      | Создать annotated tag                               |
| `git tag v1.0.0 <commit>`         | Создать tag на конкретном commit                    |
| `git show v1.0.0`                 | Показать информацию о tag                           |
| `git rev-parse v1.0.0`            | Получить hash объекта, связанного с tag             |
| `git log --decorate`              | Показать tags в истории                             |
| `git tag -d v1.0.0`               | Удалить локальный tag                               |
| `git push origin v1.0.0`          | Отправить конкретный tag                            |
| `git push origin --tags`          | Отправить все tags                                  |
| `git fetch --tags`                | Получить tags с remote                              |
| `git push origin --delete v1.0.0` | Удалить tag с remote                                |
| `git switch --detach v1.0.0`      | Перейти к состоянию проекта на tag                  |
| `git describe --tags`             | Показать ближайший tag относительно текущего commit |

---