Вот основные команды для работы с `tmux` — утилитой для управления терминальными сессиями:

### Запуск и подключение к сессии

- **`tmux`** — запустить новую сессию.
- **`tmux new -s <name>`** — создать новую сессию с именем `<name>`.
- **`tmux ls`** — список всех активных сессий.
- **`tmux attach -t <name>`** — подключиться к существующей сессии по имени.
- **`tmux detach`** — отсоединиться от текущей сессии (по умолчанию: `Ctrl+b d`).

### Основные клавиши управления (по умолчанию префикс: `Ctrl+b`)

- **`Ctrl+b c`** — создать новое окно.
- **`Ctrl+b n`** — перейти к следующему окну.
- **`Ctrl+b p`** — перейти к предыдущему окну.
- **`Ctrl+b w`** — список всех окон для выбора.
- **`Ctrl+b 0-9`** — перейти к окну по его номеру.

### Управление окнами и панелями

- **`Ctrl+b %`** — разделить окно вертикально (создать панель).
- **`Ctrl+b "`** — разделить окно горизонтально.
- **`Ctrl+b x`** — закрыть панель.
- **`Ctrl+b o`** — переключиться между панелями.
- **`Ctrl+b z`** — развернуть панель на весь экран (и обратно).
- **`Ctrl+b q`** — показать номера панелей для быстрого выбора.

### Режим копирования и буфер обмена

- **`Ctrl+b [`** — войти в режим копирования.
- **`Ctrl+b ]`** — вставить текст из буфера.
- **`q`** — выйти из режима копирования.
- **`Space`** — начать выделение текста.
- **`Enter`** — завершить выделение и скопировать в буфер.

### Завершение и выход

- **`Ctrl+b &`** — закрыть текущее окно.
- **`tmux kill-session -t <name>`** — закрыть сессию по имени.
- **`tmux kill-server`** — завершить работу всех сессий и остановить сервер `tmux`.

Эти команды помогут эффективно работать с терминальными сессиями в `tmux`, управлять окнами, панелями и сессиями.