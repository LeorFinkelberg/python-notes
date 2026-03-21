# Базовое использование

Задать версию Python
```bash
uv venv --python 3.12.5 .venv
```
Синхронизация
```bash
# Установить только основные зависимости (из [project.dependencies])
pdm sync

# Установить основные + CPU группу
pdm sync -G cpu-group

# Установить основные + CPU + dev + notebook
pdm sync -G cpu-group -G dev -G notebook

# Установить несколько групп сразу
pdm sync -G cpu-group -G neural-rec -G ytsaurus
```

Можно комбинировать любые группы 
```bash
# Для разработки на CPU с ноутбуками
pdm sync -G cpu-group -G dev -G notebook

# Для продакшена на GPU
pdm sync -G gpu-group
```

```bash
# Создать lock-файл только для CPU группы
pdm lock --lockfile pdm.cpu.lock -G cpu-group

# Создать lock-файл для CPU + dev + notebook
pdm lock --lockfile pdm.dev.lock -G cpu-group -G dev -G notebook

# Создать lock-файл для GPU
pdm lock --lockfile pdm.gpu.lock -G gpu-group
```

Группы (`-G`) это просто именованные наборы дополнительных зависимостей, объявленные в двух местах
```toml
[project.optional-dependencies]  # для опциональных фич
[tool.pdm.dev-dependencies]  # для инструментов разработки
```

Собираем lock-файл для MacOS на базе зависимостей группы `dev`
```bash
pdm lock --lockfile pdm.mac.dev.lock -G dev
# Если для разработки нужны также notebook и другие группы
# pdm lock --lockfile pdm.mac.dev.lock -G dev -G notebook
```
Устанавливаем зависимости
```bash
pdm sync -G dev --lockfile pdm.mac.dev.lock
```

Lock-файлы содержат информацию о платформе, на которой они собирались. Поэтому не получится собрать окружение на базе lock-файла `pdm.dev.lock` (Linux) на MacOS. Получится ошибка
```bash
(.venv) ➜  history-transformer git:(main) ✗ pdm sync -G dev --lockfile pdm.dev.lock

INFO: Inside an active virtualenv /Users/alexander.podvoyskiy/Documents/Projects/history-transformer/.venv, reusing it.

Set env var PDM_IGNORE_ACTIVE_VENV to ignore it.

ERROR: None of the lock targets matches the current env (==3.12.5, macos_26_2_arm64, cpython):

 - (>=3.10.6,<3.13, manylinux_2_40_x86_64)

[PdmException]: No compatible lock target found

WARNING: Add '-v' to see the detailed traceback
```
## Полезные ссылки
- https://pdm-project.org/latest/?ref=playfulpython.com PDM docs