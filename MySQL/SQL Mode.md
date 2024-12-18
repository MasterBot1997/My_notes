
---

- https://mariadb.com/kb/en/sql-mode/



---

-  https://www.hostcms.ru/forums/2/13020/

Собственно я и имел в виду настройку своего MySQL. К движку претензий нет никаких. Вопрос решил несколько иначе на глобальном уровне:  
1. Необходимо создать файл **etc/mysql/conf.d/disable_strict_mode.cnf**  
2. Выставить права на файл Владелец - MySQL  Группа - mysql  
3. В теле файла прописать  
    [mysqld]  
    sql_mode=""  
4. Перезагрузите MySQL с помощью этой команды:  
    sudo service mysql restart  
5. Желательно проверить статус MySQL:  
    systemctl status mysql.service  
  
Если в дальнейшем возникнут какие-либо проблемы все можно вернуть путем простого удаления данного файла с последующей перезагрузкой MySQL.

---

### Вариант GPT

`sql_mode` — это системная переменная в MySQL, которая определяет режим работы SQL-сервера. Она влияет на синтаксис, поведение запросов и обработку данных. Настройка `sql_mode` позволяет адаптировать MySQL под разные требования, включая поддержку стандарта SQL и совместимость с другими СУБД.

Каждый режим в `sql_mode` включает или отключает определенные правила. Эти правила могут влиять на выполнение запросов, обработку ошибок, поведение функций, работу с NULL-значениями и т.д. 

### Зачем нужен `sql_mode`
- **Контроль за корректностью данных** — ограничивает допустимые значения и проверяет корректность запросов. Например, можно включить строгий режим для запрета вставки неверных данных.
- **Совместимость с другими SQL-серверными системами** — режимы можно настроить так, чтобы добиться похожего поведения на других системах (например, Oracle или PostgreSQL).
- **Соблюдение стандартов SQL** — добавляет поддержку функций и поведения, соответствующих стандартам SQL.

### Как настроить `sql_mode`
`sql_mode` можно задать в конфигурационном файле `my.cnf` или временно изменить в сессии. 

#### 1. Установка через конфигурационный файл `my.cnf`:
Добавьте строку с нужными режимами под `[mysqld]`:

```ini
[mysqld]
sql_mode = "STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION"
```

#### 2. Установка через команду SQL (для текущей сессии):
Можно также настроить режим для текущей сессии, используя команду:

```sql
SET SESSION sql_mode = 'STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION';
```

### Основные режимы `sql_mode`

Некоторые популярные режимы и их функции:

- **`STRICT_TRANS_TABLES`** — запрещает вставку или обновление строк с неверными значениями. Если данные не соответствуют типу или ограничениям столбца, запрос не будет выполнен.
- **`NO_ZERO_DATE`** — запрещает использование "нулевой" даты (`0000-00-00`) в `DATE` и `DATETIME` полях.
- **`NO_ZERO_IN_DATE`** — запрещает даты с нулями в дне или месяце (например, `2023-00-15`).
- **`NO_ENGINE_SUBSTITUTION`** — если указанный движок хранения (например, `InnoDB` или `MyISAM`) недоступен, сервер выдаст ошибку, вместо автоматической замены движка.
- **`ONLY_FULL_GROUP_BY`** — требует, чтобы все столбцы, не входящие в агрегатные функции (`SUM`, `COUNT`), присутствовали в `GROUP BY`.
- **`ANSI_QUOTES`** — позволяет использовать двойные кавычки (`"`) для обозначения идентификаторов (например, названий таблиц или столбцов), как это принято в стандарте ANSI SQL.
- **`PIPES_AS_CONCAT`** — позволяет использовать оператор `||` для объединения строк, как в некоторых других SQL-системах.
  
### Примеры использования

1. **Запрет некорректных данных с `STRICT_TRANS_TABLES`**:

   ```sql
   SET SESSION sql_mode = 'STRICT_TRANS_TABLES';
   INSERT INTO users (age) VALUES (-5);  -- выдаст ошибку, если поле `age` не позволяет отрицательные значения
   ```

2. **Строгое поведение с датами с `NO_ZERO_DATE`**:

   ```sql
   SET SESSION sql_mode = 'NO_ZERO_DATE';
   INSERT INTO orders (order_date) VALUES ('0000-00-00');  -- выдаст ошибку
   ```

3. **Требование `GROUP BY` всех неагрегированных столбцов с `ONLY_FULL_GROUP_BY`**:

   ```sql
   SET SESSION sql_mode = 'ONLY_FULL_GROUP_BY';
   SELECT customer_id, MAX(order_date) FROM orders;  -- выдаст ошибку без `GROUP BY customer_id`
   ```

### Как проверить текущий `sql_mode`

Для проверки текущего значения `sql_mode` выполните команду:

```sql
SELECT @@GLOBAL.sql_mode;  -- для глобального режима
SELECT @@SESSION.sql_mode; -- для текущей сессии
```

### Итог
`sql_mode` позволяет контролировать поведение MySQL и адаптировать его под нужные стандарты или более строгие требования к данным.