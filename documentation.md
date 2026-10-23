
// WriScroll Language — Documentation
// Версия: 1.0.0 (2.6.0)
// Дата: 20-09-2026
// Автор: Longinov Denis, Tashkent
// Лицензия: MIT
//
// Этот файл — сводная документация для интерпретатора WriScroll.
// Полные версии частей — в отдельных файлах:
//
//   docs/ru/core (ru).md          — Part I. Core
//   docs/ru/security (ru).md      — Part II. Security
//   docs/ru/gui (ru).md           — Part III. GUI
//   docs/ru/data (ru).md          — Part IV. Data
//   docs/ru/normative (ru).md     — Part V. Normative
//   docs/ru/reference (ru).md     — Part VI. Reference
//
// Английские версии — в docs/en/.


export const WRISCROLL_DOCUMENTATION = `
# WriScroll Language — Documentation

**Версия:** 1.0.0 (2.6.0)
**Дата:** 20-09-2026
**Автор:** Longinov Denis, Tashkent
**Лицензия:** MIT

---

## Содержание

- [Часть I. Core](#часть-i-core)
- [Часть II. Security](#часть-ii-security)
- [Часть III. GUI](#часть-iii-gui)
- [Часть IV. Data](#часть-iv-data)
- [Часть V. Normative](#часть-v-normative)
- [Часть VI. Reference](#часть-vi-reference)

Полные версии частей — в отдельных файлах:

- \`docs/ru/core (ru).md\`
- \`docs/ru/security (ru).md\`
- \`docs/ru/gui (ru).md\`
- \`docs/ru/data (ru).md\`
- \`docs/ru/normative (ru).md\`
- \`docs/ru/reference (ru).md\`

Английские версии — в \`docs/en/\`.

---

## Часть I. Core

### Overview

WriScroll — маркерно-ориентированный язык программирования. Каждая инструкция начинается с маркера, который определяет её категорию: состояние (\`::\`), действие (\`>>\`) или проверка (\`?>\`).

Маркер стоит в начале строки. Он виден сразу. Он не требует контекста.

| Маркер | Категория | Смысл |
|--------|-----------|-------|
| \`::\` | Состояние | Создать / настроить / изменить |
| \`>>\` | Действие | Выполнить / получить / запустить |
| \`?>\` | Проверка | Проверить / утвердить / валидировать |

Три категории отвечают на три вопроса:

- \`::\` — **что есть?**
- \`>>\` — **что происходит?**
- \`?>\` — **что истинно?**

### Marker Architecture

**Семантические классы:**

\`\`\`text
                WriScroll Marker
                       │
         ┌─────────────┼─────────────┐
         │             │             │
        ::            >>            ?>
         │             │             │
      STATE          ACTION        TEST
         │             │             │
   создать/изменить   выполнить    проверить
         │             │             │
      |$| |$$|       |DB| |WGET|   |TYP| |VALID|
      |GRID|          |RUN|        |ASSERT|
      |CELL|           |HTTP|       |CHECK|
      |PERM|           |PERM|       |PERM|
      |HASH|           |AUDIT|      |RULE|
\`\`\`

**Матрица маркеров:**

| Маркер | Класс | \`::\` (STATE) | \`>>\` (ACTION) | \`?>\` (TEST) |
|--------|-------|--------------|---------------|-------------|
| \`|$|\` | STATE | ✅ | — | — |
| \`|$$|\` | SYSTEM STATE | ✅ | — | — |
| \`|?|\` | ACTION | — | ✅ | — |
| \`|!|\` | ACTION | — | ✅ | — |
| \`|+|\` | ACTION | — | ✅ | — |
| \`|-|\` | ACTION | — | ✅ | — |
| \`|DB|\` | SYSTEM ACTION | — | ✅ | — |
| \`|WGET|\` | GUI ACTION | — | ✅ | — |
| \`|GRID|\` | STATE | ✅ | — | — |
| \`|CELL|\` | STATE | ✅ | ✅ | — |
| \`|TYP|\` | TEST | ✅ | ✅ | ✅ |
| \`|VALID|\` | TEST | — | — | ✅ |
| \`|ASSERT|\` | TEST | — | — | ✅ |
| \`|F5|\` | DEBUG | ✅ | ✅ | ✅ |
| \`|PERM|\` | SECURITY | ✅ | ✅ | ✅ |
| \`|AUDIT|\` | SECURITY | — | ✅ | — |
| \`|HASH|\` | SECURITY | ✅ | ✅ | — |
| \`|RULE|\` | BUSINESS | — | — | ✅ |

**Декларативные блоки** — описывают СУЩНОСТЬ:

\`SYS::\`, \`FUNC::\`, \`WIDGET::\`, \`TABLE::\`, \`DATASET::\`, \`FILTER::\`, \`ANALYTICS::\`, \`KPI::\`, \`CHART::\`, \`REPORT::\`, \`EXPORT::\`, \`SECURITY::\`, \`CRM::\`, \`FORM::\`.

**Маркеры действий** — совершают ДЕЙСТВИЕ над сущностью:

\`|$|::{}\`, \`|DB|>>{}\`, \`|WGET|>>{}\`, \`|SHOW|>>{}\`, \`|VALID|?>{}\`, \`|PERM|>>{}\`, \`|AUDIT|>>{}\`, \`|HASH|>>{}\`.

**Схема:** DECLARATION → ENTITY → ACTION → RESULT

**ASSIGN vs MUTATE:**

Маркер \`|$|::{}\` охватывает две операции:

- ASSIGN: \`|$|::{x@int = 10}\`, \`|$|::{name@str = "John"}\`.
- MUTATE: \`|$|::{result.append(num)}\`, \`|$|::{user.name = "New Name"}\`.

**Capabilities:**

| Маркер | Capability |
|--------|-----------|
| \`|DB|>>\` | \`database.query\` |
| \`|FILE|>>\` | \`filesystem.read/write\` |
| \`|HTTP|>>\` | \`network.http\` |
| \`|PROC|>>\` | \`process.execute\` |
| \`|SYS|>>\` | \`system.admin\` |
| \`|PERM|>>\` | \`security.manage\` |
| \`|AUDIT|>>\` | \`security.audit\` |

### Program Structure

**Системный модуль:**

\`\`\`text
SYS::ModuleName:
  # тело модуля
::SYS
\`\`\`

**Пользовательская функция:**

\`\`\`text
FUNC::FunctionName:
  {{param1@type, param2@type}}
  # тело функции
::FUNC
\`\`\`

**Точка входа:**

\`\`\`text
MAIN::start:
  # основной код
::MAIN
\`\`\`

**SYS vs FUNC:**

- \`SYS::\` — объект-контейнер (namespace) с полями.
- \`FUNC::\` — функция (возвращает значение).

**CRM каркас:**

\`\`\`text
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
\`\`\`

Runtime проверяет наличие \`SECURITY\` перед запуском. Без \`SECURITY\` приложение не запускается.

### Types

**Базовые типы:**

| Тип | Описание | Пример |
|-----|----------|--------|
| \`@int\` | Целое число | \`10\`, \`-5\`, \`0\` |
| \`@str\` | Строка | \`"John"\` |
| \`@flt\` | Число с плавающей точкой | \`3.14\`, \`-0.5\` |
| \`@bol\` | Булево значение | \`true\` / \`false\` |
| \`@arr\` | Массив | \`[1, 2, 3]\` |
| \`@obj\` | Объект / словарь | \`{"key": "value"}\` |

**Финансовые типы:**

| Тип | Описание | Пример |
|-----|----------|--------|
| \`@mon\` | Деньги (2 знака, валюта) | \`150000 UZS\`, \`250 USD\` |
| \`@dec(n)\` | Десятичное с точностью \`n\` | \`@dec(3)\` → \`1.235\` |
| \`@prc\` | Проценты (2 знака, вывод \`%\`) | \`20.555\` → \`20.56%\` |
| \`@cr\` | Координата (6 знаков) | — |
| \`@frac\` | Дробь | — |
| \`@prec\` | Высокая точность | — |

**Дата и время:**

| Тип | Описание |
|-----|----------|
| \`@date\` | Дата (\`YYYY-MM-DD\`) |
| \`@datetime\` | Дата и время (\`YYYY-MM-DD HH:MM:SS\`) |
| \`@time\` | Время (\`HH:MM:SS\`) |
| \`@duration\` | Длительность |
| \`@email\` | Адрес электронной почты |

**Правило:** \`@str\` никогда не исполняется как код. Безопасность обеспечивается разделением \`CODE ≠ DATA\`.

**Функции для работы с датами:**

\`TODAY\`, \`NOW\`, \`YEAR(date)\`, \`MONTH(date)\`, \`WEEK(date)\`, \`DAY(date)\`, \`CURRENT_MONTH\`, \`PREVIOUS_MONTH\`, \`START_OF_MONTH(date)\`.

### Markers Reference

**Правило использования:**

- \`::\` — ПРИСВОЕНИЕ.
- \`>>\` — ВЫПОЛНЕНИЕ.
- \`?>\` — ПРОВЕРКА.

**Пользовательские маркеры:**

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|$|::{}\` | Переменная (ASSIGN / MUTATE) | \`|$|::{x@int = 10}\` |
| \`|?|>>{}\` | Вывод на экран | \`|?|>>{"Hello, World!"}\` |
| \`|!|>>{}\` | Возврат значения | \`|!|>>{result}\` |
| \`|+|>>{}\` | Добавление элемента | \`|+|>>{users new_user}\` |
| \`|-|>>{}\` | Удаление элемента | \`|-|>>{users user_id}\` |

**Системные маркеры:**

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|$$|::{}\` | Системная настройка | \`|$$|::{"debug": true}\` |
| \`|??|>>{}\` | Системная команда | \`|??|>>{"CONNECT TO database"}\` |

**Отладка:**

| Маркер | Назначение |
|--------|-----------|
| \`|F5|::{}\` | Настройка отладки |
| \`|F5|>>{}\` | Вывод отладочной информации |
| \`|F5|?>{}\` | Проверка условия в отладке |

**Ошибки:**

\`TRY:\`, \`EXP error:\`, \`FNL:\`, \`|!!|>>{}\`.

**Типы:**

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|TYP|?>{}\` | Проверка типа | \`|TYP|?>{x IS int}\` |
| \`|TYP|>>{}\` | Получение типа | \`|$|::{type@str = |TYP|>>{x}}\` |
| \`|TYP|::{x TO str}\` | Приведение типа | — |

**GUI:**

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|GRID|::{}\` | Создание сетки | \`|GRID|::{"cols": 4, "rows": 3}\` |
| \`|CELL|::{}\` | Добавление ячейки | \`|CELL|::{"col": 0, "row": 0}\` |
| \`|CELL|>>{}\` | Обновление ячейки | \`|CELL|>>{"text": "8"}\` |
| \`|WGET|>>{}\` | Вставка виджета | \`|WGET|>>{"type": "button"}\` |
| \`|SHOW|>>{}\` | Отображение | \`|SHOW|>>{"Dashboard"}\` |
| \`|RUN|>>{}\` | Запуск процесса | \`|RUN|>>{"BackupService"}\` |
| \`|EVENT|::{}\` | Объявление события | \`|EVENT|::{"id": "page_changed"}\` |
| \`|TIMER|::{}\` | Таймер | \`|TIMER|::{"interval": 5000}\` |

**Безопасность:**

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|PERM|>>{}\` | Управление правами | \`|PERM|>>{"allow": ...}\` |
| \`|PERM|::{}\` | Настройка прав | \`|PERM|::{"default_policy": "deny"}\` |
| \`|PERM|?>{}\` | Проверка права | \`|PERM|?>{resource: "students", action: "CREATE"}\` |
| \`|AUDIT|>>{}\` | Журналирование | \`|AUDIT|>>{"action": "CREATE"}\` |
| \`|HASH|>>{}\` | Хеширование | \`|HASH|>>{"admin123"}\` |
| \`|HASH|::{}\` | Настройка хеширования | \`|HASH|::{"algorithm": "bcrypt"}\` |

**Системные маркеры администрирования:**

\`|SYS|>>{}\`, \`|PROC|>>{}\`, \`|SERV|>>{}\`, \`|CRON|::{}\`, \`|LOG|>>{}\`, \`|WARN|>>{}\`, \`|ALERT|>>{}\`, \`|METRIC|>>{}\`, \`|ASSERT|?>{}\`, \`|VALID|?>{}\`, \`|CHECK|?>{}\`, \`|RULE|?>{}\`, \`|IN|>>{}\`, \`|OUT|>>{}\`, \`|LEN|>>{}\`, \`|NET|>>{}\`, \`|HTTP|>>{}\`, \`|SOCKET|>>{}\`, \`|FILE|>>{}\`, \`|DIR|>>{}\`, \`|DB|>>{}\`, \`|BACKUP|>>{}\`, \`|MIGRATE|>>{}\`, \`|AUTH|>>{}\`, \`|ENCRYPT|>>{}\`, \`|SCAN|>>{}\`.

### Control Flow

**Условия:**

\`\`\`text
IF condition:
  # код выполняется, если condition истинно
:IF

EF condition:
  # код выполняется, если condition2 истинно
:EF

ES:
  # код выполняется, если все условия ложны
:ES
\`\`\`

**Циклы:**

\`\`\`text
FOR i = 1 to 10:
  # код повторяется 10 раз
:FOR

FOR i = 0 to 100 step 10:
  # код повторяется с шагом 10
:FOR

FOR item in data:
  # код для каждого item
:FOR

|$|::{x@int = 0}
WHILE x < 10:
  |$|::{x = x + 1}
:WHILE
\`\`\`

**Управление циклами:**

- \`STOP\` — прерывает выполнение цикла.
- \`NEXT\` — переходит к следующей итерации.

**Обработка ошибок:**

\`\`\`text
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
\`\`\`

**Работа с типами:**

\`\`\`text
|TYP|?>{x IS int}    # Возвращает true, если x — целое число
|$|::{type@str = |TYP|>>{x}}
|TYP|::{x TO str}   # Преобразует x в строку
\`\`\`

### Debugging

\`\`\`text
|F5|::{"level": "verbose"}
|F5|::{"log_file": "debug.log"}

|F5|>>{"Вход в функцию"}
|F5|>>{"x = " + x}

|F5|?>{x > 0}:
  |F5|>>{"x положительное"}
\`\`\`

---

## Часть II. Security

### SECURITY

\`SECURITY::\` — обязательный блок в CRM-приложении. Без \`SECURITY\` приложение не запускается.

\`\`\`text
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
\`\`\`

### Capabilities

| Маркер | Capability |
|--------|-----------|
| \`|DB|>>\` | \`database.query\` |
| \`|FILE|>>\` | \`filesystem.read/write\` |
| \`|HTTP|>>\` | \`network.http\` |
| \`|PROC|>>\` | \`process.execute\` |
| \`|SYS|>>\` | \`system.admin\` |
| \`|PERM|>>\` | \`security.manage\` |
| \`|AUDIT|>>\` | \`security.audit\` |

Это архитектура **«Тюрьма»** (Security Architecture).

### Уровни безопасности

1. **ВНЕШНЯЯ СТЕНА** — проверка входящих данных (\`|VALID|?>\`).
2. **НАДСТРОЙКА** — аутентификация и авторизация (\`|AUTH|>>\`).
3. **ПЕСОЧНИЦА** — изоляция выполнения (\`|$$|::{"sandbox": true}\`).
4. **ЯДРО** — выполнение базового кода (\`|$|\`, \`|?|\`, \`|!|\`).

### Принципы

- \`DATA ≠ CODE\` — данные не исполняются как код.
- \`GUI ≠ SECURITY\` — интерфейс не является границей безопасности.
- \`FILTER ≠ VALIDATION\` — фильтрация не заменяет валидацию.
- \`PERMISSION ≠ VISIBILITY\` — право не равно видимости.

### PERM

Три режима:

| Маркер | Назначение | Пример |
|--------|-----------|--------|
| \`|PERM|::{}\` | Настройка политики | \`|PERM|::{"default_policy": "deny"}\` |
| \`|PERM|>>{}\` | Выдача прав | \`|PERM|>>{"allow": {"resource": "students", "action": "READ"}}\` |
| \`|PERM|?>{}\` | Проверка права | \`|PERM|?>{resource: "students", action: "CREATE"}\` |

**Действия:** \`READ\`, \`CREATE\`, \`UPDATE\`, \`DELETE\`, \`WRITE\`.

**Правило:** \`WRITE = CREATE + UPDATE\`.

### Scope

Scope ограничивает **не только интерфейс**, но и **фактически доступные данные**.

\`|$$|::{"apply_scope": true}\` указывает runtime, что \`DATASET\` должен применять scope до передачи данных в GUI.

**Правильный порядок:**

\`\`\`text
SOURCE
  ↓
PERMISSION / SCOPE CONTEXT
  ↓
SCOPE FILTER
  ↓
DATASET.data
  ↓
GUI
\`\`\`

**Неправильно:**

\`\`\`text
SOURCE
  ↓
DATASET.data
  ↓
GUI
  ↓
визуальное скрытие
\`\`\`

Скрытие строк или кнопок в GUI **не является** механизмом безопасности.

**StudentSubjects** — reference-паттерн many-to-many связи.

**Именование Scope:** уникально в пределах приложения.

**Примеры:** \`teacher_students\`, \`teacher_grades\`, \`self_students\`, \`self_payments\`.

### Audit

\`\`\`text
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
\`\`\`

### Security Pipeline

\`\`\`text
UNTRUSTED INPUT
      ↓
    |IN|>>          получение входных данных
      ↓
  |CHECK|?>         проверка структуры и типов
      ↓
  |VALID|?>         бизнес-валидация значения
      ↓
  |PERM|?>          проверка права
      ↓
   |RULE|?>         бизнес-правило
      ↓
    |DB|>>          запись / изменение
      ↓
  |AUDIT|>>         журналирование
      ↓
   |OUT|>>          безопасный результат
      ↓
     |!|>>          возврат значения функции
\`\`\`

### Consistency Rule

Если \`PERM\`, \`SCOPE\`, \`CHECK\`, \`VALID\` или обязательный \`RULE\` не пройдены, \`DB\`-операция **не выполняется**.

---

## Часть III. GUI

### Структура

| Сущность | Что | Уровень |
|----------|-----|---------|
| \`GRID::\` | Сетка | Верхний |
| \`CELL::\` | Ячейка сетки | Внутри \`GRID::\` |
| \`TRELLIS::\` | Позиция внутри ячейки | Внутри \`CELL::\` |
| \`ITEM::\` | Позиция внутри trellis | Внутри \`TRELLIS::\` |
| \`|WGET|>>\` | Вставка виджета | Внутри \`TRELLIS::\` / \`ITEM::\` |
| \`|SHOW|>>\` | Отображение | Где угодно |

**Позиции TRELLIS:** \`lt\`, \`ct\`, \`rt\`, \`lm\`, \`cm\`, \`rm\`, \`lb\`, \`cb\`, \`rb\`, \`all\`.

**Позиции ITEM:** \`ilt\`, \`ict\`, \`irt\`, \`ilm\`, \`icm\`, \`irm\`, \`ilb\`, \`icb\`, \`irb\`, \`iall\`.

**Правила:**

- \`TRELLIS → ITEM → TRELLIS → ITEM → ...\` без ограничения.
- \`ITEM\` внутри \`ITEM\` — запрещено.

### Типы виджетов

\`text\`, \`button\`, \`chart\`, \`table\`, \`slider\`, \`input\`, \`image\`, \`progress\`, \`metric\`, \`container\`.

### WSS — WriScroll Styles

**Маркеры:**

| Маркер | Назначение |
|--------|-----------|
| \`%\` | Стиль (class) |
| \`&\` | Псевдокласс |
| \`|media|::\` | Медиа-запрос |
| \`extends:\` | Наследование |

**Пример:**

\`\`\`text
%btn {
  bg: #4fc3f7
  color: #0a0e1c
  border_radius: 8px
  padding: 14px
  font_size: 16px
  cursor: pointer
}

$head {
  size: 60px auto
  bg: #141b33
  padding: 16px
}

%crm_layout {
  area: [(0,0), (1,0), (2,0)] as $head
  area: (1,1) as $main
  area: [(0,2), (1,2), (2,2)] as $footer

  gap: 8px
  padding: 8px
  width: 100vw
  height: 100vh
}

$head_admin {
  extends: $head
  bg: #2d1b33
  border: 2px solid gold
}

$head {
  size: 60px auto

  |media|::(max-width: 640px) {
    size: 40px auto
    padding: 8px
  }
}
\`\`\`

**Единицы:** \`px\`, \`%\`, \`flex\`, \`auto\`, \`vh\`, \`vw\`, \`em\`, \`rem\`.

**Приоритеты стилей:** \`$area\` < \`CELL::$area\` < \`%class\` < путь-селектор < \`|media|::\` < \`"style"\` в \`|WGET|>>\`.

### Charts

**Типы:** \`linechart\`, \`areachart\`, \`sparkline\`, \`multiline\`, \`bars\`.

**Общие параметры:** \`type\`, \`data\`, \`label\`, \`color\`, \`height\`, \`smooth\`.

**Правила:**

1. Графики вставляются только через \`|WGET|>>{}\` в \`CELL::\`.
2. Данные — массив \`@arr\`. Мутируй (\`.append\` / \`.clear\`).
3. После изменения данных — вызови \`|WGET|>>\` для перерисовки.

---

## Часть IV. Data

### DATASET

\`\`\`text
DATASET::Students:
  |$$|::{"source": "DB"}
  |$$|::{"table": "students"}
  |$|::{fields@arr = ["id", "name", "age"]}
  |DB|>>{"CONNECT TO " + source}
  |DB|>>{"SELECT " + fields + " FROM " + table}
  |$|::{data@arr = |DB|>>{result}}
  |!|>>{data}
::DATASET
\`\`\`

**Параметры:** \`source\`, \`table\`, \`fields\`, \`apply_scope\`.

### TABLE

\`\`\`text
TABLE::OrdersTable ("cols": 3):
  HEADERS: ["ID", "Клиент", "Товары"]
  DATA: orders

  COL(3)::ItemsTable:
    HEADERS: ["Название", "Цена", "Кол-во"]
    DATA: row.items
  ::COL(3)
::TABLE
\`\`\`

### Financial Types

| Тип | Описание | Пример |
|-----|----------|--------|
| \`@mon\` | Деньги (2 знака, валюта) | \`150000 UZS\` |
| \`@prc\` | Проценты (2 знака, вывод \`%\`) | \`20.555\` → \`20.56%\` |
| \`@dec(n)\` | Десятичное с точностью \`n\` | \`@dec(3)\` → \`1.235\` |

**Правила:**

- \`@mon\` — 2 знака, валюта сохраняется.
- \`@prc\` — 2 знака, вывод \`%\`.
- \`@dec(n)\` — \`0 ≤ n ≤ 15\`.
- Валюты нельзя смешивать.
- \`@prc\` нельзя присвоить в \`@mon\` без явного приведения.
- \`money * prc\` → \`money\` (процент).
- \`money + prc\` → надбавка; \`money - prc\` → скидка.

### Postfix %

**Постфиксный \`%\` — процент:**

\`\`\`text
price * 20%   → 20% от price (money)
price + 20%   → price * 1.20 (надбавка)
price - 15%   → price * 0.85 (скидка)
\`\`\`

**Бинарный \`%\` — остаток:**

\`\`\`text
17 % 5 → 2
\`\`\`

**Различие:** если справа от \`%\` — операнд, оператор бинарный (остаток); если конец выражения — постфиксный (процент).

### Rounding, Formatting, Conversion

**ACTION-маркеры:**

\`\`\`text
|ROUND|>>{value, "bankers"}
|ROUND|>>{value, "ceil"}
|ROUND|>>{value, "floor"}
|ROUND|>>{value, "half_up"}

|FORMAT|>>{value, "USD"}   → "$120.56"
|FORMAT|>>{value, "EUR"}   → "€120.56"
|FORMAT|>>{value, "RUB"}   → "120.56 ₽"
|FORMAT|>>{value, "UZS"}   → "120.56 UZS"
|FORMAT|>>{value, "KZT"}   → "120.56 ₸"
|FORMAT|>>{tax@prc}        → "20.56%"

|CURRENCY|>>{value}        → "USD" / ""
|AMOUNT|>>{value}          → 120.56
|IS_SAME|>>{a, b}          → true / false
|CONVERT|>>{value, "EUR", 1.1}
|CHECK_PRECISION|>>{value, "mon", 2}
\`\`\`

---

## Часть V. Normative

### WriScroll 2.5.1 — Security, Input & Business CRM

**Принцип:**

- \`PERM\` определяет, **ЧТО** пользователь может сделать.
- \`SCOPE\` определяет, **С КАКИМИ ЗАПИСЯМИ** он может работать.
- \`apply_scope=true\` применяет ограничение на уровне \`DATASET\`, **ДО** GUI.

**Ключевые правила:**

1. \`WRITE = CREATE + UPDATE\`.
2. \`PERM = action\`, \`SCOPE = record boundary\`.
3. Имя Scope уникально в пределах приложения.
4. \`apply_scope\` применяется runtime на уровне \`DATASET\`.
5. Scope проверяется и при \`CREATE\`.
6. \`RULE\` применяется после проверки права, но до изменения данных.
7. Явные ссылки на входные данные в \`RULE\`: \`EXISTING_RECORD.field\` vs \`INPUT.field\`.
8. \`name\` сам по себе не является уникальным ключом.
9. AutoID — учебный механизм; для production — UUID / sequence.
10. \`GUI ≠ SECURITY\`.

**Consistency Rule:** если \`PERM\`, \`SCOPE\`, \`CHECK\`, \`VALID\` или обязательный \`RULE\` не пройдены — \`DB\`-операция не выполняется.

### WriScroll 1.0.0 (2.6.0) — Strict Typing & Precision

**Принцип:**

- \`TYPING\` определяет, **С КАКОЙ ТОЧНОСТЬЮ** и **В КАКОЙ ВАЛЮТЕ**.

**Ключевые правила:**

1. \`@mon\` — 2 знака, валюта сохраняется.
2. \`@prc\` — 2 знака, вывод \`%\`.
3. \`@dec(n)\` — \`0 ≤ n ≤ 15\`.
4. Валюты нельзя смешивать.
5. Постфиксный \`%\` — процент. Бинарный \`%\` — остаток.
6. \`@prc\` нельзя присвоить в \`@mon\` без явного приведения.
7. \`DATA ≠ CODE\`. \`@str\` никогда не исполняется как код.
8. Все финансовые операции детерминированы.

### WriScroll 1.0.0 (2.6.0) — Charts & Visualization

**Типы:** \`linechart\`, \`areachart\`, \`sparkline\`, \`multiline\`, \`bars\`.

**Ключевые правила:**

1. Графики вставляются через \`|WGET|>>{}\` в \`CELL::\`.
2. Данные — \`@arr\`. Мутируй (\`.append\` / \`.clear\`).
3. После изменения данных — \`|WGET|>>\` для перерисовки.
4. \`color\` — hex. \`height\` — px. \`smooth\` — флаг.
5. Мультисерии — массив массивов, цвета автоматически.

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
