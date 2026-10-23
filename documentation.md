WriScroll Language — Documentation
Версия: 1.0.0 (2.6.0)
Дата: 20-09-2026
Автор: Longinov Denis, Tashkent
Лицензия: MIT

Этот файл — сводная документация для интерпретатора WriScroll.

export const WRISCROLL_DOCUMENTATION = `
# WriScroll Language — Documentation

**Версия:** 1.0.0 (2.6.0)
**Дата:** 20-09-2026
**Автор:** Longinov Denis, Tashkent
**Лицензия:** MIT

---

## 1. Overview

### 1.1. Что такое WriScroll

WriScroll — маркерный DSL (mDSL — Marker Domain-Specific Language). Каждая инструкция начинается с маркера, который определяет её категорию: состояние (`::`), действие (`>>`) или проверка (`?>`).

Маркер стоит в начале строки. Он виден сразу. Он не требует контекста.

### 1.2. Три категории

| Маркер | Категория | Смысл                                |
|--------|-----------|--------------------------------------|
|  `::`  | Состояние | Создать / настроить / изменить       |
|  `>>`  | Действие  | Выполнить / получить / запустить     |
|  `?>`  | Проверка  | Проверить / утвердить / валидировать |

Три категории отвечают на три вопроса:

- `::` — **что есть?**
- `>>` — **что происходит?**
- `?>` — **что истинно?**

Это не синтаксис. Это **онтология программы**. Любая программа — это ответы на эти три вопроса. WriScroll делает ответы видимыми.

### 1.3. Философия маркеров

Маркер отвечает на вопрос **«ЧТО Я ДЕЛАЮ?»** до того, как читатель разберёт аргументы операции.

Это не украшение. Это **семантическая классификация**, встроенная в грамматику.

---

## 2. Marker Architecture

### 2.1. Семантические классы

```text  
                    WriScroll Marker
                           │
         ┌─────────────┼─────────────┐
         │                 │                │
        ::                 >>               ?>
         │                 │                │
      STATE              ACTION            TEST
         │                 │                │
   создать/изменить     выполнить        проверить
         │                 │                │
      |$| |$$|         |DB| |WGET|     |TYP| |VALID|
      |GRID|             |RUN|           |ASSERT|
      |CELL|             |HTTP|          |CHECK|
      |PERM|             |PERM|          |PERM|
      |HASH|             |AUDIT|         |RULE|
```

### 2.2. Матрица маркеров

|     Маркер   |      Класс    | `::` (STATE) | `>>` (ACTION) | `?>` (TEST) |
|--------------|---------------|--------------|---------------|-------------|
| `\|$\|`      | STATE         |      +       |       —       |      —      |
| `\|$$\|`     | SYSTEM STATE  |      +       |       —       |      —      |
| `\|?\|`      | ACTION        |      —       |       +       |      —      |
| `\|!\|`      | ACTION        |      —       |       +       |      —      |
| `\|+\|`      | ACTION        |      —       |       +       |      —      |
| `\|-\|`      | ACTION        |      —       |       +       |      —      |
| `\|DB\|`     | SYSTEM ACTION |      —       |       +       |      —      |
| `\|WGET\|`   | GUI ACTION    |      —       |       +       |      —      |
| `\|GRID\|`   | STATE         |      +       |       —       |      —      |
| `\|CELL\|`   | STATE         |      +       |       +       |      —      |
| `\|TYP\|`    | TEST          |      +       |       +       |      +      |
| `\|VALID\|`  | TEST          |      —       |       —       |      +      |
| `\|ASSERT\|` | TEST          |      —       |       —       |      +      |
| `\|F5\|`     | DEBUG         |      +       |       +       |      +      |
| `\|PERM\|`   | SECURITY      |      +       |       +       |      +      |
| `\|AUDIT\|`  | SECURITY      |      —       |       +       |      —      |
| `\|HASH\|`   | SECURITY      |      +       |       +       |      —      |
| `\|RULE\|`   | BUSINESS      |      —       |       —       |      +      |

### 2.3. Декларативные блоки vs маркеры действий

**Декларативные блоки** — описывают СУЩНОСТЬ:

- `SYS::`, `FUNC::`, `WIDGET::`, `TABLE::`, `DATASET::`
- `FILTER::`, `ANALYTICS::`, `KPI::`, `CHART::`, `REPORT::`, `EXPORT::`
- `SECURITY::`, `CRM::`, `FORM::`

**Маркеры действий** — совершают ДЕЙСТВИЕ над сущностью:

- `|$|::{}` — создать/изменить состояние
- `|DB|>>{}` — выполнить запрос
- `|WGET|>>{}` — вставить виджет
- `|SHOW|>>{}` — отобразить
- `|VALID|?>{}` — проверить
- `|PERM|>>{}` — управлять правами
- `|AUDIT|>>{}` — журналировать
- `|HASH|>>{}` — хешировать

**Схема:** DECLARATION → ENTITY → ACTION → RESULT

### 2.4. ASSIGN vs MUTATE

Маркер `|$|::{}` охватывает две разные операции:

**ASSIGN (присвоение)** — создание или изменение значения переменной:

```text
|$|::{x@int = 10}
|$|::{name@str = "John"}
```

**MUTATE (мутация)** — изменение структуры данных:

```text
|$|::{result.append(num)}
|$|::{result.remove(index)}
|$|::{user.name = "New Name"}
```

### 2.5. Маркеры и безопасность (capabilities)

Каждый системный маркер требует определённых прав (capabilities):

|     Маркер    |        Capability       |
|---------------|-------------------------|
| `\|DB\|>>`    | `database.query`        |
| `\|FILE\|>>`  | `filesystem.read/write` |
| `\|HTTP\|>>`  | `network.http`          |
| `\|PROC\|>>`  | `process.execute`       |
| `\|SYS\|>>`   | `system.admin`          |
| `\|PERM\|>>`  | `security.manage`       |
| `\|AUDIT\|>>` | `security.audit`        |

Это архитектура «Тюрьма» (Security Architecture).

---

## 3. Program Structure

### 3.1. Системный модуль

```text
SYS::ModuleName:
  # тело модуля
::SYS
```

### 3.2. Пользовательская функция

```text
FUNC::FunctionName:
  {{param1@type, param2@type}}  # параметры функции
  # тело функции
::FUNC
```

### 3.3. Точка входа

```text
MAIN::start:
  # основной код
::MAIN
```

### 3.4. SYS vs FUNC

**SYS::** — создаёт ОБЪЕКТ-КОНТЕЙНЕР (namespace) с полями:

```text
SYS::Data:
  |$|::{groups@arr = [...]}
  |$|::{employees@arr = [...]}
  |$|::{children@arr = []}
::SYS
```

**FUNC::** — создаёт ФУНКЦИЮ (возвращает значение):

```text
FUNC::CalculateTotal:
  {{data@arr}}
  |$|::{sum@int = 0}
  FOR item in data:
    |$|::{sum = sum + item}
  :FOR
  |!|>>{sum}
::FUNC
```

### 3.5. CRM каркас

```text
CRM::MyApplication:

    SECURITY::
        # Секция безопасности (обязательна)
    ::SECURITY

    DATA::
        # Секция данных
    ::DATA

    BUSINESS::
        # Секция бизнес-логики
    ::BUSINESS

    GUI::
        # Секция интерфейса
    ::GUI

    EVENTS::
        # Секция событий
    ::EVENTS

::CRM
```

Runtime проверяет наличие `SECURITY` перед запуском. Без `SECURITY` приложение не запускается.

---

## 4. Types

### 4.1. Базовые типы

|   Тип  |         Описание         |        Пример      |
|--------|--------------------------|--------------------|
| `@int` | Целое число              | `10`, `-5`, `0`    |
| `@str` | Строка                   | `"John"`           |
| `@flt` | Число с плавающей точкой | `3.14`, `-0.5`     |
| `@bol` | Булево значение          | `true` / `false`   |
| `@arr` | Массив                   | `[1, 2, 3]`        |
| `@obj` | Объект / словарь         | `{"key": "value"}` |

### 4.2. Денежные и финансовые типы

|    Тип    |         Описание              |         Пример          |
|-----------|-------------------------------|-------------------------|
| `@mon`    | Деньги (2 знака, валюта)      | `150000 UZS`, `250 USD` |
| `@dec(n)` | Десятичное с точностью `n`    | `@dec(3)` → `1.235`     |
| `@prc`    | Проценты (2 знака, вывод `%`) | `20.555` → `20.56%`     |
| `@cr`     | Координата (6 знаков)         | —                       |
| `@frac`   | Дробь (точное представление)  | —                       |
| `@prec`   | Высокая точность              | —                       |

### 4.3. Дата и время

|     Тип     |              Описание                |
|-------------|--------------------------------------|
| `@date`     | Дата (`YYYY-MM-DD`)                  |
| `@datetime` | Дата и время (`YYYY-MM-DD HH:MM:SS`) |
| `@time`     | Время (`HH:MM:SS`)                   |
| `@duration` | Длительность                         |
| `@email`    | Адрес электронной почты              |

### 4.4. Правило: `@str` никогда не исполняется

`@str` — это ДАННЫЕ. Даже если строка содержит:

- код на WriScroll
- HTML-теги
- SQL-команды
- JavaScript
- Shell-команды

Это остаётся просто строкой и не исполняется. Безопасность обеспечивается разделением **CODE ≠ DATA**.

### 4.5. Функции для работы с датами

|         Функция        |         Описание           |
|------------------------|----------------------------|
| `TODAY`                | Текущая дата               |
| `NOW`                  | Текущая дата и время       |
| `YEAR(date)`           | Извлечь год                |
| `MONTH(date)`          | Извлечь месяц              |
| `WEEK(date)`           | Извлечь номер недели       |
| `DAY(date)`            | Извлечь день               |
| `CURRENT_MONTH`        | Текущий месяц (для KPI)    |
| `PREVIOUS_MONTH`       | Предыдущий месяц (для KPI) |
| `START_OF_MONTH(date)` | Первый день месяца         |

---

## 5. Markers Reference

### 5.1. Правило использования

- `::` — ПРИСВОЕНИЕ (создание, настройка, изменение состояния)
- `>>` — ВЫПОЛНЕНИЕ (действие, команда, запуск процесса)
- `?>` — ПРОВЕРКА (условие, валидация, утверждение)

### 5.2. Пользовательские маркеры (одинарные)

|    Маркер   |             Назначение                 |         Пример             |
|-------------|----------------------------------------|----------------------------|
| `\|$\|::{}` | Переменная (ASSIGN) / Мутация (MUTATE) | `\|$\|::{x@int = 10}`      |
| `\|?\|>>{}` | Вывод на экран                         | `\|?\|>>{"Hello, World!"}` |
| `\|!\|>>{}` | Возврат значения из функции            | `\|!\|>>{result}`          |
| `\|+\|>>{}` | Добавление элемента в список           | `\|+\|>>{users new_user}`  |
| `\|-\|>>{}` | Удаление элемента из списка            | `\|-\|>>{users user_id}`   |

### 5.3. Системные маркеры (двойные)

|     Маркер   |      Назначение     |             Пример                |
|--------------|---------------------|-----------------------------------|
| `\|$$\|::{}` | Системная настройка | `\|$$\|::{"debug": true}`         | 
| `\|??\|>>{}` | Системная команда   | `\|??\|>>{"CONNECT TO database"}` |

### 5.4. Маркеры отладки

|     Маркер   |         Назначение          |            Пример              |
|--------------|-----------------------------|--------------------------------|
| `\|F5\|::{}` | Настройка отладки           | `\|F5\|::{"level": "verbose"}` |
| `\|F5\|>>{}` | Вывод отладочной информации | `\|F5\|>>{"x = " + x}`         |
| `\|F5\|?>{}` | Проверка условия в отладке  | `\|F5\|?>{x > 0}`              |

### 5.5. Маркеры ошибок

|     Маркер   |         Назначение          |
|--------------|-----------------------------|
| `TRY:`       | Попытка выполнить блок      |
| `EXP error:` | Перехват ошибки             |
| `FNL:`       | Финализация (как `finally`) |
| `\|!!\|>>{}` | Выброс ошибки               |

### 5.6. Маркеры типов

|     Маркер    |    Назначение   |             Пример                 |
|---------------|-----------------|------------------------------------|
| `\|TYP\|?>{}` | Проверка типа   | `\|TYP\|?>{x IS int}`              |
| `\|TYP\|>>{}` | Получение типа  | `\|$\|::{type@str = \|TYP\|>>{x}}` |
| `\|TYP\|::{}` | Приведение типа | `\|TYP\|::{x TO str}`              |

### 5.7. Маркеры GUI

|      Маркер     |       Назначение         |                          Пример                          |
|-----------------|--------------------------|----------------------------------------------------------|
| `\|GRID\|::{}`  | Создание сетки           | `\|GRID\|::{"id": "dashboard", "cols": 4, "rows": 3}`    |
| `\|CELL\|::{}`  | Добавление ячейки        | `\|CELL\|::{"grid": "dashboard", "col": 0, "row": 0}`    |
| `\|CELL\|>>{}`  | Обновление ячейки        | `\|CELL\|>>{"grid": "dashboard", "col": 0, "text": "8"}` |
| `\|WGET\|>>{}`  | Вставка виджета          | `\|WGET\|>>{"type": "button", "text": "OK"}`             |
| `\|SHOW\|>>{}`  | Отображение виджета      | `\|SHOW\|>>{"Dashboard"}`                                |
| `\|RUN\|>>{}`   | Запуск фонового процесса | `\|RUN\|>>{"BackupService"}`                             |
| `\|EVENT\|::{}` | Объявление события       | `\|EVENT\|::{"id": "page_changed", "page": "dashboard"}` |
| `\|TIMER\|::{}` | Таймер                   | `\|TIMER\|::{"interval": 5000, "action": "refresh"}`     |

### 5.8. Маркеры безопасности

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| `\|PERM\|>>{}` | Управление правами | `\|PERM\|>>{"allow": {"resource": "clients", "action": "READ"}}` |
| `\|PERM\|::{}` | Настройка прав | `\|PERM\|::{"default_policy": "deny"}` |
| `\|PERM\|?>{}` | Проверка права | `\|PERM\|?>{resource: "students", action: "CREATE"}` |
| `\|AUDIT\|>>{}` | Журналирование | `\|AUDIT\|>>{"action": "CREATE", "result": "SUCCESS"}` |
| `\|HASH\|>>{}` | Хеширование | `\|HASH\|>>{"admin123"}` |
| `\|HASH\|::{}` | Настройка хеширования | `\|HASH\|::{"algorithm": "bcrypt", "rounds": 10}` |

### 5.9. Системные маркеры администрирования

|       Маркер      |         Назначение          |    Capability     |
|-------------------|-----------------------------|-------------------|
| `\|SYS\|>>{}`     | Системный вызов             | `system.admin`    |
| `\|PROC\|>>{}`    | Управление процессами       | `process.execute` |
| `\|SERV\|>>{}`    | Управление службами         | `service.manage`  |
| `\|CRON\|::{}`    | Планировщик задач           | —                 |
| `\|LOG\|>>{}`     | Системное логирование       | —                 |
| `\|WARN\|>>{}`    | Предупреждение              | —                 |
| `\|ALERT\|>>{}`   | Критическое уведомление     | —                 |
| `\|METRIC\|>>{}`  | Сбор метрик                 | —                 |
| `\|ASSERT\|?>{}`  | Проверка условия            | —                 |
| `\|VALID\|?>{}`   | Валидация данных            | —                 |
| `\|CHECK\|?>{}`   | Проверка структуры и типов  | —                 |
| `\|RULE\|?>{}`    | Бизнес-правило              | —                 |
| `\|IN\|>>{}`      | Получение входных данных    | —                 |
| `\|OUT\|>>{}`     | Безопасный вывод результата | —                 |
| `\|LEN\|>>{}`     | Длина коллекции / строки    | —                 |
| `\|NET\|>>{}`     | Сетевая команда             | `network`         |
| `\|HTTP\|>>{}`    | HTTP-запрос                 | `network.http`    |
| `\|SOCKET\|>>{}`  | Сокет-соединение            | `network.socket`  |
| `\|FILE\|>>{}`    | Работа с файлами            | `filesystem`      |
| `\|DIR\|>>{}`     | Работа с директориями       | `filesystem`      |
| `\|DB\|>>{}`      | Запрос к базе данных        | `database.query`  |
| `\|BACKUP\|>>{}`  | Резервное копирование       | —                 |
| `\|MIGRATE\|>>{}` | Миграция данных             | —                 |
| `\|AUTH\|>>{}`    | Аутентификация              | —                 |
| `\|ENCRYPT\|>>{}` | Шифрование                  | —                 |
| `\|SCAN\|>>{}`    | Сканирование                | —                 |

---

## 6. Control Flow

### 6.1. Условия

```text
IF condition:
  # код выполняется, если condition истинно
:IF

EF condition:
  # код выполняется, если condition2 истинно
:EF

ES:
  # код выполняется, если все условия ложны
:ES
```

**Правило вложенности:** уровень определяется отступами. Каждый новый блок внутри блока увеличивает уровень.

**Пример:**

```text
FUNC::CheckAge:
  {{age@int}}
  
  IF age >= 18:
    |?|>>{"Взрослый"}
  :IF
  
  EF age >= 14:
    |?|>>{"Подросток"}
  :EF
  
  ES:
    |?|>>{"Ребёнок"}
  :ES
::FUNC
```

### 6.2. Циклы

**FOR:**

```text
FOR i = 1 to 10:
  # код повторяется 10 раз
:FOR
```

**FOR с шагом:**

```text
FOR i = 0 to 100 step 10:
  # код повторяется с шагом 10
:FOR
```

**FOR по коллекции:**

```text
FOR item in data:
  # код для каждого item
:FOR
```

**WHILE:**

```text
|$|::{x@int = 0}
WHILE x < 10:
  |$|::{x = x + 1}
:WHILE
```

**Управление циклами:**

- `STOP` — прерывает выполнение цикла.
- `NEXT` — переходит к следующей итерации.

**Пример:**

```text
FOR i = 1 to 10:
  IF i == 5:
    STOP
  :IF
  IF i == 3:
    NEXT
  :IF
  |?|>>{"i = " + i}
:FOR
# Выведет: 1, 2, 4
```

### 6.3. Обработка ошибок

```text
TRY:
  # код, который может вызвать ошибку
  IF error_condition:
    |!!|>>{"Сообщение ошибки"}
  :IF
:TRY

EXP error:
  # код, выполняемый при возникновении ошибки
:EXP

FNL:
  # код, выполняемый всегда (как finally)
:FNL
```

**Пример:**

```text
FUNC::SafeDivide:
  {{a@int, b@int}}
  
  |$|::{result@int = 0}
  
  TRY:
    IF b == 0:
      |!!|>>{"Деление на ноль"}
    :IF
    ES:
      |$|::{result = a / b}
    :ES
  :TRY
  
  EXP error:
    |?|>>{"⚠️ Ошибка: " + error}
    |$|::{result = 0}
  :EXP
  
  FNL:
    |?|>>{"+ Операция завершена"}
  :FNL
  
  |!|>>{result}
::FUNC
```

### 6.4. Работа с типами

**Проверка типа:**

```text
|TYP|?>{x IS int}    # Возвращает true, если x — целое число
```

**Получение типа:**

```text
|$|::{type@str = |TYP|>>{x}}
```

**Приведение типа:**

```text
|TYP|::{x TO str}   # Преобразует x в строку
```

**Пример:**

```text
FUNC::TypeDemo:
  {{value}}
  
  |TYP|?>{value IS int}:
    |?|>>{"Это целое число"}
  
  |$|::{type@str = |TYP|>>{value}}
  |?|>>{"Тип: " + type}
  
  |TYP|::{value TO str}
  |?|>>{"Как строка: " + value}
::FUNC
```

---

## 7. Debugging

### 7.1. Включение отладки

```text
|F5|::{"level": "verbose"}
|F5|::{"log_file": "debug.log"}
```

### 7.2. Вывод отладки

```text
|F5|>>{"Вход в функцию"}
|F5|>>{"x = " + x}
```

### 7.3. Проверка в отладке

```text
|F5|?>{x > 0}:
  |F5|>>{"x положительное"}
```

### 7.4. Пример

```text
FUNC::DebugDemo:
  {{x@int}}
  
  |F5|::{"level": "detailed"}
  |F5|>>{"x = " + x}
  
  |F5|?>{x > 0}:
    |F5|>>{"x положительное"}
  
  |!|>>{x * 2}
::FUNC
```

---

## 8. Правила раздела

1. Вложенность определяется отступами.
2. `::` — присвоение (состояние).
3. `>>` — выполнение (действие).
4. `?>` — проверка (условие).
5. Одинарный маркер — пользователь (`|$|`, `|?|`, `|!|`).
6. Двойной маркер — система (`|$$|`, `|??|`).
7. Все блоки имеют явное закрытие (`:IF`, `:FOR`, `:WHILE`, `:TRY`).
8. Безопасность встроена (capabilities).
9. `|F5|?>` и `|TYP|?>` НЕ ТРЕБУЮТ ЗАКРЫТИЯ.
10. `STOP` и `NEXT` — управление циклами.
11. `SYS::` — объект-контейнер; `FUNC::` — функция.
12. Идентификаторы событий в `snake_case`.
13. `|+|>>` добавляет ОТДЕЛЬНЫЙ элемент в массив.
14. Инлайн-циклы ЗАПРЕЩЕНЫ.
15. `WGET` с `id` обновляет содержимое контейнера.
16. `|$|::{}` может быть ASSIGN или MUTATE.
17. `SECURITY` — обязательный блок в CRM-приложении.
18. `@str` никогда не исполняется как код (`CODE ≠ DATA`).
19. `WRITE = CREATE + UPDATE`.
20. `apply_scope` применяется runtime на уровне `DATASET`.

---

## 9. Security Architecture

### 9.1. Что такое SECURITY

`SECURITY::` — обязательный блок в CRM-приложении. Без `SECURITY` приложение не запускается.

```text
SECURITY::Application:

    SYS::Authentication:
        # настройки аутентификации
    ::SYS

    SYS::Roles:
        # роли и права
    ::SYS

    SYS::Users:
        # пользователи
    ::SYS

    SYS::Scopes:
        # ограничения данных
    ::SYS

    SYS::Audit:
        # журналирование
    ::SYS

::SECURITY
```

### 9.2. Capabilities

Каждый системный маркер требует определённых прав (capabilities). Без capability системный вызов не выполняется.

|    Маркер     |       Capability        |
|---------------|-------------------------|
| `\|DB\|>>`    | `database.query`        |
| `\|FILE\|>>`  | `filesystem.read/write` |
| `\|HTTP\|>>`  | `network.http`          |
| `\|PROC\|>>`  | `process.execute`       |
| `\|SYS\|>>`   | `system.admin`          |
| `\|PERM\|>>`  | `security.manage`       |
| `\|AUDIT\|>>` | `security.audit`        |

Это архитектура **«Тюрьма»** (Security Architecture).

### 9.3. Уровни безопасности

1. **ВНЕШНЯЯ СТЕНА** — проверка входящих данных (`|VALID|?>`).
2. **НАДСТРОЙКА** — аутентификация и авторизация (`|AUTH|>>`).
3. **ПЕСОЧНИЦА** — изоляция выполнения (`|$$|::{"sandbox": true}`).
4. **ЯДРО** — выполнение базового кода (`|$|`, `|?|`, `|!|`).

### 9.4. Принципы

- `DATA ≠ CODE` — данные не исполняются как код.
- `GUI ≠ SECURITY` — интерфейс не является границей безопасности.
- `FILTER ≠ VALIDATION` — фильтрация не заменяет валидацию.
- `PERMISSION ≠ VISIBILITY` — право не равно видимости.

---

## 10. Roles and Permissions

### 10.1. Три режима PERM

`|PERM|` используется в трёх режимах:

|     Маркер     |      Назначение    |                            Пример                                 |
|----------------|--------------------|-------------------------------------------------------------------|
| `\|PERM\|::{}` | Настройка политики | `\|PERM\|::{"default_policy": "deny"}`                            |
| `\|PERM\|>>{}` | Выдача прав        | `\|PERM\|>>{"allow": {"resource": "students", "action": "READ"}}` |
| `\|PERM\|?>{}` | Проверка права     | `\|PERM\|?>{resource: "students", action: "CREATE"}`              |

### 10.2. Действия

| Действие |          Описание            |
|----------|------------------------------|
| `READ`   | Чтение                       |
| `CREATE` | Создание                     |
| `UPDATE` | Изменение                    |
| `DELETE` | Удаление                     |
| `WRITE`  | Составное: `CREATE + UPDATE` |

**Правило:** `WRITE = CREATE + UPDATE`.

При операции создания записи runtime рассматривает `WRITE` как `CREATE`. При изменении существующей записи — как `UPDATE`.

### 10.3. Пример: роли SchoolCRM

```text
SYS::Roles:

    SYS::RoleAdmin:
        |PERM|>>{"allow": "*"}
    ::SYS

    SYS::RoleDirector:
        |PERM|>>{"allow": {"resource": "students", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "students", "action": "WRITE"}}
        |PERM|>>{"allow": {"resource": "teachers", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "teachers", "action": "WRITE"}}
        |PERM|>>{"allow": {"resource": "subjects", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "subjects", "action": "WRITE"}}
        |PERM|>>{"allow": {"resource": "payments", "action": "READ"}}
        |PERM|>>{"deny": {"resource": "system", "action": "*"}}
        |PERM|>>{"deny": {"resource": "payments", "action": "DELETE"}}
    ::SYS

    SYS::RoleAccountant:
        |PERM|>>{"allow": {"resource": "payments", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "payments", "action": "WRITE"}}
        |PERM|>>{"allow": {"resource": "students", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "students", "action": "WRITE"}}
        |PERM|>>{"allow": {"resource": "subjects", "action": "READ"}}
        |PERM|>>{"deny": {"resource": "teachers", "action": "WRITE"}}
        |PERM|>>{"deny": {"resource": "system", "action": "*"}}
    ::SYS

    SYS::RoleTeacher:
        |PERM|>>{"allow": {"resource": "students", "action": "READ", "scope": "teacher_students"}}
        |PERM|>>{"allow": {"resource": "students", "action": "WRITE", "scope": "teacher_students"}}
        |PERM|>>{"allow": {"resource": "subjects", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "payments", "action": "READ"}}
        |PERM|>>{"allow": {"resource": "grades", "action": "CREATE", "scope": "teacher_grades"}}
        |PERM|>>{"deny": {"resource": "payments", "action": "WRITE"}}
        |PERM|>>{"deny": {"resource": "teachers", "action": "WRITE"}}
        |PERM|>>{"deny": {"resource": "system", "action": "*"}}
    ::SYS

    SYS::RoleStudent:
        |PERM|>>{"allow": {"resource": "students", "action": "READ", "scope": "self_students"}}
        |PERM|>>{"allow": {"resource": "payments", "action": "READ", "scope": "self_payments"}}
        |PERM|>>{"allow": {"resource": "subjects", "action": "READ"}}
        |PERM|>>{"deny": {"resource": "*", "action": "WRITE"}}
        |PERM|>>{"deny": {"resource": "system", "action": "*"}}
    ::SYS

::SYS
```

### 10.4. Аутентификация

```text
SYS::Authentication:
    |$$|::{"login_field": "email"}
    |$$|::{"password_field": "password"}
    |$$|::{"session_timeout": 3600}
    |$$|::{"encryption": "bcrypt"}
::SYS
```

### 10.5. Пользователи

```text
SYS::Users:

    SYS::UserAdmin:
        |$|::{user@obj = {
            "login": "admin",
            "role": "Admin",
            "password_hash": |HASH|>>{"admin123"}
        }}
        |AUTH|>>{"add_user": user}
    ::SYS

::SYS
```

### 10.6. Хеширование

```text
|HASH|::{"algorithm": "bcrypt", "rounds": 10}

|$|::{hash@str = |HASH|>>{"password"}}
|$|::{hash@str = |HASH|>>{"password", "salt": "random_salt"}}
```

---

## 11. Scope

### 11.1. Что такое Scope

Scope ограничивает **не только интерфейс**, но и **фактически доступные данные**.

`|$$|::{"apply_scope": true}` указывает runtime, что `DATASET` должен применять scope до передачи данных в GUI.

### 11.2. Определение Scope

```text
SYS::ScopeSelf:
    |$|::{scope@obj = {
        "name": "self_students",
        "resource": "students",
        "action": "READ",
        "condition": "id == CURRENT_USER.student_id"
    }}
    |VALID|?>{scope.condition IS valid}
::SYS

SYS::ScopeTeacherStudents:
    |$|::{scope@obj = {
        "name": "teacher_students",
        "resource": "students",
        "action": "READ",
        "condition": "student.id IN StudentSubjects.student_id WHERE StudentSubjects.subject_id == CURRENT_USER.subject_id"
    }}
    |VALID|?>{scope.condition IS valid}
::SYS

SYS::ScopeTeacherGrades:
    |$|::{scope@obj = {
        "name": "teacher_grades",
        "resource": "grades",
        "action": "CREATE",
        "condition": "grades.subject_id == CURRENT_USER.subject_id AND grades.student_id принадлежит StudentSubjects"
    }}
    |VALID|?>{scope.condition IS valid}
::SYS

SYS::ScopeSelfPayments:
    |$|::{scope@obj = {
        "name": "self_payments",
        "resource": "payments",
        "action": "READ",
        "condition": "student_id == CURRENT_USER.student_id"
    }}
    |VALID|?>{scope.condition IS valid}
::SYS
```

### 11.3. Именование Scope

Имя Scope должно быть **уникальным** в пределах приложения.

Нельзя определять два разных Scope с одинаковым `name`, если они относятся к разным ресурсам или имеют разную семантику.

**Reference SchoolCRM использует:**

- `teacher_students`
- `teacher_grades`
- `self_students`
- `self_payments`

### 11.4. StudentSubjects — many-to-many связь

Учитель не должен получать `subject_id` непосредственно из записи ученика, если один ученик может изучать несколько предметов.

Связь `Teacher → Subject → Student` реализуется через таблицу связи:

```text
StudentSubjects = [
    {"student_id": 1, "subject_id": 1},
    {"student_id": 1, "subject_id": 2}
]
```

**Нормативный смысл:**

```text
teacher_students:
    student.id IN StudentSubjects.student_id
    WHERE StudentSubjects.subject_id == CURRENT_USER.subject_id
```

**Принцип:**

```text
Student
   ↑
StudentSubjects
   ↑
Subject
   ↑
Teacher
```

Этот шаблон может использоваться для:

- `User ↔ Group`
- `Student ↔ Course`
- `Employee ↔ Department`
- `Product ↔ Category`
- `User ↔ Project`

### 11.5. APPLY_SCOPE — порядок применения

**Правильный порядок:**

```text
SOURCE
  ↓
PERMISSION / SCOPE CONTEXT
  ↓
SCOPE FILTER
  ↓
DATASET.data
  ↓
GUI
```

**Неправильно:**

```text
SOURCE
  ↓
DATASET.data
  ↓
GUI
  ↓
визуальное скрытие
```

Скрытие строк или кнопок в GUI **не является** механизмом безопасности.

### 11.6. Scope и CREATE

Scope не должен использоваться только как фильтр отображения.

При `CREATE` runtime должен проверить, что создаваемая запись удовлетворяет scope текущего пользователя.

**Например, для `grades + CREATE + teacher_grades`:**

```text
new_grade.subject_id == CURRENT_USER.subject_id
AND
new_grade.student_id → StudentSubjects → CURRENT_USER.subject_id
```

Таким образом, нельзя создать запись, которая не попадает в разрешённую область пользователя, даже если GUI был изменён или запрос сформирован вручную.

---

## 12. Audit

### 12.1. Настройка аудита

```text
SYS::Audit:
    |$$|::{"audit_enabled": true}
    |$$|::{"audit_storage": "database"}
    |$$|::{"audit_retention_days": 90}

    |$|::{events@arr = [
        "LOGIN",
        "LOGOUT",
        "CREATE_STUDENT",
        "UPDATE_STUDENT",
        "CREATE_PAYMENT",
        "UPDATE_PAYMENT",
        "DELETE_PAYMENT",
        "CREATE_GRADE",
        "UPDATE_GRADE",
        "PERMISSION_DENIED"
    ]}
    |AUDIT|>>{"register_events": events}
::SYS
```

### 12.2. Запись события

```text
|AUDIT|>>{
    "action": "CREATE_STUDENT",
    "resource": "student",
    "id": new_id,
    "result": "SUCCESS"
}
```

Audit фиксирует как успешные, так и отказанные критические операции.

### 12.3. Рекомендуемые события

- `LOGIN`
- `LOGOUT`
- `CREATE`
- `UPDATE`
- `DELETE`
- `EXPORT`
- `PERMISSION_DENIED`
- `STEP_UP_SUCCESS`
- `STEP_UP_FAILED`

---

## 13. Security Pipeline

### 13.1. Канонический конвейер

Безопасная CRUD-операция проходит через последовательность категорий:

```text
UNTRUSTED INPUT
      ↓
    |IN|>>          получение входных данных (из FORM / API / FILE)
      ↓
  |CHECK|?>         проверка структуры и типов
      ↓
  |VALID|?>         бизнес-валидация значения
      ↓
  |PERM|?>          проверка права текущего пользователя
      ↓
   |RULE|?>         бизнес-правило (дополнительная проверка)
      ↓
    |DB|>>          запись / изменение данных
      ↓
  |AUDIT|>>         журналирование результата
      ↓
   |OUT|>>          безопасный результат
      ↓
     |!|>>          возврат значения функции (если требуется)
```

### 13.2. Канонический пример

```text
FUNC::AddStudent:
    |IN|>>{"form": "StudentForm"}
    |CHECK|?>{name IS @str AND grade IS @int AND parent_phone IS @str}
    |VALID|?>{grade >= 1 AND grade <= 11}
    |PERM|?>{resource: "students", action: "CREATE"}
    |$|::{new_id@int = AutoID()}
    |$|::{new_student@obj = {
        "id": new_id,
        "name": name,
        "grade": grade,
        "parent_phone": parent_phone
    }}
    |DB|>>{"INSERT": SchoolData.students, "values": new_student}
    |AUDIT|>>{"action": "CREATE_STUDENT", "resource": "student", "id": new_id, "result": "SUCCESS"}
    |OUT|>>{"message": "Ученик добавлен", "id": new_id}
    |!|>>{new_student}
::FUNC
```

### 13.3. |IN| — получение входных данных

```text
|IN|>>{}        # Получение данных из входного источника.
|IN|>>{"form": "StudentForm"}
```

**Формально:** `|IN|` принимает источник данных и переносит значения в локальный контекст операции. Значения **не становятся кодом**.

**Рекомендуемые источники:**

- `FORM` — данные формы
- `API` — внешние данные
- `FILE` — импорт файла
- `SYSTEM` — системные данные
- `IMPORT` — импорт данных

**Принцип:** `CODE ≠ DATA`. Любое значение, полученное через `|IN|`, является данными.

### 13.4. FORM + SUBMIT

```text
FORM::StudentForm:
    |$|::{fields@obj = {
        "name": {
            "type": "@str",
            "widget": "textinput",
            "label": "ФИО ученика"
        },
        "grade": {
            "type": "@int",
            "widget": "numberinput",
            "label": "Класс"
        }
    }}
    SUBMIT: AddStudent
::FORM
```

**При отправке:**

```text
StudentForm
    ↓
SUBMIT: AddStudent
    ↓
AddStudent()
    ↓
|IN|>>{"form": "StudentForm"}
```

SUBMIT **не должен** обходить `SECURITY`, `CHECK`, `VALID`, `PERM` или `RULE`.

### 13.5. |CHECK|?> — проверка структуры и типов

```text
|CHECK|?>{name IS @str AND grade IS @int}
```

`|CHECK|?>` проверяет соответствие входных данных ожидаемым типам, структуре или другим техническим требованиям.

Если проверка не пройдена — операция прекращается, и `|DB|>>` не выполняется.

### 13.6. |VALID|?> — валидация значений

```text
|VALID|?>{grade >= 1 AND grade <= 11}
|VALID|?>{amount > 0}
```

**Разница:**

- `|CHECK|?>` → «данные имеют правильную структуру / тип?»
- `|VALID|?>` → «значение допустимо по правилам?»

**Пример:**

- `grade = "abc"` → `|CHECK|?>` FAIL
- `grade = 15` → `|CHECK|?>` PASS, `|VALID|?>` FAIL

### 13.7. |PERM|?> — проверка права

```text
|PERM|?>{
    resource: "students",
    action: "CREATE"
}
```

Проверяется текущий пользователь, его роль и scope.

### 13.8. |RULE|?> — бизнес-правило

```text
|RULE|?>{age >= 18}

|RULE|?>{
    check: age >= 18,
    on_fail: "Требуется совершеннолетие"
}
```

`|RULE|` применяется после технической проверки и права доступа, но до изменения данных.

**Примеры:**

```text
AddPayment:
    EXISTS(Students.data WHERE id == student_id)

AddGrade:
    EXISTS(Students.data WHERE id == student_id)
    AND
    EXISTS(Subjects.data WHERE id == subject_id)
```

### 13.9. Явные ссылки на входные данные

В бизнес-условиях нельзя использовать неоднозначное `name == name`, если одна сторона означает поле существующей записи, а другая — введённое пользователем значение.

Runtime должен различать:

- `EXISTING_RECORD.field`
- `INPUT.field`

**Reference-форма:**

```text
NOT EXISTS(
    Students.data AS student
    WHERE student.name == INPUT.name
      AND student.birth_date == INPUT.birth_date
)
```

### 13.10. |DB|>> — data operation

```text
|DB|>>{
    "INSERT": SchoolData.students,
    "values": new_student
}
```

`|DB|>>` является системным ACTION и требует capability `database.query`.

### 13.11. |OUT|>> — безопасный вывод

```text
|OUT|>>{
    "message": "Ученик добавлен",
    "id": new_id
}
```

`|OUT|` передаёт результат операции во внешний / GUI слой. Значение **не должно** автоматически интерпретироваться как WriScroll, SQL, HTML, JavaScript или shell-код.

`|OUT|` — data output, а не code execution.

### 13.12. |!|>> — возврат значения функции

```text
|!|>>{new_student}
```

`|OUT|` и `|!|` имеют разные назначения:

- `|OUT|>>` → результат для внешнего / UI слоя
- `|!|>>` → return функции

### 13.13. @str и безопасность входных данных

WriScroll **не должен** удалять из `@str` слова `def`, `var`, `function`, `SELECT`, `DROP`, `script` и т.д.

Также WriScroll **не должен** автоматически удалять `<` `>`.

Если пользователь ввёл `<script>alert(1)</script>` и поле имеет тип `@str` — это обычная строка.

**Безопасность обеспечивается тем, что:**

1. `@str` никогда не исполняется;
2. SQL использует параметризованные значения;
3. HTML / JSON / URL вывод кодируется согласно контексту;
4. типизированные поля проверяются через `CHECK` / `VALID`.

**Фильтрация слов не является основным механизмом безопасности.**

### 13.14. Уникальность сущности

Для CRM не следует считать ФИО единственным идентификатором человека.

Если бизнес-правило требует проверки повторной регистрации ученика, рекомендуется составной критерий: `name + birth_date` или другой бизнес-идентификатор.

`name` сам по себе не является гарантированно уникальным ключом.

### 13.15. AutoID

```text
FUNC::AutoID:
    |$|::{last_id@int = SchoolData.last_id}
    |$|::{new_id@int = last_id + 1}
    |$|::{SchoolData.last_id = new_id}
    |!|>>{new_id}
::FUNC
```

Это **учебный / in-memory** механизм. Для production runtime рекомендуется:

- `UUID`
- `DB sequence`
- `DB identity`

AutoID **не гарантирует** безопасную генерацию уникального ID при конкурентном выполнении нескольких операций.

### 13.16. Security Boundary

Полная модель CRM:

```text
AUTH
  ↓
ROLE
  ↓
PERM
  ↓
SCOPE
  ↓
CHECK
  ↓
VALID
  ↓
RULE
  ↓
DB
  ↓
AUDIT
  ↓
OUT
```

GUI является **только представлением**:

```text
GUI ≠ SECURITY
```

Нельзя считать удаление кнопки, скрытие `CELL` или `WIDGET` механизмом разрешения доступа.

### 13.17. Consistency Rule

Для reference CRM каждая операция записи должна быть проверяема по цепочке:

```text
INPUT
  ↓
TYPE CHECK
  ↓
VALUE VALIDATION
  ↓
PERMISSION
  ↓
SCOPE
  ↓
BUSINESS RULE
  ↓
DATABASE
  ↓
AUDIT
  ↓
OUTPUT
```

Запрещается считать операцию безопасной только потому, что пользователь не видит соответствующий элемент GUI.

Если `PERM`, `SCOPE`, `CHECK`, `VALID` или обязательный `RULE` не пройдены — `DB`-операция **не выполняется**.

---

## 14. Правила раздела

1. `SECURITY` — обязательный блок в CRM.
2. Capabilities обязательны для системных вызовов.
3. `|PERM|::` — настройка, `|PERM|>>` — выдача, `|PERM|?>` — проверка.
4. `WRITE = CREATE + UPDATE`.
5. Scope применяется runtime на уровне `DATASET`, до GUI.
6. `apply_scope=true` физически ограничивает набор записей.
7. Имя Scope уникально в пределах приложения.
8. `StudentSubjects` — reference-паттерн many-to-many.
9. Scope проверяется и при `CREATE`, не только при `READ`.
10. `|AUDIT|>>` фиксирует успешные и отказанные операции.
11. Конвейер: `IN → CHECK → VALID → PERM → RULE → DB → AUDIT → OUT`.
12. `SUBMIT` не обходит проверки.
13. `|CHECK|?>` — структура и типы.
14. `|VALID|?>` — значения.
15. `|PERM|?>` — права.
16. `|RULE|?>` — бизнес-правила.
17. Явные ссылки на входные данные в `RULE`.
18. `@str` никогда не исполняется как код.
19. Фильтрация слов ≠ безопасность.
20. `GUI ≠ SECURITY` — скрытие ≠ запрет.
21. `PERMISSION ≠ VISIBILITY`.
22. `FILTER ≠ VALIDATION`.
23. `DATA ≠ CODE`.
24. AutoID — учебный механизм; для production — UUID / sequence.
25. Consistency Rule: без проверок `DB`-операция не выполняется.

---

## 15. GUI Structure

### 15.1. Обзор

Интерфейс описывается теми же маркерами, что и логика. Это не отдельный слой — это часть языка.

**Структура:**

|    Сущность  |           Что             |      Уровень вложенности      |
|--------------|---------------------------|-------------------------------|
| `GRID::`     | Сетка (каркас приложения) | Верхний                       |
| `CELL::`     | Ячейка сетки              | Внутри `GRID::`               |
| `TRELLIS::`  | Позиция внутри ячейки     | Внутри `CELL::`               |
| `ITEM::`     | Позиция внутри trellis    | Внутри `TRELLIS::`            |
| `\|WGET\|>>` | Вставка виджета           | Внутри `TRELLIS::` / `ITEM::` |
| `\|SHOW\|>>` | Отображение               | Где угодно |

**Принцип единообразия:** каждый уровень использует одну и ту же систему 9 позиций.

```text
CELL → TRELLIS → ITEM → TRELLIS → ITEM → ...
  9  →    9    →   9  →    9    →   9  → ...  (без ограничения)
```

**Чередование обязательно:** `TRELLIS → ITEM → TRELLIS → ITEM → ...`
**Запрещено:** `ITEM` внутри `ITEM`.

### 15.2. GRID:: — сетка

**Объявление:**

```text
GRID::Name:
  |$|::{"cols": N, "rows": M, "class": "%..."}

  CELL::...
::GRID
```

- **`Name`** — имя сетки.
- **`"cols": N`** — количество колонок (целое ≥ 1).
- **`"rows": M`** — количество строк (целое ≥ 1).
- **`class`** — привязка стиля раскладки (опционально).

**Способы адресации ячеек:**

**A. По имени (рекомендуется для крупных сеток):**

```text
CELL::$head: ...
CELL::$left: ...
```

**B. По координатам:**

```text
CELL::(0,0): ...
CELL::(0,1): ...
```

**Правила:**

1. `cols` и `rows` — целые положительные числа.
2. Имена ячеек определяются в WSS через `area: ... as $name`.
3. В одном файле может быть несколько `GRID::`.
4. `GRID::` может быть вложен в `WIDGET::`, `APP::`, `CELL::`.
5. Вложенные `GRID::` внутри `CELL::` разрешены.

**Пример:**

```text
GRID::Layout:
  |$|::{"cols": 3, "rows": 3, "class": "%crm_layout"}

  CELL::$head:  ...
  CELL::$left:  ...
  CELL::$main:  ...
  CELL::$right: ...
  CELL::$footer: ...
::GRID
```

### 15.3. CELL:: — ячейка

**Три уровня CELL::**

|    №  |          Синтаксис        |         Что задаёт            |          Когда использовать          |
|-------|---------------------------|-------------------------------|--------------------------------------|
| **1** | `CELL::(row, col):`       | только позицию                | простая ячейка без WSS-переменных    |
| **2** | `CELL::(row, col) $name:` | позицию + привязку к стилям   | ячейка со стилями, без объединений   |
| **3** | `CELL::$name:`            | позицию из `area` WSS + стили | ячейка с объединениями или из `area` |

**Уровень 1 — только позиция:**

```text
CELL::(0,0):
  |$|::{"class": "%icon"}
  |WGET|>>{"type": "text", "text": "Hello"}
::CELL
```

**Уровень 2 — позиция + имя переменной:**

```text
CELL::(0,0) $icon:
  |WGET|>>{"type": "text", "text": "Hello"}
::CELL
```

**Уровень 3 — имя переменной (позиция из WSS):**

```text
CELL::$left:
  |WGET|>>{"widget": "Sidebar"}
::CELL
```

**Что может быть внутри CELL::**

- `TRELLIS::` — позиционирование объектов.
- `|WGET|>>` — виджеты.
- `|$|::{"class": "%..."}` — стили (для уровня 1).
- `FUNC::` — локальные функции.
- `GRID::` — вложенная сетка.

**Правила:**

1. Имя ячейки уникально в пределах сетки.
2. Координаты — `(row, col)`, начиная с `0`.
3. Смешивание уровней в одной сетке разрешено.
4. Конфликт позиций — ошибка загрузки.

**Сводная таблица уровней:**

| Уровень |     Синтаксис     |   Позиция   |        Стили       | `area` в WSS | `$name` в WSS |
|---------|-------------------|-------------|--------------------|--------------|---------------|
|  **1**  | `CELL::(N,M):`    | из `CELL::` | `\|WSS\|::` внутри | не нужен     | не нужен      |
|  **2**  | `CELL::(N,M) $n:` | из `CELL::` | из `$n`            | не нужен     | обязателен    |
|  **3**  | `CELL::$n:`       | из `area`   | из `$n`            | обязателен   | обязателен    |

### 15.4. TRELLIS:: — позиция в ячейке

**Trellis** — решётка **3×3** внутри ячейки. **9 позиций**.

```text
lt | ct | rt
lm | cm | rm
lb | cb | rb
```

**Позиции:**

| Позиция |  Расшифровка  |       Где         |
|---------|---------------|-------------------|
|   `lt`  | left-top      | левый верх        |
|   `ct`  | center-top    | центр верх        |
|   `rt`  | right-top     | правый верх       |
|   `lm`  | left-middle   | левый центр       |
|   `cm`  | center-middle | **центр**         |
|   `rm`  | right-middle  | правый центр      |
|   `lb`  | left-bottom   | левый низ         |
|   `cb`  | center-bottom | центр низ         |
|   `rb`  | right-bottom  | правый низ        | 
|   `all` | all positions | занять всю ячейку |

**Объявление:**

```text
CELL::$name:
  TRELLIS::lt:
    # содержимое в левом верхнем углу
  ::TRELLIS
::CELL
```

**Что может быть внутри TRELLIS::**

- `|WGET|>>` — виджеты.
- `ITEM::` — вложенное позиционирование.
- `TRELLIS::` — вложенный trellis (после `ITEM`).
- `|$|::{"class": "%..."}` — стили.

**Пример:**

```text
CELL::$head:
  TRELLIS::lm:
    |WGET|>>{"type": "text", "text": "CRM System"}
  ::TRELLIS

  TRELLIS::rm:
    |WGET|>>{"type": "icon", "name": "user"}
  ::TRELLIS
::CELL
```

**Правила:**

1. `TRELLIS::` — внутри `CELL::` или внутри `ITEM::`.
2. Позиция — одна из 9 (+ `all`).
3. Разные `TRELLIS::` в одной ячейке — разные позиции.
4. Стили — через `trellis: <pos>` в WSS.

### 15.5. ITEM:: — позиция внутри trellis

Та же решётка **3×3**, но внутри позиции trellis. **9 позиций** с префиксом `i`.

```text
ilt | ict | irt
ilm | icm | irm
ilb | icb | irb
```

**Позиции:**

| Позиция |     Расшифровка    |
|---------|--------------------|
|  `ilt`  | item-left-top      |
|  `ict`  | item-center-top    |
|  `irt`  | item-right-top     |
|  `ilm`  | item-left-middle   |
|  `icm`  | item-center-middle |
|  `irm`  | item-right-middle  |
|  `ilb`  | item-left-bottom   |
|  `icb`  | item-center-bottom |
|  `irb`  | item-right-bottom  |
|  `iall` | занять всю позицию |

**Объявление:**

```text
TRELLIS::lt:
  |WGET|>>{"type": "icon", "name": "user"}

  ITEM::ilt:
    |WGET|>>{"type": "badge", "text": "5"}
  ::ITEM
::TRELLIS
```

**Что может быть внутри ITEM::**

- `|WGET|>>` — виджеты.
- `TRELLIS::` — вложенный trellis (для дальнейшей вложенности).
- `|$|::{"class": "%..."}` — стили.

**Вложенность — без пределов.**

Разрешено чередование `TRELLIS → ITEM → TRELLIS → ITEM → ...` без ограничения глубины.

**Запрещено:** `ITEM` внутри `ITEM`.

**Пример глубокой вложенности:**

```text
CELL::$head:
  TRELLIS::rt:                    # 1. позиция в ячейке
    |WGET|>>{"type": "icon"}

    ITEM::ilt:                    # 2. позиция в иконке
      |WGET|>>{"type": "badge"}

      TRELLIS::ct:                # 3. позиция в бейдже
        |WGET|>>{"type": "text", "text": "5"}

        ITEM::ilt:                # 4. позиция в цифре
          |WGET|>>{"type": "dot"}
        ::ITEM
      ::TRELLIS
    ::ITEM
  ::TRELLIS
::CELL
```

**Правила:**

1. `ITEM::` — только внутри `TRELLIS::`.
2. Позиция — одна из 9 (+ `iall`).
3. Стили — через `item: <pos>` в WSS.
4. `ITEM` внутри `ITEM` — запрещено.
5. `TRELLIS` внутри `ITEM` — разрешено.

### 15.6. Контейнеры и обновление

**Именованный контейнер:**

```text
WIDGET::PrivateSchool:
    GRID::MainGrid:
        |$$|::{"cols": 2, "rows": 1}

        CELL::(0,0):
            |WGET|>>{"widget": "Sidebar"}
        ::CELL

        CELL::(1,0):
            |WGET|>>{
                "type": "container",
                "id": "content_area",
                "class": "%content"
            }
        ::CELL
    ::GRID
::WIDGET
```

**Обновление содержимого контейнера:**

```text
|WGET|>>{"id": "content_area", "widget": "Dashboard"}
```

Это заменяет содержимое контейнера `content_area` виджетом `Dashboard`.

### 15.7. SHOW — отображение и режимы

```text
|SHOW|>>{"PrivateSchool"}
```

**Модальное отображение формы:**

```text
|SHOW|>>{"StudentForm", "mode": "modal"}
```

**Минимальные режимы:**

- `normal`
- `modal`

Runtime должен проверять существование `TARGET` перед отображением.

### 15.8. EVENT и DISPATCH

**Объявление события:**

```text
EVENT::page_changed:
    {{page@str}}
    ...
::EVENT
```

**Вызов:**

```text
|EVENT|::{"id": "page_changed", "page": "dashboard"}
```

**Правило:** идентификатор события — `snake_case`.

- `page_changed` ✓
- `PageChanged` ✗

**Обработчик кнопки:**

```text
|WGET|>>{
    "type": "button",
    "text": "➕ Добавить ученика",
    "action": "ShowAddStudent"
}
```

```text
EVENT::ShowAddStudent:
    |SHOW|>>{"StudentForm", "mode": "modal"}
    |F5|>>{"📝 Форма добавления ученика открыта"}
::EVENT
```

### 15.9. TIMER

```text
|TIMER|::{"id": "auto_refresh", "interval": 5000, "action": "refresh"}
```

---

## 16. Widgets

### 16.1. Типы виджетов

|     Тип     |          Назначение           |
|-------------|-------------------------------|
| `text`      | Текст (заголовки, метки)      |
| `button`    | Кнопка (действия)             |
| `chart`     | График (визуализация данных)  |
| `table`     | Таблица (данные)              |
| `slider`    | Слайдер (выбор значения)      |
| `input`     | Поле ввода (ввод данных)      |
| `image`     | Изображение (картинки)        |
| `progress`  | Индикатор (прогресс-бар)      |
| `metric`    | Метрика (значение + единица)  |
| `container` | Контейнер для группы виджетов |

### 16.2. Вставка виджета

```text
|WGET|>>{"type": "button", "text": "OK"}
```

**С параметрами:**

```text
|WGET|>>{
    "type": "button",
    "text": "🔄 Обновить",
    "action": "refresh_metrics",
    "class": "%btn"
}
```

### 16.3. Обновление ячейки

```text
|CELL|>>{"grid": "dashboard", "col": 0, "text": "8"}
```

### 16.4. Форма

```text
FORM::StudentForm:
    |$|::{fields@obj = {
        "name": {
            "type": "@str",
            "widget": "textinput",
            "label": "ФИО ученика"
        },
        "grade": {
            "type": "@int",
            "widget": "numberinput",
            "label": "Класс"
        }
    }}
    SUBMIT: AddStudent
::FORM
```

**Соответствие типов и виджетов:**

|     Тип     |       Виджет      |
|-------------|-------------------|
| `@str`      | `textinput`       |
| `@int`      | `numberinput`     |
| `@flt`      | `numberinput`     |
| `@mon`      | `moneyinput`      |
| `@date`     | `datepicker`      |
| `@datetime` | `datetime picker` |
| `@bool`     | `checkbox`        |
| `@enum`     | `select`          |
| `@email`    | `emailinput`      |

### 16.5. Таблица

```text
TABLE::OrdersTable ("cols": 3):
  HEADERS: ["📋 ID", "🏢 Клиент", "📦 Товары"]
  DATA: orders

  COL(3)::ItemsTable:
    HEADERS: ["Название", "Цена", "Кол-во"]
    DATA: row.items
  ::COL(3)
::TABLE
```

**Вставка таблицы в ячейку:**

```text
CELL::(0,1):
  |WGET|>>{"type": "table", "table": "OrdersTable"}
::CELL
```

---

## 17. WSS — WriScroll Styles

### 17.1. Обзор

WSS — подсистема стилей. Стили описываются теми же маркерами, что и логика.

**Маркеры WSS:**

|     Маркер    |          Назначение        |
|---------------|----------------------------|
| `%`           | Стиль (class)              |
| `&`           | Псевдокласс (внутри стиля) |
| `\|media\|::` | Медиа-запрос               |
| `extends:`    | Наследование               |

### 17.2. Классы `%`

Набор свойств, применяется через `class: "%name"`.

```text
%btn {
  bg: #4fc3f7
  color: #0a0e1c
  border_radius: 8px
  padding: 14px
  font_size: 16px
  cursor: pointer
}
```

### 17.3. Переменные `$`

Переменная области — размеры и стили для конкретной области сетки.

```text
$head {
  size: 60px auto
  bg: #141b33
  padding: 16px
}
```

### 17.4. Раскладка — `%layout`

```text
%crm_layout {
  area: [(0,0), (1,0), (2,0)] as $head
  area: (0,1)                 as $left
  area: (1,1)                 as $main
  area: (2,1)                 as $right
  area: [(0,2), (1,2), (2,2)] as $footer

  gap:     8px
  padding: 8px
  width:   100vw
  height:  100vh
  bg:      #0a0e1c

  |media|::(max-width: 1024px) {
    area: [(0,0), (1,0)] as $head
    area: (0,1)          as $main
    area: [(0,2), (1,2)] as $footer
  }
}
```

### 17.5. `area` — привязка области

**Синтаксис:**

```text
area: [ячейки] as $имя
```

**Список ячеек:**

- Одиночная: `(1,1)`.
- Список: `[(0,0), (1,0), (2,0)]`.
- Диапазон: `(0,0)..(2,0)`.

### 17.6. `$area` — размеры и стили

```text
$head {
  size:    60px auto          # <height> <width>
  bg:      #141b33
  padding: 16px
  border:  1px solid #2d3b5a
}
```

### 17.7. Единицы измерения

|    Единица  |           Значение          |
|-------------|-----------------------------|
| `px`        | пиксели                     |
| `%`         | проценты от родителя        |
| `flex`      | растянуться                 |
| `auto`      | по содержимому              |
| `vh`, `vw`  | от высоты / ширины окна     |
| `em`, `rem` | относительно размера шрифта |

### 17.8. Свойства стилей

|      Свойство   |          Что        |          Пример           |
|-----------------|---------------------|---------------------------|
| `size`          | размеры области     | `size: 60px auto`         |
| `bg`            | фон                 | `bg: #141b33`             |
| `padding`       | внутренние отступы  | `padding: 16px`           |
| `margin`        | внешние отступы     | `margin: 8px`             |
| `border`        | границы             | `border: 1px solid red`   |
| `color`         | цвет текста         | `color: #e8eefb`          |
| `font_size`     | размер шрифта       | `font_size: 18px`         |
| `font_weight`   | жирность            | `font_weight: bold`       |
| `font_family`   | шрифт               | `font_family: monospace`  |
| `border_radius` | скругление          | `border_radius: 8px`      |
| `text_align`    | выравнивание текста | `text_align: center`      |
| `cursor`        | курсор              | `cursor: pointer`         |
| `gap`           | промежутки          | `gap: 8px`                |

### 17.9. `extends` — наследование

Один источник истины. Наследник берёт всё от родителя и переопределяет нужное.

```text
$head {
  size:    60px auto
  bg:      #141b33
  padding: 16px
}

$head_admin {
  extends: $head
  bg:      #2d1b33
  border:  2px solid gold
}
```

**Итог `$head_admin`:**

```text
size:    60px auto          # из $head
bg:      #2d1b33            # переопределён
padding: 16px               # из $head
border:  2px solid gold     # добавлен
```

**Правила:**

- Один `extends` (не множественное наследование).
- Цепочка разрешена, максимум **5 уровней**.
- Циклы запрещены (ошибка загрузки).
- Отмена наследования — просто **не указывать** `extends`.

### 17.10. `|media|::` — адаптивность

**Работает в `%layout` и в `$name`:**

```text
%crm_layout {
  area: ... как обычно ...

  |media|::(max-width: 1024px) {
    area: [(0,0), (1,0)] as $head
    area: (0,1)          as $main
    area: [(0,2), (1,2)] as $footer
  }
}

$head {
  size:    60px auto
  bg:      #141b33
  padding: 16px

  |media|::(max-width: 640px) {
    size:    40px auto
    padding: 8px
  }
}
```

Можно менять любые свойства — `size`, `bg`, `padding`, `border` и т.д.

### 17.11. `CELL::$name { ... }` — стили конкретной ячейки

Дополнительные стили к области конкретной ячейки. Приоритет — выше `$name`.

```text
WSS::CRMTheme:
  %crm_layout {
    area: [(0,0), (1,0), (2,0)] as $head
    ...
  }

  $head {
    size: 60px auto
    bg:   #141b33
  }

  CELL::$head {
    padding: 16px 24px
    border:  2px solid gold
  }
::WSS
```

**Правило:** `CELL::$head` переопределяет `$head`.

### 17.12. Путь-селекторы

**Синтаксис:**

**Через пробел (потомок):**

```text
$head TRELLIS::rt ITEM::ilt %badge_count {
  bg: #00ff00
}
```

**Через `>` (прямой потомок):**

```text
$head > TRELLIS::rt > ITEM::ilt > %badge_count {
  bg: #00ff00
}
```

**Через `*` (любой промежуточный уровень):**

```text
$head TRELLIS::rt * %badge_count {
  bg: #00ff00
}
```

**Без конечного `%class` (все виджеты в позиции):**

```text
$head TRELLIS::rt ITEM::ilt {
  bg: #0000ff
}
```

**Семантика:** путь идёт от корня (области `$name`) через все вложенные блоки до виджета.

**Правила:**

1. Корень пути — всегда `$area`.
2. Каждый уровень — блок (`TRELLIS::pos` или `ITEM::pos`).
3. Конец пути — либо `%class` виджета, либо сам блок.
4. Через пробел — любой потомок.
5. Через `>` — прямой потомок.
6. Через `*` — любой промежуточный уровень.

### 17.13. Приоритеты стилей

**От низкого к высокому:**

| № |            Что                  |                       Пример                             |
|---|---------------------------------|----------------------------------------------------------|
| 1 | `$area`                         | `$head { bg: #141b33 }`                                  |
| 2 | `CELL::$area`                   | `CELL::$head { padding: 8px }`                           |
| 3 | `%class` (общий)                | `%badge_count { bg: red }`                               |
| 4 | Путь-селектор                   | `$head TRELLIS::rt ITEM::ilt %badge_count { bg: green }` |
| 5 | `\|media\|::`                   | `\|media\|::(max-width: 640px) { ... }`                  |
| 6 | `"style": {...}` в `\|WGET\|>>` | `\|WGET\|>>{"type": "text", "style": {"color": "red"}}`  |

**Чем конкретнее — тем выше приоритет.**

### 17.14. Пример WSS

```text
WSS::CalculatorStyle:

  # ---- ПЕРЕМЕННЫЕ ----
  |$|::{--bg_main@str = #0a0a1a}
  |$|::{--bg_display@str = #1a1a2e}
  |$|::{--color_primary@str = #00ff88}
  |$|::{--color_accent@str = #e67e22}

  # ---- СТИЛЬ ДЛЯ ВСЕГО ВИДЖЕТА ----
  %widget {
    bg: --bg_main
    padding: 20px
    border_radius: 16px
    border: 1px solid #2a2a4a
    width: 320px
    height: 420px
  }

  # ---- СТИЛЬ ДЛЯ ДИСПЛЕЯ ----
  %display {
    bg: --bg_display
    color: --color_primary
    font_size: 32px
    padding: 16px
    align: right
    border_radius: 8px
    font_family: monospace
    min_height: 60px
  }

  # ---- БАЗОВЫЙ СТИЛЬ ДЛЯ КНОПОК ----
  %button {
    bg: #2c2c3e
    color: #ffffff
    font_size: 20px
    border_radius: 8px
    padding: 12px
    cursor: pointer
    width: 60px
    height: 50px
  }

  # ---- КНОПКИ-ЦИФРЫ ----
  %num {
    extends: %button
    bg: #2c2c3e

    &:hover {
      bg: #3d3d5e
    }

    &:active {
      bg: #4c4c6e
    }
  }

  # ---- КНОПКИ-ОПЕРАТОРЫ ----
  %op {
    extends: %button
    bg: --color_accent

    &:hover {
      bg: #f39c12
    }

    &:active {
      bg: #d68910
    }
  }

  # ---- КНОПКА "C" ----
  %clear {
    extends: %button
    bg: #e74c3c

    &:hover {
      bg: #c0392b
    }

    &:active {
      bg: #a93226
    }
  }

  # ---- КНОПКА "=" ----
  %eq {
    extends: %button
    bg: #27ae60

    &:hover {
      bg: #2ecc71
    }

    &:active {
      bg: #1e8449
    }
  }

  # ---- МЕДИА-ЗАПРОС ----
  |media|::(max-width: 400px) {
    %widget {
      padding: 12px
      border_radius: 8px
      width: 280px
      height: 380px
    }

    %display {
      font_size: 24px
      padding: 12px
      min_height: 50px
    }

    %button {
      font_size: 16px
      padding: 8px
      width: 50px
      height: 42px
    }
  }

::WSS
```

### 17.15. Применение стилей в виджете

```text
WIDGET::Calculator:

  GRID::WidgetLayout:
    |$$|::{"style": "CalculatorStyle"}

    CELL::(0,0):
      |WGET|>>{
        "type": "display",
        "class": "%display"
      }
    ::CELL

    CELL::(0,1):
      |WGET|>>{
        "type": "button",
        "text": "7",
        "class": "%num"
      }
    ::CELL

::WIDGET
```

---

## 18. Charts

### 18.1. Общие принципы

Каждый график — это виджет, вставляемый через `|WGET|>>{}`:

```text
CELL::(col, row):
  |WGET|>>{"type": "ТИП_ГРАФИКА", "data": МАССИВ, ...параметры}
::CELL
```

**Где:**

- `type` — тип графика (`linechart`, `areachart`, `sparkline`, `multiline`, `bars`)
- `data` — массив чисел (`@arr`) или массив массивов (для `multiline`)
- `label` — подпись над графиком (опционально)
- `color` — цвет линии в hex (опционально)
- `height` — высота в пикселях (опционально, по умолчанию 100)

**Важно:** данные должны **обновляться** через мутации массива (`.append`, `.clear`), а не через переприсваивание (`= []`), иначе график не перерисуется.

### 18.2. Типы графиков

|      Тип    |        Что рисует     |     Когда использовать    |
|-------------|-----------------------|---------------------------|
| `linechart` | Линия                 | Динамика во времени       |
| `areachart` | Линия + заливка       | Красивая динамика, акцент |
| `sparkline` | Мини-график без осей  | Внутри метрик, таблиц     |
| `multiline` | Несколько серий линий | Сравнение 2+ показателей  |
| `bars`      | Столбцы с осями       | Сравнение величин, объёмы |

### 18.3. linechart — линия

Линейный график с осями, сеткой и точками.

**Параметры:**

- `type`: `"linechart"`
- `data`: массив чисел
- `label`: подпись (опционально)
- `color`: hex-цвет линии (опционально, по умолчанию `#56ccf2`)
- `height`: высота в px (опционально, по умолчанию 100)
- `smooth`: `true` — сглаженная кривая (опционально)
- `axes`: `false` — скрыть оси и сетку (опционально)
- `dots`: `false` — скрыть точки на линии (опционально)

**Пример:**

```text
CELL::(0,0):
  |WGET|>>{
    "type": "linechart",
    "data": State.rate_history,
    "label": "📈 Курс USD",
    "color": "#56ccf2",
    "height": 100
  }
::CELL
```

**Со сглаживанием:**

```text
CELL::(0,1):
  |WGET|>>{
    "type": "linechart",
    "data": State.rate_history,
    "label": "📈 Плавная динамика",
    "smooth": true
  }
::CELL
```

### 18.4. areachart — линия с заливкой

Площадной график: линия + градиентная заливка под ней.

**Параметры** (те же, что у `linechart`, плюс):

- `smooth`: `true` по умолчанию — плавная кривая

**Пример:**

```text
CELL::(0,0):
  |WGET|>>{
    "type": "areachart",
    "data": State.rate_history,
    "label": "💵 Курс USD (area)",
    "color": "#56ccf2",
    "height": 110
  }
::CELL
```

### 18.5. sparkline — мини-график

Компактный график без осей и сетки. Хорош внутри метрик и строк таблицы.

**Параметры:**

- `type`: `"sparkline"`
- `data`: массив чисел
- `color`: hex-цвет (опционально, по умолчанию `#6fe3a5`)
- `height`: высота в px (опционально, по умолчанию 28)
- `smooth`: `true` по умолчанию — плавная кривая
- `area`: `false` — без заливки под линией

**Пример:**

```text
CELL::(1,0):
  |WGET|>>{
    "type": "sparkline",
    "data": State.rate_history_usd,
    "color": "#56ccf2",
    "height": 32
  }
::CELL
```

**Внутри метрики:**

```text
CELL::(0,1):
  |WGET|>>{"type": "metric", "label": "💰 Капитал", "value": |AMOUNT|>>{State.capital}, "unit": "UZS"}
  |WGET|>>{"type": "sparkline", "data": State.capital_history, "color": "#6fe3a5", "height": 24}
::CELL
```

### 18.6. multiline — несколько серий

График с несколькими линиями разных цветов. Легенда вверху.

**Параметры:**

- `type`: `"multiline"`
- `data`: массив массивов: `[[серия1], [серия2], ...]`
- `labels`: массив имён серий: `["USD", "EUR"]` (опционально)
- `label`: подпись всего графика
- `height`: высота в px (по умолчанию 120)
- `smooth`: `true` по умолчанию — плавные кривые

**Цвета серий автоматически чередуются:**

- `#56ccf2` (голубой)
- `#ffb454` (жёлтый)
- `#6fe3a5` (зелёный)
- `#ff6b7a` (красный)
- `#c792ea` (фиолетовый)
- `#f78c6c` (оранжевый)
- `#82aaff` (синий)

**Пример:**

```text
CELL::(0,2):
  |WGET|>>{
    "type": "multiline",
    "data": [State.rate_history_usd, State.rate_history_eur],
    "labels": ["USD", "EUR"],
    "label": "📈 Сравнение курсов",
    "height": 120
  }
::CELL
```

### 18.7. bars — столбцы с осями

Столбчатая диаграмма с осями и сеткой. Последний столбец выделяется цветом (акцент).

**Параметры:**

- `type`: `"bars"`
- `data`: массив чисел
- `label`: подпись
- `color`: hex-цвет последнего столбца (по умолчанию `#ffb454`)
- `height`: высота в px (по умолчанию 100)
- `axes`: `false` — скрыть оси (опционально)

**Пример:**

```text
CELL::(1,2):
  |WGET|>>{
    "type": "bars",
    "data": State.volume_history,
    "label": "📊 Объёмы",
    "color": "#ffb454",
    "height": 120
  }
::CELL
```

### 18.8. Встраивание в WIDGET — полный пример

```text
SYS::State:
  |$|::{rate_history_usd@arr = [12650.00, 12700.00, 12680.00, 12750.00]}
  |$|::{rate_history_eur@arr = [13750.00, 13800.00, 13780.00, 13850.00]}
  |$|::{capital@mon = 100000000.00 UZS}
::SYS

WIDGET::TraderCharts:
  GRID::ChartsGrid:
    |$$|::{"cols": 2, "rows": 3}

    CELL::(0,0):
      |WGET|>>{"type": "areachart", "data": State.rate_history_usd, "label": "💵 USD (area)", "color": "#56ccf2", "height": 110}
    ::CELL

    CELL::(1,0):
      |WGET|>>{"type": "areachart", "data": State.rate_history_eur, "label": "💶 EUR (area)", "color": "#ffb454", "height": 110}
    ::CELL

    CELL::(0,1):
      |WGET|>>{
        "type": "multiline",
        "data": [State.rate_history_usd, State.rate_history_eur],
        "labels": ["USD", "EUR"],
        "label": "📈 Сравнение курсов",
        "height": 120
      }
    ::CELL

    CELL::(1,1):
      |WGET|>>{"type": "bars", "data": State.rate_history_usd, "label": "📊 Объёмы USD", "height": 120}
    ::CELL

    CELL::(0,2):
      |WGET|>>{"type": "metric", "label": "💰 Капитал", "value": |AMOUNT|>>{State.capital}, "unit": "UZS"}
      |WGET|>>{"type": "sparkline", "data": State.rate_history_usd, "color": "#6fe3a5", "height": 32}
    ::CELL

    CELL::(1,2):
      |WGET|>>{"type": "linechart", "data": State.rate_history_usd, "label": "📈 Линия USD", "smooth": true}
    ::CELL

  ::GRID
::WIDGET

MAIN::start:
  |SHOW|>>{"TraderCharts"}
::MAIN
```

### 18.9. Обновление данных и перерисовка

**Важно:** данные для графиков должны **мутироваться**, не переприсваиваться.

**Правильно:**

```text
FUNC::AddRate:
  {{new_rate@flt}}
  |$|::{State.rate_history_usd.append(new_rate)}   # мутация
  |WGET|>>{"id": "content_area", "widget": "TraderCharts"}
::FUNC
```

**Неправильно:**

```text
FUNC::BadAddRate:
  {{new_rate@flt}}
  |$|::{State.rate_history_usd = [new_rate]}       # переприсваивание — рвёт ссылку
  |WGET|>>{"id": "content_area", "widget": "TraderCharts"}
::FUNC
```

**Правило:** для `@arr` используй `.append()` / `.clear()` / `.remove()` / `.pop()`, не присваивай новый массив.

Если ссылка разорвана — график покажет старые данные, даже после `|WGET|>>` и `|SHOW|>>`.

### 18.10. Цвета и темы

**Базовые цвета (по умолчанию):**

|     Цвет   |     Hex   | Назначение |
|------------|-----------|------------|
| голубой    | `#56ccf2` | STATE      |
| жёлтый     | `#ffb454` | ACTION     |
| зелёный    | `#6fe3a5` | TEST       |  
| красный    | `#ff6b7a` | danger     |
| фиолетовый | `#c792ea` | —          |
| оранжевый  | `#f78c6c` | —          |
| синий      | `#82aaff` | —          |

**Свой цвет:**

```text
|WGET|>>{"type": "areachart", "data": State.rate_history, "color": "#e67e22", "label": "Курс"}
```

### 18.11. Высота графиков

**По умолчанию:**

|      Тип    | Высота |
|-------------|--------|
| `linechart` | 100 px |
| `areachart` | 100 px |
| `sparkline` | 28 px  |
| `multiline` | 120 px |
| `bars`      | 100 px |

**Своя высота:**

```text
|WGET|>>{"type": "areachart", "data": State.rate_history, "height": 180}
```

Для `sparkline` — обычно 24–40 px. Для больших графиков — 120–200 px.

---

## 19. Правила раздела

1. Интерфейс описывается теми же маркерами, что и логика.
2. `GRID → CELL → TRELLIS → ITEM` — 9 позиций на каждом уровне.
3. `ITEM` внутри `ITEM` — запрещено.
4. `TRELLIS` внутри `ITEM` — разрешено (без ограничения глубины).
5. `CELL` имеет три уровня: `(N,M)`, `(N,M) $name`, `$name`.
6. `WGET` с `id` обновляет содержимое контейнера.
7. `SHOW` отображает виджет или страницу.
8. Идентификаторы событий — `snake_case`.
9. WSS использует `%` (класс), `$` (область), `extends`, `|media|::`.
10. `extends` — максимум 5 уровней, циклы запрещены.
11. Приоритет стилей: `$area` < `CELL::$area` < `%class` < путь-селектор < `|media|::` < `"style"` в `|WGET|>>`.
12. Графики вставляются только через `|WGET|>>{}` в `CELL::`.
13. Данные для графиков — `@arr`. Мутируй (`.append` / `.clear`).
14. После изменения данных — вызови `|WGET|>>` для перерисовки.
15. `color` — hex-строка; `height` — целое число пикселей; `smooth` — флаг.
16. Мультисерии — массив массивов, цвета автоматически.

---

## 20. DATASET

### 20.1. Что такое DATASET

`DATASET::` — источник данных. Может быть связан с базой данных (`|$$|::{"source": "DB"}`) или с in-memory коллекцией.

### 20.2. Объявление DATASET

```text
DATASET::Students:
  |$$|::{"source": "DB"}
  |$$|::{"table": "students"}
  |$|::{fields@arr = ["id", "name", "age", "branch", "monthly_payment"]}
  |DB|>>{"CONNECT TO " + source}
  |DB|>>{"SELECT " + fields + " FROM " + table}
  |$|::{data@arr = |DB|>>{result}}
  |!|>>{data}
::DATASET
```

### 20.3. Параметры

|    Параметр   |    Описание     |            Пример           |
|---------------|-----------------|-----------------------------|
| `source`      | Источник данных | `"DB"`, `"MEMORY"`, `"API"` |
| `table`       | Имя таблицы     | `"students"`                |
| `fields`      | Список полей    | `["id", "name", "age"]`     |
| `apply_scope` | Применять scope | `true` / `false`            |

### 20.4. apply_scope

```text
DATASET::Students:
  |$$|::{"apply_scope": true}
  ...
::DATASET
```

**При `apply_scope=true`:**

`DATASET.data` возвращает только записи, разрешённые текущему scope.

**Правильный порядок:**

```text
SOURCE
  ↓
PERMISSION / SCOPE CONTEXT
  ↓
SCOPE FILTER
  ↓
DATASET.data
  ↓
GUI
```

**Важно:** `apply_scope` должен применяться runtime на уровне `DATASET`, **до** передачи данных в GUI.

### 20.5. Источники данных

| Источник |                Описание                    |
|----------|--------------------------------------------|
| `DB`     | База данных (capability: `database.query`) |
| `MEMORY` | In-memory коллекция                        |
| `API`    | Внешний API                                |
| `FILE`   | Файл                                       |

### 20.6. Пример из SchoolCRM

```text
DATASET::Clients:
    |$$|::{"source": "DB"}
    |$$|::{"table": "clients"}
    |$|::{fields@arr = ["id", "name", "phone", "email", "balance"]}
    |DB|>>{"CONNECT TO " + source}
    |DB|>>{"SELECT " + fields + " FROM " + table}
    |$|::{data@arr = |DB|>>{result}}
    |!|>>{data}
::DATASET
```

---

## 21. TABLE

### 21.1. Что такое TABLE

`TABLE::` — таблица для отображения данных.

### 21.2. Объявление TABLE

```text
TABLE::OrdersTable ("cols": 3):
  HEADERS: ["📋 ID", "🏢 Клиент", "📦 Товары"]
  DATA: orders

  COL(3)::ItemsTable:
    HEADERS: ["Название", "Цена", "Кол-во"]
    DATA: row.items
  ::COL(3)
::TABLE
```

### 21.3. Параметры

|   Параметр |            Описание               |
|------------|-----------------------------------|
| `cols`     | Количество колонок                |
| `HEADERS`  | Массив заголовков                 |
| `DATA`     | Источник данных                   |
| `COL(n)::` | Вложенная таблица для колонки `n` |

### 21.4. Вложенные таблицы

Вложенная таблица объявляется через `COL(n)::`:

```text
COL(3)::ItemsTable:
  HEADERS: ["Название", "Цена", "Кол-во"]
  DATA: row.items
::COL(3)
```

`COL(3)` означает, что таблица встраивается в **третью** колонку родительской таблицы. `row.items` — для **каждой** строки свой массив.

### 21.5. Вставка таблицы в ячейку

```text
CELL::(0,1):
  |WGET|>>{"type": "table", "table": "OrdersTable"}
::CELL
```

### 21.6. Итерация данных

```text
TABLE::OrdersTable ("cols": 3):
  HEADERS: ["📋 ID", "🏢 Клиент", "📦 Товары"]
  DATA: orders

  FOR row in DATA:
    |?|>>{"ID: " + row.id}
    |?|>>{"Клиент: " + row.client}

    IF |LEN|>>{row.items} > 0:
      |?|>>{"  📦 Товары:"}
      FOR item in row.items:
        |?|>>{"    - " + item.name + " | Цена: $" + item.price + " | Кол-во: " + item.qty + " шт."}
      :FOR
    :IF
    ES:
      |?|>>{"  ❌ Нет товаров"}
    :ES

    |?|>>{"─────────────────────────────"}
  :FOR
::TABLE
```

---

## 22. Financial Types

### 22.1. Обзор

WriScroll 2.6.0 вводит строгую типизацию и контроль точности для финансовых вычислений.

**Принцип:**

- `PERM` определяет, **ЧТО** пользователь может сделать.
- `SCOPE` определяет, **С КАКИМИ ЗАПИСЯМИ** он может работать.
- `TYPING` определяет, **С КАКОЙ ТОЧНОСТЬЮ** и **В КАКОЙ ВАЛЮТЕ**.

### 22.2. @mon — деньги

**Синтаксис:**

```text
|$|::{price@mon = 150000 UZS}
|$|::{total@mon = 250 USD}
|$|::{salary@mon = 1200 RUB}
```

**Правила:**

- `@mon` всегда округляется до **2 знаков** (половина вверх).
- Валюта (`@mon = amount + currency`) хранится вместе со значением.
- Если валюта не указана, `currency = null`.
- При арифметике валюты **нельзя смешивать**.
- Если одна из сторон без валюты, результат без валюты.
- Вывод в консоль: `"100.00 USD"` / `"100.00"`.

**Примеры:**

|      Вход    |        Выход       |
|--------------|--------------------|
| `99.999`     | `100.00`           |
| `99.994`     | `99.99`            |
| `99.995`     | `100.00` (half-up) |
| `100.00 USD` | `100.00 USD`       |
| `150000`     | `150000.00`        |

### 22.3. @prc — проценты

**Синтаксис:**

```text
|$|::{tax@prc = 20}
|$|::{tax@prc = 20.555}
|$|::{tax@prc = 15%}
```

**Правила:**

- `@prc` всегда округляется до **2 знаков**.
- Внутреннее представление: `{ __prc: number }`.
- Вывод в консоль: `"15.00%"` (с символом `%`).
- `@prc` нельзя присвоить в `@mon` и наоборот.
- При умножении `money * prc` → `money` (процент от money).
- При сложении / вычитании `money ± prc` → надбавка / скидка.

**Примеры:**

|   Вход   |   Выход  |
|----------|----------|
| `20`     | `20.00%` |
| `20.555` | `20.56%` |
| `15%`    | `15.00%` |

### 22.4. @dec(n) — десятичное с точностью n

**Синтаксис:**

```text
|$|::{weight@dec(3) = 1.2345}
|$|::{weight@dec(0) = 1.5}
|$|::{weight@dec(2) = 1.2345}
```

**Правила:**

- `@dec(n)` округляет до `n` знаков после запятой.
- `n` — целое число от **0 до 15**.
- Вывод в консоль: `"1.235"` (без валюты и без `%`).
- `@dec(n)` не смешивается с `@mon` и `@prc` автоматически.

**Эквивалентные записи:**

- `@dec(3)` и `@dec_3`
- `@dec(0)` и `@dec_0`

**Примеры:**

|       Вход         |  Выход  |
|--------------------|---------|
| `@dec(3) = 1.2345` | `1.235` |
| `@dec(0) = 1.5`    | `2`     |
| `@dec(2) = 1.2345` | `1.23`  |

### 22.5. Арифметика @mon

|     Операция    |             Результат                      |
|-----------------|--------------------------------------------|
| `a@mon + b@mon` | `money` (валюты совпадают или одна `null`) |
| `a@mon - b@mon` | `money`                                    |
| `a@mon * 2`     | `money` (умножение на число)               |
| `a@mon * b@prc` | `money` (процент от money)                 |
| `a@mon + b@prc` | `money * (1 + b/100)` (надбавка)           |
| `a@mon - b@prc` | `money * (1 - b/100)` (скидка)             |
| `a@mon / 2`     | `money`                                    |
| `a@mon / b@prc` | `money / (b/100)`                          |

Все результаты `@mon` округляются до 2 знаков.

**Ошибка при смешении валют:**

```text
|$|::{a@mon = 100.00 USD}
|$|::{b@mon = 50.00 EUR}
|$|::{c@mon = a + b}      # ОШИБКА: Нельзя смешивать USD и EUR
```

### 22.6. Сравнение версий

|        Возможность        | 2.5.1 |            2.6.0             |
|---------------------------|-------|------------------------------|
| Контроль точности `@mon`  |   —   | + 2 знака                    |
| Контроль точности `@prc`  |   —   | + 2 знака, вывод `%`         |
| `@dec(n)` с точностью     |   —   | + `@dec(0)…@dec(15)`         |
| Проверка валют            |   —   | + `USD / EUR / UZS / …`      |
| Сложение разных валют     |   —   | + Ошибка типа                |
| Конвертация валют         |   —   | + `\|CONVERT\|>>`            |
| Банковское округление     |   —   | + `\|ROUND\|>>{…,"bankers"}` |
| Проверка точности         |   —   | + `\|CHECK_PRECISION\|>>`    |
| Форматирование с валютой  |   —   | + `\|FORMAT\|>>`             |
| Постфиксный `%` (процент) |   —   | + `price + 20%`              |
| Бинарный `%` (остаток)    |   +   | + (обратная совместимость)   |

---

## 23. Postfix % — Percent

### 23.1. Контекстно-зависимое значение оператора `%`

WriScroll 2.6.0 вводит **контекстно-зависимое** значение оператора `%`.

### 23.2. Бинарный `%` — остаток от деления

Обратная совместимость.

```text
17 % 5
→ 2

x % y
→ x - floor(x/y)*y
```

### 23.3. Постфиксный `%` — процент

```text
15%
→ { __pctValue: 15 }

price * 20%
→ 20% от price (money)

price + 20%
→ price * 1.20 (надбавка)

price - 15%
→ price * 0.85 (скидка)

100 * 50%
→ 50 (число, доля)

tax@prc = 15%
→ 15.00%
```

### 23.4. Различие

**Если справа от `%` стоит ОПЕРАНД** (число, строка, идентификатор, `(`, `[`, `{`, `@тип`, `|маркер|`) — оператор **бинарный** (остаток).

**Если справа от `%` стоит КОНЕЦ выражения**, `)`, `]`, `}`, `:`, `?`, запятая или другой оператор — оператор **постфиксный** (процент).

**Примеры:**

|   Выражение   |             Что                |              Почему              |
|---------------|--------------------------------|----------------------------------|
| `a % b`       | остаток                        | после `%` идёт идентификатор `b` |
| `a % 5`       | остаток                        | после `%` идёт число             |
| `20 * 15%`    | `15%`                          | после `%` — конец выражения      |
| `price + 20%` | надбавка                       | после `%` — конец выражения      |
| `f(x%)`       | процент                        | после `%` — `)`                  |
| `nums[0]%`    | процент                        | после `%` — `]`                  |
| `a% + b`      | процент от `a`, затем сложение | —                                |
| `a % b%`      | остаток от `a` на `(b%)`       | —                                |

### 23.5. Приоритет правил

1. Точность `@mon` и `@prc` — всегда 2 знака, автоматически.
2. `@dec(n)` округляет до `n` знаков (`0 ≤ n ≤ 15`).
3. Валюты нельзя смешивать. Если одна сторона без валюты — результат без валюты.
4. Постфиксный `%` — процент. Бинарный `%` — остаток (обратная совместимость).
5. `@prc` нельзя присвоить в `@mon` (и наоборот) без явного приведения.
6. `DATA ≠ CODE`. `@str` никогда не исполняется как код.
7. `GUI ≠ SECURITY`. Скрытие ≠ запрет. `PERM ≠ VISIBILITY`.
8. Все финансовые операции должны быть **детерминированы** (без накопления ошибок с плавающей точкой).

---

## 24. Rounding, Formatting, Conversion

### 24.1. ACTION-маркеры

|                 Маркер                   |                Назначение                  |      Пример    |
|------------------------------------------|--------------------------------------------|----------------|
| `\|ROUND\|>>{value, "bankers"}`          | Банковское округление                      | —              |
| `\|ROUND\|>>{value, "ceil"}`             | Округление вверх                           | —              |
| `\|ROUND\|>>{value, "floor"}`            | Округление вниз                            | —              |
| `\|ROUND\|>>{value, "half_up"}`          | Половина вверх (по умолчанию)              | —              |
| `\|FORMAT\|>>{value}`                    | Форматирование `@mon` (валюта из значения) | —              |
| `\|FORMAT\|>>{value, "USD"}`             | Форматирование с валютой                   | `"$120.56"`    |
| `\|FORMAT\|>>{value, "EUR"}`             | Форматирование с валютой                   | `"€120.56"`    |
| `\|FORMAT\|>>{value, "RUB"}`             | Форматирование с валютой                   | `"120.56 ₽"`   |
| `\|FORMAT\|>>{value, "UZS"}`             | Форматирование с валютой                   | `"120.56 UZS"` |
| `\|FORMAT\|>>{value, "KZT"}`             | Форматирование с валютой                   | `"120.56 ₸"`   |
| `\|FORMAT\|>>{tax@prc}`                  | Форматирование процентов                   | `"20.56%"`     |
| `\|CURRENCY\|>>{value}`                  | Валюта значения                            | `"USD"` / `""` |
| `\|AMOUNT\|>>{value}`                    | Число без валюты                           | `120.56`       |
| `\|IS_SAME\|>>{a, b}`                    | true, если валюты совпадают                | —              |
| `\|CONVERT\|>>{value, "EUR", 1.1}`       | Конвертация по курсу                       | —              |
| `\|CHECK_PRECISION\|>>{value, "mon", 2}` | true / false                               | —              |

### 24.2. Builtin-функции

Можно вызывать как обычные функции:

```text
ROUND(v, "bankers")
ROUND_UP(v)
ROUND_DOWN(v)
ROUND_HALF_UP(v)
FORMAT(v, "USD")
CURRENCY(v)
AMOUNT(v)
IS_SAME(a, b)
CONVERT(v, "EUR", 1.1)
CHECK_PRECISION(v, "mon", 2)
```

### 24.3. Вывод значений в консоль

|             Выражение                 |     Вывод      |
|---------------------------------------|----------------|
| `\|?\|\|>>{100.00}`                   | `"100.00"`     |
| `\|?\|\|>>{100.00 USD}`               | `"100.00 USD"` |
| `\|?\|\|>>{15%}`                      | `"15.00%"`     |
| `\|?\|\|>>{\|FORMAT\|\|>>{v, "USD"}}` | `"$100.00"`    |
| `\|?\|\|>>{\|FORMAT\|\|>>{v, "UZS"}}` | `"100.00 UZS"` |
| `\|?\|\|>>{\|CURRENCY\|\|>>{v}}`      | `"USD"`        |
| `\|?\|\|>>{\|AMOUNT\|\|>>{v}}`        | `"100.00"`     |

### 24.4. Пример: финансовый расчёт

```text
MAIN::start:
    |F5|::{"level": "verbose"}

    # ─── 1. ДЕНЬГИ И ПРОЦЕНТЫ ───
    |$|::{price@mon = 99.999}
    |F5|>>{"  price: " + price}          # → 100.00

    |$|::{tax_rate@prc = 20.555}
    |F5|>>{"  tax_rate: " + tax_rate}    # → 20.56%

    # ─── 2. @dec(n) ───
    |$|::{weight@dec(3) = 1.2345}
    |F5|>>{"  weight: " + weight}        # → 1.235

    # ─── 3. ПОСТФИКСНЫЙ % ───
    |$|::{price_usd@mon = 100.00 USD}
    |$|::{tax_usd@mon = price_usd * 20%}
    |F5|>>{"  tax_usd: " + tax_usd}      # → 20.00 USD

    |$|::{total_usd@mon = price_usd + 20%}
    |F5|>>{"  total_usd: " + total_usd}  # → 120.00 USD

    |$|::{discounted@mon = price_usd - 15%}
    |F5|>>{"  discounted: " + discounted} # → 85.00 USD

    # ─── 4. БИНАРНЫЙ % — ОСТАТОК ───
    |?|>>{"17 % 5 = " + (17 % 5)}        # → 2

    # ─── 5. КОНВЕРТАЦИЯ ───
    |$|::{price_eur@mon = |CONVERT|>>{price_usd, "EUR", 0.92}}
    |F5|>>{"  price_eur: " + price_eur}  # → 92.00 EUR

    # ─── 6. ФОРМАТИРОВАНИЕ ───
    |$|::{display@str = |FORMAT|>>{total_usd, "USD"}}
    |?|>>{"  display: " + display}       # → $120.00

    |?|>>{"  tax как процент: " + |FORMAT|>>{tax_rate}}  # → 20.56%

    # ─── 7. БАНКОВСКОЕ ОКРУГЛЕНИЕ ───
    |$|::{r1@mon = |ROUND|>>{119.985, "bankers"}}
    |?|>>{"  bankers(119.985) = " + r1} # → 119.98

    |$|::{r2@mon = |ROUND|>>{119.995, "bankers"}}
    |?|>>{"  bankers(119.995) = " + r2} # → 120.00

    # ─── 8. ПРОВЕРКА ТОЧНОСТИ ───
    |$|::{ok@bol = |CHECK_PRECISION|>>{price_usd, "mon", 2}}
    |?|>>{"  precision mon=2: " + ok}    # → true

    # ─── 9. СМЕШЕНИЕ ВАЛЮТ — ОШИБКА ───
    |$|::{price_eur2@mon = 50.00 EUR}
    TRY:
        |$|::{bad@mon = price_usd + price_eur2}
    :TRY
    EXP error:
        |?|>>{"⛔ " + error}              # → Нельзя смешивать валюты: USD и EUR
    :EXP

    |OUT|>>{"message": "§25: типы и точность в порядке"}
::MAIN
```

### 24.5. Форматирование валют

| Валюта | Символ |    Пример      |
|--------|--------|----------------|
| `USD`  |  `$`   | `"$120.56"`    |
| `EUR`  |  `€`   | `"€120.56"`    |
| `RUB`  |  `₽`   | `"120.56 ₽"`   |
| `UZS`  |  —     | `"120.56 UZS"` |
| `KZT`  |  `₸`   | `"120.56 ₸"`   |

---

## 25. Правила раздела

1. `DATASET` — источник данных. Может быть связан с БД или in-memory.
2. `apply_scope` применяется runtime на уровне `DATASET`, до GUI.
3. `TABLE` — таблица для отображения. Поддерживает `COL(n)::`.
4. `@mon` — деньги, 2 знака, валюта сохраняется.
5. `@prc` — проценты, 2 знака, вывод `%`.
6. `@dec(n)` — десятичное с точностью `n` (`0 ≤ n ≤ 15`).
7. Валюты нельзя смешивать. Ошибка типа.
8. `@prc` нельзя присвоить в `@mon` (и наоборот) без явного приведения.
9. `money * prc` → `money` (процент).
10. `money + prc` → надбавка; `money - prc` → скидка.
11. Постфиксный `%` — процент. Бинарный `%` — остаток.
12. Все финансовые операции детерминированы.
13. Форматирование: `|FORMAT|>>{value, "USD"}` и т.д.
14. Округление: `|ROUND|>>{value, "bankers"}` и т.д.
15. Конвертация: `|CONVERT|>>{value, "EUR", 1.1}`.
16. Проверка точности: `|CHECK_PRECISION|>>{value, "mon", 2}`.

---

## 26. WriScroll — Security, Input & Business CRM

**Статус:** нормативное дополнение к STRUCTUR_v2_5.MD

**Цель:** зафиксировать изменения, внесённые в reference-программу SchoolCRM, и устранить неоднозначности в Security / Scope / CRUD / Business Rules.

**Принцип:**

- `PERM` определяет, **ЧТО** пользователь может сделать.
- `SCOPE` определяет, **С КАКИМИ ЗАПИСЯМИ** он может работать.
- `apply_scope=true` применяет это ограничение на уровне `DATASET`, **ДО** передачи данных в GUI.

### 26.1. WRITE и CREATE / UPDATE

`WRITE` является составным правом:

```text
WRITE = CREATE + UPDATE
```

**Следовательно:**

- при операции создания записи runtime рассматривает `WRITE` как разрешение `CREATE`;
- при изменении существующей записи runtime рассматривает `WRITE` как разрешение `UPDATE`;
- `READ`, `CREATE`, `UPDATE`, `DELETE` остаются атомарными действиями;
- `SCOPE` не является действием и не заменяет `PERM`.

**Нормативная модель:**

```text
PERM = action
SCOPE = record boundary
```

**Пример:**

```text
|PERM|>>{
    "allow": {
        "resource": "students",
        "action": "WRITE",
        "scope": "teacher_students"
    }
}
```

означает:

```text
Teacher → CREATE/UPDATE → только записи,
разрешённые scope teacher_students.
```

### 26.2. Именование Scope

Имя Scope должно быть **уникальным** в пределах приложения.

Нельзя определять два разных Scope с одинаковым:

```text
"name": "teacher_subject"
```

если они относятся к разным ресурсам или имеют разную семантику.

**В reference SchoolCRM используются:**

- `teacher_students`
- `teacher_grades`
- `self_students`
- `self_payments`

Это устраняет неоднозначность поиска Scope runtime.

### 26.3. Scope `teacher_students`

Учитель не должен получать `subject_id` непосредственно из записи ученика, если один ученик может изучать несколько предметов.

Поэтому связь:

```text
Teacher → Subject → Student
```

реализуется через таблицу / коллекцию связи:

```text
StudentSubjects
```

**Минимальная структура:**

```text
StudentSubjects = [
    {"student_id": 1, "subject_id": 1},
    {"student_id": 1, "subject_id": 2}
]
```

**Нормативный смысл:**

```text
teacher_students:
    student.id IN StudentSubjects.student_id
    WHERE StudentSubjects.subject_id == CURRENT_USER.subject_id
```

**Принцип:**

```text
Student
   ↑
StudentSubjects
   ↑
Subject
   ↑
Teacher
```

Это позволяет одному ученику иметь несколько предметов без дублирования `subject_id` внутри `Students`.

### 26.4. Scope `teacher_grades`

Для оценок используется отдельный Scope:

```text
name: teacher_grades
resource: grades
action: CREATE
```

Условие должно одновременно обеспечивать:

1. оценка относится к предмету текущего учителя;
2. ученик действительно связан с этим предметом через `StudentSubjects`.

**Концептуально:**

```text
grades.subject_id == CURRENT_USER.subject_id
AND
grades.student_id принадлежит StudentSubjects
для CURRENT_USER.subject_id
```

**Таким образом:**

```text
Teacher
   ↓
grades CREATE
   ↓
teacher_grades
   ↓
только свой предмет + свои ученики
```

### 26.5. Scope `self_students`

Для пользователя Student:

```text
self_students:
    resource: students
    condition:
        id == CURRENT_USER.student_id
```

Этот Scope ограничивает доступ только собственной записью ученика.

### 26.6. Scope `self_payments`

Для платежей нельзя использовать `self_students`, поскольку ресурс платежей имеет другую структуру.

**Нормативный Scope:**

```text
self_payments:
    resource: payments
    action: READ
    condition:
        student_id == CURRENT_USER.student_id
```

**Таким образом:**

```text
Student
   ↓
payments READ
   ↓
self_payments
   ↓
только собственные платежи
```

### 26.7. APPLY_SCOPE — обязательная семантика runtime

При:

```text
|$$|::{"apply_scope": true}
```

runtime **ОБЯЗАН** применить Scope до формирования результата `DATASET`.

**Правильный порядок:**

```text
SOURCE
  ↓
PERMISSION / SCOPE CONTEXT
  ↓
SCOPE FILTER
  ↓
DATASET.data
  ↓
GUI
```

**Неправильно:**

```text
SOURCE
  ↓
DATASET.data
  ↓
GUI
  ↓
визуальное скрытие
```

Скрытие строк / кнопок в GUI **не является** механизмом безопасности.

`apply_scope=true` должен **физически** ограничивать набор записей, который возвращается через `DATASET.data`.

### 26.8. Scope и CREATE

Scope не должен использоваться только как фильтр отображения.

При `CREATE` runtime должен проверить, что создаваемая запись удовлетворяет scope текущего пользователя.

**Например, для:**

```text
grades + CREATE + teacher_grades
```

runtime должен проверить:

```text
new_grade.subject_id == CURRENT_USER.subject_id
```

и связь:

```text
new_grade.student_id
    → StudentSubjects
    → CURRENT_USER.subject_id
```

Таким образом нельзя создать запись, которая не попадает в разрешённую область пользователя, даже если GUI был изменён или запрос сформирован вручную.

### 26.9. RULE — бизнес-ограничения

**Канонический CRUD:**

```text
|IN|>>
|CHECK|?>>
|VALID|?>>
|PERM|?>>
|RULE|?>>
|DB|>>
|AUDIT|>>
|OUT|>>
```

`RULE` применяется **после** технической проверки и права доступа, но **до** изменения данных.

**Примеры reference SchoolCRM:**

```text
AddPayment:
    EXISTS(Students.data WHERE id == student_id)

AddGrade:
    EXISTS(Students.data WHERE id == student_id)
    AND
    EXISTS(Subjects.data WHERE id == subject_id)
```

`RULE` отвечает за бизнес-условия, а не за типизацию.

### 26.10. Явные ссылки на входные данные в RULE

В бизнес-условиях нельзя использовать неоднозначное:

```text
name == name
```

если одна сторона означает поле существующей записи, а другая — введённое пользователем значение.

Runtime должен различать:

```text
EXISTING_RECORD.field
INPUT.field
```

**Reference-форма:**

```text
NOT EXISTS(
    Students.data AS student
    WHERE student.name == INPUT.name
      AND student.birth_date == INPUT.birth_date
)
```

Алиасы обязательны там, где одинаковые имена могут создать неоднозначность разрешения переменных.

### 26.11. Уникальность сущности

Для CRM не следует считать ФИО единственным идентификатором человека.

Если бизнес-правило требует проверки повторной регистрации ученика, рекомендуется составной критерий, например:

```text
name + birth_date
```

или другой бизнес-идентификатор.

`name` сам по себе не является гарантированно уникальным ключом.

### 26.12. Teacher permissions — reference policy

Reference SchoolCRM разрешает Teacher:

```text
students READ  + teacher_students
students WRITE + teacher_students
subjects READ
grades CREATE  + teacher_grades
```

Teacher **не имеет:**

```text
payments WRITE
teachers WRITE
system *
```

Если политика приложения требует ограничить Teacher ещё и просмотром платежей только собственных учеников, необходимо использовать отдельный:

```text
teacher_payments
```

Scope.

Отсутствие Scope означает намеренно более широкий READ-доступ и должно быть осознанным решением политики приложения.

### 26.13. Student permissions — reference policy

Reference Student:

```text
students READ  + self_students
payments READ  + self_payments
subjects READ
```

и:

```text
deny resource "*" action "WRITE"
```

Таким образом Student не получает право изменять CRM-данные.

### 26.14. StudentSubjects как промышленный шаблон связи

`StudentSubjects` является reference-паттерном для many-to-many связи.

Он предпочтительнее поля:

```text
Students.subject_id
```

если сущность может иметь несколько предметов.

Этот же шаблон может использоваться для:

```text
User ↔ Group
Student ↔ Course
Employee ↔ Department
Product ↔ Category
User ↔ Project
```

WriScroll не обязан делать `StudentSubjects` встроенным системным типом. Это обычная DATA-сущность, используемая Scope / Rule / DB.

### 26.15. AutoID

**Reference:**

```text
AutoID()
```

является учебным / in-memory механизмом.

Для production runtime рекомендуется:

```text
UUID
DB sequence
DB identity
```

AutoID не гарантирует безопасную генерацию уникального ID при конкурентном выполнении нескольких операций.

### 26.16. Security Boundary

**Полная модель CRM:**

```text
AUTH
  ↓
ROLE
  ↓
PERM
  ↓
SCOPE
  ↓
CHECK
  ↓
VALID
  ↓
RULE
  ↓
DB
  ↓
AUDIT
  ↓
OUT
```

При этом GUI является **только представлением:**

```text
GUI ≠ SECURITY
```

Нельзя считать удаление кнопки, скрытие `CELL` или `WIDGET` механизмом разрешения доступа.

### 26.17. Reference implementation

Файл:

```text
SchoolCRM_fixed.wris
```

считается reference-примером для данной редакции спецификации.

**Он демонстрирует:**

```text
CRM
SECURITY
AUTH
HASH
ROLE
PERM
SCOPE
DATA
DATASET
FORM
SUBMIT
IN
CHECK
VALID
RULE
DB
AUDIT
OUT
WIDGET
GRID
CELL
WGET
SHOW
EVENT
```

При этом `SchoolData` и `AutoID` являются **учебным backend-слоем**, а не промышленной СУБД.

### 26.18. Consistency Rule — нормативное правило версии 2.5.1

Для reference CRM каждая операция записи должна быть проверяема по цепочке:

```text
INPUT
  ↓
TYPE CHECK
  ↓
VALUE VALIDATION
  ↓
PERMISSION
  ↓
SCOPE
  ↓
BUSINESS RULE
  ↓
DATABASE
  ↓
AUDIT
  ↓
OUTPUT
```

Запрещается считать операцию безопасной только потому, что пользователь не видит соответствующий элемент GUI.

Если `PERM`, `SCOPE`, `CHECK`, `VALID` или обязательный `RULE` не пройдены, `DB`-операция **не выполняется**.

---

## 27. WriScroll 2.6.0 — Strict Typing & Precision

**Статус:** нормативный. При конфликте с §1–§24 применяется правило §25.

**Цель:** зафиксировать строгую типизацию и контроль точности для финансовых вычислений (`@mon`, `@prc`, `@dec(n)`), ввести постфиксный оператор `%`, запретить смешивание валют и добавить новые функции / маркеры для округления, форматирования, конвертации и проверки точности.

**Принцип:**

- `PERM` определяет, **ЧТО** пользователь может сделать.
- `SCOPE` определяет, **С КАКИМИ ЗАПИСЯМИ** он может работать.
- `TYPING` определяет, **С КАКОЙ ТОЧНОСТЬЮ** и **В КАКОЙ ВАЛЮТЕ**.

### 27.1. @mon — деньги (2 знака после запятой)

```text
|$|::{price@mon = 99.999}
# → 100.00 (автоматическое округление)

|$|::{price@mon = 99.994}
# → 99.99

|$|::{price@mon = 99.995}
# → 100.00 (half-up)

|$|::{price@mon = 100.00 USD}
# → 100.00 USD (валюта сохраняется)

|$|::{price@mon = 50.00 EUR}
# → 50.00 EUR

|$|::{price@mon = 150000}
# → 150000.00 (без валюты, currency = null)
```

**Нормативно:**

- `@mon` всегда округляется до 2 знаков (половина вверх);
- валюта (`@mon = amount + currency`) хранится вместе со значением;
- если валюта не указана, `currency = null`;
- при арифметике валюты нельзя смешивать;
- если одна из сторон без валюты, результат без валюты;
- вывод в консоль: `"100.00 USD"` / `"100.00"`.

### 27.2. @prc — проценты (2 знака, вывод `"15.00%"`)

```text
|$|::{tax@prc = 20}
# → 20.00%

|$|::{tax@prc = 20.555}
# → 20.56% (автоматическое округление)

|$|::{tax@prc = 15%}
# → 15.00%
```

**Нормативно:**

- `@prc` всегда округляется до 2 знаков;
- внутреннее представление: `{ __prc: number }`;
- вывод в консоль: `"15.00%"` (с символом `%`);
- `@prc` нельзя присвоить в `@mon` и наоборот;
- при умножении `money * prc` → `money` (процент от money);
- при сложении / вычитании `money ± prc` → надбавка / скидка.

### 27.3. @dec(n) — десятичное с точностью n

```text
|$|::{weight@dec(3) = 1.2345}
# → 1.235

|$|::{weight@dec(0) = 1.5}
# → 2

|$|::{weight@dec(2) = 1.2345}
# → 1.23
```

**Эквивалентные записи:**

```text
@dec(3)  и  @dec_3
@dec(0)  и  @dec_0
```

**Нормативно:**

- `@dec(n)` округляет до `n` знаков после запятой;
- `n` — целое число от 0 до 15;
- вывод в консоль: `"1.235"` (без валюты и без `%`);
- `@dec(n)` не смешивается с `@mon` и `@prc` автоматически.

### 27.4. Постфиксный оператор `%` — процент

WriScroll 2.6.0 вводит **контекстно-зависимое** значение оператора `%`.

**Бинарный `%` — остаток от деления (обратная совместимость):**

```text
17 % 5
→ 2

x % y
→ x - floor(x/y)*y
```

**Постфиксный `%` — процент:**

```text
15%
→ { __pctValue: 15 }

price * 20%
→ 20% от price (money)

price + 20%
→ price * 1.20 (надбавка)

price - 15%
→ price * 0.85 (скидка)

100 * 50%
→ 50 (число, доля)

tax@prc = 15%
→ 15.00%
```

**Различие:**

Если справа от `%` стоит **ОПЕРАНД** (число, строка, идентификатор, `(`, `[`, `{` или `@тип`, `|маркер|`) — оператор **бинарный** (остаток).

Если справа от `%` стоит **КОНЕЦ выражения**, `)`, `]`, `}`, `:`, `?`, запятая или другой оператор — оператор **постфиксный** (процент).

**Примеры:**

```text
a % b         → остаток (после % идёт идентификатор b)
a % 5         → остаток (после % идёт число)
20 * 15%      → 15% (после % — конец выражения)
price + 20%   → надбавка (после % — конец выражения)
f(x%)         → процент (после % — )
nums[0]%      → процент (после % — ])
a% + b        → процент от a, затем сложение
a % b%        → остаток от a на (b%)
```

### 27.5. Арифметика @mon (валюты и точность)

```text
a@mon + b@mon          → money (валюты совпадают или одна null)
a@mon - b@mon          → money
a@mon * 2              → money (умножение на число)
a@mon * b@prc          → money (процент от money)
a@mon + b@prc          → money * (1 + b/100)   (надбавка)
a@mon - b@prc          → money * (1 - b/100)   (скидка)
a@mon / 2              → money
a@mon / b@prc          → money / (b/100)
```

Все результаты `@mon` округляются до 2 знаков.

**При смешении разных валют — ошибка типа:**

```text
|$|::{a@mon = 100.00 USD}
|$|::{b@mon = 50.00 EUR}
|$|::{c@mon = a + b}      # ОШИБКА: Нельзя смешивать USD и EUR
```

### 27.6. Новые маркеры и функции

**ACTION-маркеры (`>>`):**

```text
|ROUND|>>{value, "bankers"}          — банковское округление
|ROUND|>>{value, "ceil"}             — округление вверх
|ROUND|>>{value, "floor"}            — округление вниз
|ROUND|>>{value, "half_up"}          — половина вверх (по умолчанию)

|FORMAT|>>{value}                    — форматирование @mon (валюта из значения)
|FORMAT|>>{value, "USD"}             — "$120.56"
|FORMAT|>>{value, "EUR"}             — "€120.56"
|FORMAT|>>{value, "RUB"}             — "120.56 ₽"
|FORMAT|>>{value, "UZS"}             — "120.56 UZS"
|FORMAT|>>{value, "KZT"}             — "120.56 ₸"
|FORMAT|>>{tax@prc}                  — "20.56%"

|CURRENCY|>>{value}                  — "USD" / "" (если валюты нет)
|AMOUNT|>>{value}                    — 120.56 (число без валюты)
|IS_SAME|>>{a, b}                    — true, если валюты совпадают
|CONVERT|>>{value, "EUR", 1.1}       — конвертация по курсу
|CHECK_PRECISION|>>{value, "mon", 2} — true/false
```

**Builtin-функции (можно вызывать как обычные функции):**

```text
ROUND(v, "bankers") / ROUND_UP(v) / ROUND_DOWN(v) / ROUND_HALF_UP(v)
FORMAT(v, "USD") / CURRENCY(v) / AMOUNT(v) / IS_SAME(a, b)
CONVERT(v, "EUR", 1.1) / CHECK_PRECISION(v, "mon", 2)
```

### 27.7. Вывод значений в консоль

```text
|?|>>{100.00}                 → "100.00"
|?|>>{100.00 USD}             → "100.00 USD"
|?|>>{15%}                    → "15.00%"
|?|>>{|FORMAT|>>{v, "USD"}}   → "$100.00"
|?|>>{|FORMAT|>>{v, "UZS"}}   → "100.00 UZS"
|?|>>{|CURRENCY|>>{v}}        → "USD"
|?|>>{|AMOUNT|>>{v}}          → "100.00"
```

### 27.8. Пример: финансовый расчёт

```text
MAIN::start:
    |F5|::{"level": "verbose"}

    # ─── 1. ДЕНЬГИ И ПРОЦЕНТЫ ───
    |$|::{price@mon = 99.999}
    |F5|>>{"  price: " + price}          # → 100.00

    |$|::{tax_rate@prc = 20.555}
    |F5|>>{"  tax_rate: " + tax_rate}    # → 20.56%

    # ─── 2. @dec(n) ───
    |$|::{weight@dec(3) = 1.2345}
    |F5|>>{"  weight: " + weight}        # → 1.235

    # ─── 3. ПОСТФИКСНЫЙ % ───
    |$|::{price_usd@mon = 100.00 USD}
    |$|::{tax_usd@mon = price_usd * 20%}
    |F5|>>{"  tax_usd: " + tax_usd}      # → 20.00 USD

    |$|::{total_usd@mon = price_usd + 20%}
    |F5|>>{"  total_usd: " + total_usd}  # → 120.00 USD

    |$|::{discounted@mon = price_usd - 15%}
    |F5|>>{"  discounted: " + discounted} # → 85.00 USD

    # ─── 4. БИНАРНЫЙ % — ОСТАТОК ───
    |?|>>{"17 % 5 = " + (17 % 5)}        # → 2

    # ─── 5. КОНВЕРТАЦИЯ ───
    |$|::{price_eur@mon = |CONVERT|>>{price_usd, "EUR", 0.92}}
    |F5|>>{"  price_eur: " + price_eur}  # → 92.00 EUR

    # ─── 6. ФОРМАТИРОВАНИЕ ───
    |$|::{display@str = |FORMAT|>>{total_usd, "USD"}}
    |?|>>{"  display: " + display}       # → $120.00

    |?|>>{"  tax как процент: " + |FORMAT|>>{tax_rate}}  # → 20.56%

    # ─── 7. БАНКОВСКОЕ ОКРУГЛЕНИЕ ───
    |$|::{r1@mon = |ROUND|>>{119.985, "bankers"}}
    |?|>>{"  bankers(119.985) = " + r1} # → 119.98

    |$|::{r2@mon = |ROUND|>>{119.995, "bankers"}}
    |?|>>{"  bankers(119.995) = " + r2} # → 120.00

    # ─── 8. ПРОВЕРКА ТОЧНОСТИ ───
    |$|::{ok@bol = |CHECK_PRECISION|>>{price_usd, "mon", 2}}
    |?|>>{"  precision mon=2: " + ok}    # → true

    # ─── 9. СМЕШЕНИЕ ВАЛЮТ — ОШИБКА ───
    |$|::{price_eur2@mon = 50.00 EUR}
    TRY:
        |$|::{bad@mon = price_usd + price_eur2}
    :TRY
    EXP error:
        |?|>>{"⛔ " + error}              # → Нельзя смешивать валюты: USD и EUR
    :EXP

    |OUT|>>{"message": "§25: типы и точность в порядке"}
::MAIN
```

### 27.9. Сравнение 2.5.1 vs 2.6.0

|        Возможность        | 2.5.1 |             2.6.0            |
|---------------------------|-------|------------------------------|
| Контроль точности `@mon`  |   —   | + 2 знака                    |
| Контроль точности `@prc`  |   —   | + 2 знака, вывод `%`         |
| `@dec(n)` с точностью     |   —   | + `@dec(0)…@dec(15)`         |
| Проверка валют            |   —   | + `USD / EUR / UZS / …`      |
| Сложение разных валют     |   —   | + Ошибка типа                |
| Конвертация валют         |   —   | + `\|CONVERT\|>>`            |
| Банковское округление     |   —   | + `\|ROUND\|>>{…,"bankers"}` |
| Проверка точности         |   —   | + `\|CHECK_PRECISION\|>>`    |
| Форматирование с валютой  |   —   | + `\|FORMAT\|>>`             |
| Постфиксный `%` (процент) |   —   | + `price + 20%`              |
| Бинарный `%` (остаток)    |   +   | + (обратная совместимость)   |

### 27.10. Приоритет правил §25

1. Точность `@mon` и `@prc` — всегда 2 знака, автоматически.
2. `@dec(n)` округляет до `n` знаков (`0 ≤ n ≤ 15`).
3. Валюты нельзя смешивать. Если одна сторона без валюты — результат без валюты.
4. Постфиксный `%` — процент. Бинарный `%` — остаток (обратная совместимость).
5. `@prc` нельзя присвоить в `@mon` (и наоборот) без явного приведения.
6. `DATA ≠ CODE`. `@str` никогда не исполняется как код.
7. `GUI ≠ SECURITY`. Скрытие ≠ запрет. `PERM ≠ VISIBILITY`.
8. Все финансовые операции должны быть детерминированы (без накопления ошибок с плавающей точкой).

---

## 28. WriScroll 2.6.0 — Charts & Visualization

**Статус:** нормативный. Описывает GUI-виджеты для визуализации данных во вкладке «Браузер» (GUI-рантайм).

Все графики объявляются через `|WGET|>>{}` внутри `CELL::` виджета. Данные берутся из массивов `@arr`. Цвет, высота и сглаживание — настраиваются.

### 28.1. Общие принципы

Каждый график — это виджет, вставляемый через `|WGET|>>{}`:

```text
CELL::(col, row):
  |WGET|>>{"type": "ТИП_ГРАФИКА", "data": МАССИВ, ...параметры}
::CELL
```

**Где:**

- `type` — тип графика (`linechart`, `areachart`, `sparkline`, `multiline`, `bars`)
- `data` — массив чисел (`@arr`) или массив массивов (для `multiline`)
- `label` — подпись над графиком (опционально)
- `color` — цвет линии в hex (опционально)
- `height` — высота в пикселях (опционально, по умолчанию 100)

Данные должны **обновляться** через мутации массива (`.append`, `.clear`), а не через переприсваивание (`= []`), иначе график не перерисуется.

### 28.2. Типы графиков — сводная таблица

|     Тип     |         Что рисует    |    Когда использовать     |
|-------------|-----------------------|---------------------------|
| `linechart` | Линия                 | Динамика во времени       |
| `areachart` | Линия + заливка       | Красивая динамика, акцент |
| `sparkline` | Мини-график без осей  | Внутри метрик, таблиц     |
| `multiline` | Несколько серий линий | Сравнение 2+ показателей  |
| `bars`      | Столбцы с осями       | Сравнение величин, объёмы |
| `chart`     | Старый (столбики)     | Только для legacy-кода    |

### 28.3. linechart — линия

Линейный график с осями, сеткой и точками.

**Параметры:**

- `type`: `"linechart"`
- `data`: массив чисел
- `label`: подпись (опционально)
- `color`: hex-цвет линии (опционально, по умолчанию `#56ccf2`)
- `height`: высота в px (опционально, по умолчанию 100)
- `smooth`: `true` — сглаженная кривая (опционально)
- `axes`: `false` — скрыть оси и сетку (опционально)
- `dots`: `false` — скрыть точки на линии (опционально)

**Пример:**

```text
CELL::(0,0):
  |WGET|>>{
    "type": "linechart",
    "data": State.rate_history,
    "label": "📈 Курс USD",
    "color": "#56ccf2",
    "height": 100
  }
::CELL
```

**Со сглаживанием:**

```text
CELL::(0,1):
  |WGET|>>{
    "type": "linechart",
    "data": State.rate_history,
    "label": "📈 Плавная динамика",
    "smooth": true
  }
::CELL
```

### 28.4. areachart — линия с заливкой

Площадной график: линия + градиентная заливка под ней.

**Параметры** (те же, что у `linechart`, плюс):

- `smooth`: `true` по умолчанию — плавная кривая

**Пример:**

```text
CELL::(0,0):
  |WGET|>>{
    "type": "areachart",
    "data": State.rate_history,
    "label": "💵 Курс USD (area)",
    "color": "#56ccf2",
    "height": 110
  }
::CELL
```

### 28.5. sparkline — мини-график

Компактный график без осей и сетки. Хорош внутри метрик и строк таблицы.

**Параметры:**

- `type`: `"sparkline"`
- `data`: массив чисел
- `color`: hex-цвет (опционально, по умолчанию `#6fe3a5`)
- `height`: высота в px (опционально, по умолчанию 28)
- `smooth`: `true` по умолчанию — плавная кривая
- `area`: `false` — без заливки под линией

**Пример:**

```text
CELL::(1,0):
  |WGET|>>{
    "type": "sparkline",
    "data": State.rate_history_usd,
    "color": "#56ccf2",
    "height": 32
  }
::CELL
```

### 28.6. multiline — несколько серий

График с несколькими линиями разных цветов. Легенда вверху.

**Параметры:**

- `type`: `"multiline"`
- `data`: массив массивов: `[[серия1], [серия2], ...]`
- `labels`: массив имён серий: `["USD", "EUR"]` (опционально)
- `label`: подпись всего графика
- `height`: высота в px (по умолчанию 120)
- `smooth`: `true` по умолчанию — плавные кривые

**Цвета серий автоматически чередуются:**

```text
#56ccf2 (голубой), #ffb454 (жёлтый), #6fe3a5 (зелёный),
#ff6b7a (красный), #c792ea (фиолетовый), #f78c6c (оранжевый),
#82aaff (синий)
```

**Пример:**

```text
CELL::(0,2):
  |WGET|>>{
    "type": "multiline",
    "data": [State.rate_history_usd, State.rate_history_eur],
    "labels": ["USD", "EUR"],
    "label": "📈 Сравнение курсов",
    "height": 120
  }
::CELL
```

### 28.7. bars — столбцы с осями

Столбчатая диаграмма с осями и сеткой. Последний столбец выделяется цветом (акцент).

**Параметры:**

- `type`: `"bars"`
- `data`: массив чисел
- `label`: подпись
- `color`: hex-цвет последнего столбца (по умолчанию `#ffb454`)
- `height`: высота в px (по умолчанию 100)
- `axes`: `false` — скрыть оси (опционально)

**Пример:**

```text
CELL::(1,2):
  |WGET|>>{
    "type": "bars",
    "data": State.volume_history,
    "label": "📊 Объёмы",
    "color": "#ffb454",
    "height": 120
  }
::CELL
```

### 28.8. Встраивание в WIDGET — полный пример

```text
SYS::State:
  |$|::{rate_history_usd@arr = [12650.00, 12700.00, 12680.00, 12750.00]}
  |$|::{rate_history_eur@arr = [13750.00, 13800.00, 13780.00, 13850.00]}
  |$|::{capital@mon = 100000000.00 UZS}
::SYS

WIDGET::TraderCharts:
  GRID::ChartsGrid:
    |$$|::{"cols": 2, "rows": 3}

    CELL::(0,0):
      |WGET|>>{"type": "areachart", "data": State.rate_history_usd, "label": "💵 USD (area)", "color": "#56ccf2", "height": 110}
    ::CELL

    CELL::(1,0):
      |WGET|>>{"type": "areachart", "data": State.rate_history_eur, "label": "💶 EUR (area)", "color": "#ffb454", "height": 110}
    ::CELL

    CELL::(0,1):
      |WGET|>>{
        "type": "multiline",
        "data": [State.rate_history_usd, State.rate_history_eur],
        "labels": ["USD", "EUR"],
        "label": "📈 Сравнение курсов",
        "height": 120
      }
    ::CELL

    CELL::(1,1):
      |WGET|>>{"type": "bars", "data": State.rate_history_usd, "label": "📊 Объёмы USD", "height": 120}
    ::CELL

    CELL::(0,2):
      |WGET|>>{"type": "metric", "label": "💰 Капитал", "value": |AMOUNT|>>{State.capital}, "unit": "UZS"}
      |WGET|>>{"type": "sparkline", "data": State.rate_history_usd, "color": "#6fe3a5", "height": 32}
    ::CELL

    CELL::(1,2):
      |WGET|>>{"type": "linechart", "data": State.rate_history_usd, "label": "📈 Линия USD", "smooth": true}
    ::CELL

  ::GRID
::WIDGET

MAIN::start:
  |SHOW|>>{"TraderCharts"}
::MAIN
```

### 28.9. Обновление данных и перерисовка

**Важно:** данные для графиков должны **мутироваться**, не переприсваиваться.

**Правильно:**

```text
FUNC::AddRate:
  {{new_rate@flt}}
  |$|::{State.rate_history_usd.append(new_rate)}   # мутация
  |WGET|>>{"id": "content_area", "widget": "TraderCharts"}
::FUNC
```

**Неправильно:**

```text
FUNC::BadAddRate:
  {{new_rate@flt}}
  |$|::{State.rate_history_usd = [new_rate]}       # переприсваивание — рвёт ссылку
  |WGET|>>{"id": "content_area", "widget": "TraderCharts"}
::FUNC
```

**Правило:** для `@arr` используй `.append()` / `.clear()` / `.remove()` / `.pop()`, не присваивай новый массив.

Если ссылка разорвана — график покажет старые данные, даже после `|WGET|>>` и `|SHOW|>>`.

### 28.10. Цвета и темы

**Базовые цвета (по умолчанию):**

```text
#56ccf2  — голубой (STATE)
#ffb454  — жёлтый (ACTION)
#6fe3a5  — зелёный (TEST)
#ff6b7a  — красный (danger)
#c792ea  — фиолетовый
#f78c6c  — оранжевый
#82aaff  — синий
```

**Можно задать любой hex в параметре `"color"`:**

```text
|WGET|>>{"type": "areachart", "data": State.rate_history, "color": "#e67e22", "label": "Курс"}
```

### 28.11. Высота графиков

**По умолчанию:**

```text
linechart  — 100 px
areachart  — 100 px
sparkline  — 28 px
multiline  — 120 px
bars       — 100 px
```

**Можно задать через `"height"`:**

```text
|WGET|>>{"type": "areachart", "data": State.rate_history, "height": 180}
```

Для `sparkline` — обычно 24–40 px. Для больших графиков — 120–200 px.

### 28.12. Типичные сценарии

**Сценарий 1: Дашборд с двумя графиками курсов**

```text
CELL::(0,0):
  |WGET|>>{"type": "areachart", "data": State.rate_history_usd, "label": "USD", "color": "#56ccf2"}
::CELL
CELL::(1,0):
  |WGET|>>{"type": "areachart", "data": State.rate_history_eur, "label": "EUR", "color": "#ffb454"}
::CELL
```

**Сценарий 2: Метрика + sparkline**

```text
CELL::(0,1):
  |WGET|>>{"type": "metric", "label": "Выручка", "value": |AMOUNT|>>{State.capital}, "unit": "UZS"}
  |WGET|>>{"type": "sparkline", "data": State.capital_history, "color": "#6fe3a5"}
::CELL
```

**Сценарий 3: Сравнение валют**

```text
CELL::(0,2):
  |WGET|>>{
    "type": "multiline",
    "data": [State.usd_history, State.eur_history, State.rub_history],
    "labels": ["USD", "EUR", "RUB"],
    "label": "Все курсы"
  }
::CELL
```

**Сценарий 4: Объёмы по дням**

```text
CELL::(0,3):
  |WGET|>>{"type": "bars", "data": State.daily_volume, "label": "Оборот по дням", "color": "#ffb454"}
::CELL
```

**Сценарий 5: KPI с трендом (внутри метрики)**

```text
CELL::(0,4):
  |WGET|>>{
    "type": "metric",
    "label": "📊 P&L за день",
    "value": |AMOUNT|>>{KPI.DailyPnL.result},
    "unit": "UZS"
  }
  |WGET|>>{"type": "sparkline", "data": KPI.DailyPnL.result.history, "color": "#6fe3a5"}
::CELL
```

### 28.13. Сравнение с прошлыми версиями

|    Возможность   |   2.5.1   |               2.6.0            |
|------------------|-----------|--------------------------------|
| Линейный график  | —         | + `linechart`                  |
| Площадной график | —         | + `areachart`                  |
| Мультисерии      | —         | + `multiline`                  |
| Мини-график      | —         | + `sparkline`                  |
| Столбцы с осями  | ! `chart` | + `bars` (с осями и сеткой)    |
| Сглаживание      | —         | + `smooth: true` (Catmull-Rom) |
| Настройка цвета  | !         | + `color: "#hex"`              |
| Настройка высоты | —         | + `height: N`                  |

### 28.14. Приоритет правил §26

1. Графики вставляются только через `|WGET|>>{}` в `CELL::`.
2. Данные — массив `@arr`. Мутируй его (`.append` / `.clear`).
3. После изменения данных — вызови `|WGET|>>` для перерисовки.
4. `color` — hex-строка. Если не указан — по умолчанию голубой.
5. `height` — целое число пикселей.
6. `smooth` — `true` для плавной кривой, `false` для угловатой.
7. `axes` / `dots` / `area` — флаги для тонкой настройки.
8. Мультисерии — массив массивов, цвета автоматически.

---

## Часть VI. Reference

### Golden Rules

**Синтаксис:**

1. Вложенность — отступами.
2. \`::\` — присвоение.
3. \`>>\` — выполнение.
4. \`?>\` — проверка.
5. Одинарный маркер — пользователь.
6. Двойной маркер — система.
7. Все блоки имеют явное закрытие.

**Маркеры:**

8. \`|F5|?>\` и \`|TYP|?>\` НЕ ТРЕБУЮТ ЗАКРЫТИЯ.
9. \`STOP\` и \`NEXT\` — управление циклами.
10. \`SYS::\` — контейнер; \`FUNC::\` — функция.
11. Идентификаторы событий — \`snake_case\`.
12. \`|+|>>\` добавляет ОТДЕЛЬНЫЙ элемент.
13. Инлайн-циклы ЗАПРЕЩЕНЫ.
14. \`WGET\` с \`id\` обновляет контейнер.
15. \`|$|::{}\` — ASSIGN или MUTATE.

**Безопасность:**

16. \`SECURITY\` — обязательный блок в CRM.
17. Capabilities обязательны.
18. \`|PERM|::\` — настройка, \`|PERM|>>\` — выдача, \`|PERM|?>\` — проверка.
19. \`WRITE = CREATE + UPDATE\`.
20. Scope применяется runtime на уровне \`DATASET\`.
21. \`apply_scope=true\` физически ограничивает записи.
22. Имя Scope уникально.
23. \`StudentSubjects\` — many-to-many.
24. Scope проверяется и при \`CREATE\`.
25. \`|AUDIT|>>\` фиксирует успешные и отказанные операции.
26. Конвейер: \`IN → CHECK → VALID → PERM → RULE → DB → AUDIT → OUT\`.
27. \`SUBMIT\` не обходит проверки.
28. \`@str\` никогда не исполняется.
29. Фильтрация слов ≠ безопасность.
30. \`GUI ≠ SECURITY\`.
31. \`PERMISSION ≠ VISIBILITY\`.
32. \`FILTER ≠ VALIDATION\`.
33. \`DATA ≠ CODE\`.
34. AutoID — учебный; для production — UUID / sequence.
35. Consistency Rule.

**Типы и точность:**

36. \`@mon\` — 2 знака, валюта.
37. \`@prc\` — 2 знака, \`%\`.
38. \`@dec(n)\` — \`0 ≤ n ≤ 15\`.
39. Валюты нельзя смешивать.
40. \`@prc\` ≠ \`@mon\` без приведения.
41. \`money * prc\` → \`money\`.
42. \`money ± prc\` → надбавка / скидка.
43. Постфиксный \`%\` — процент. Бинарный — остаток.
44. Финансовые операции детерминированы.
45. \`|FORMAT|>>\`.
46. \`|ROUND|>>\`.
47. \`|CONVERT|>>\`.
48. \`|CHECK_PRECISION|>>\`.

**GUI и WSS:**

49. Интерфейс — теми же маркерами.
50. \`GRID → CELL → TRELLIS → ITEM\`.
51. \`ITEM\` внутри \`ITEM\` — запрещено.
52. \`TRELLIS\` внутри \`ITEM\` — разрешено.
53. \`CELL\` — три уровня.
54. WSS: \`%\`, \`$\`, \`extends\`, \`|media|::\`.
55. \`extends\` — максимум 5 уровней.
56. Приоритеты стилей.
57. Графики — только через \`|WGET|>>{}\`.
58. Данные для графиков — \`@arr\`.
59. После изменения — \`|WGET|>>\`.
60. \`color\`, \`height\`, \`smooth\`.
61. Мультисерии — массив массивов.

### Marker Cheatsheet

**Присвоение (\`::\`):**

| Маркер | Пример |
|--------|--------|
| \`|$|\` | \`|$|::{x@int = 10}\` |
| \`|$$|\` | \`|$$|::{"debug": true}\` |
| \`|F5|\` | \`|F5|::{"level": "verbose"}\` |
| \`|TYP|\` | \`|TYP|::{x TO str}\` |
| \`|GRID|\` | \`|GRID|::{"cols": 4}\` |
| \`|CELL|\` | \`|CELL|::{"col": 0, "row": 0}\` |
| \`|PERM|\` | \`|PERM|::{"default_policy": "deny"}\` |
| \`|HASH|\` | \`|HASH|::{"algorithm": "bcrypt"}\` |

**Выполнение (\`>>\`):**

| Маркер | Пример |
|--------|--------|
| \`|?|\` | \`|?|>>{"Hello"}\` |
| \`|!|\` | \`|!|>>{result}\` |
| \`|+|\` | \`|+|>>{list item}\` |
| \`|-|\` | \`|-|>>{list item}\` |
| \`|??|\` | \`|??|>>{"CONNECT"}\` |
| \`|F5|\` | \`|F5|>>{"x = " + x}\` |
| \`|TYP|\` | \`|TYP|>>{x}\` |
| \`|DB|\` | \`|DB|>>{"SELECT ..."}\` |
| \`|WGET|\` | \`|WGET|>>{"type": "button"}\` |
| \`|SHOW|\` | \`|SHOW|>>{"Dashboard"}\` |
| \`|RUN|\` | \`|RUN|>>{"Backup"}\` |
| \`|CELL|\` | \`|CELL|>>{"text": "8"}\` |
| \`|PERM|\` | \`|PERM|>>{"allow": ...}\` |
| \`|HASH|\` | \`|HASH|>>{"admin123"}\` |
| \`|AUDIT|\` | \`|AUDIT|>>{"action": "CREATE"}\` |
| \`|IN|\` | \`|IN|>>{"form": "StudentForm"}\` |
| \`|OUT|\` | \`|OUT|>>{"message": "OK"}\` |
| \`|LEN|\` | \`|LEN|>>{Subjects.data}\` |

**Проверка (\`?>\`):**

| Маркер | Пример |
|--------|--------|
| \`|F5|\` | \`|F5|?>{x > 0}\` |
| \`|TYP|\` | \`|TYP|?>{x IS int}\` |
| \`|ASSERT|\` | \`|ASSERT|?>{x > 0}\` |
| \`|VALID|\` | \`|VALID|?>{grade >= 1}\` |
| \`|PERM|\` | \`|PERM|?>{resource: "students", action: "CREATE"}\` |
| \`|CHECK|\` | \`|CHECK|?>{name IS @str}\` |
| \`|RULE|\` | \`|RULE|?>{age >= 18}\` |

### Быстрая шпаргалка

| Действие | Маркер | Пример |
|----------|--------|--------|
| Создать / изменить | \`|...|::{}\` | \`|$|::{x = 10}\` |
| Выполнить | \`|...|>>{}\` | \`|?|>>{"Hello"}\` |
| Проверить | \`|...|?>{}\` | \`|TYP|?>{x IS int}\` |
| Управлять правами | \`|PERM|>>{}\` | \`|PERM|>>{"allow": "*"}\` |
| Журналировать | \`|AUDIT|>>{}\` | \`|AUDIT|>>{"action": "CREATE"}\` |
| Хешировать | \`|HASH|>>{}\` | \`|HASH|>>{"password"}\` |

### Architecture Layers

\`\`\`text
┌─────────────────────────────────────────┐
│              PRESENTATION               │
│     WIDGET GRID CELL WGET WSS           │
├─────────────────────────────────────────┤
│              BUSINESS                   │
│     KPI ANALYTICS REPORT CHART          │
├─────────────────────────────────────────┤
│                DATA                     │
│  DATASET FILTER SORT GROUP AGGREGATE    │
├─────────────────────────────────────────┤
│               SYSTEM                    │
│  DB FILE HTTP AUTH PERM BACKUP ...      │
├─────────────────────────────────────────┤
│                 CORE                    │
│ types vars functions IF FOR WHILE ...   │
└─────────────────────────────────────────┘
\`\`\`

**Слои обучения:**

- Слой 1 — состояние (\`::\`).
- Слой 2 — действие (\`>>\`).
- Слой 3 — проверка (\`?>\`).

**Конвейер безопасности:**

\`\`\`text
UNTRUSTED INPUT
      ↓
    |IN|>>
      ↓
  |CHECK|?>
      ↓
  |VALID|?>
      ↓
  |PERM|?>
      ↓
   |RULE|?>
      ↓
    |DB|>>
      ↓
  |AUDIT|>>
      ↓
   |OUT|>>
      ↓
     |!|>>
\`\`\`

---

# Конец документации

**Версия:** 1.0.0 (2.6.0)
**Дата:** 20-09-2026
**Автор:** Longinov Denis, Tashkent
**Лицензия:** MIT
`;
