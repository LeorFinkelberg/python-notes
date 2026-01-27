Перед началом работы, в директории проекта вызываем `uv init`. После этого в текущей директории будут созданы `.git`, `.gitignore`, `.python-version`, `main.py`, `README.md` и `pyproject.toml`. В `.python-version` будет указана версия Python.

Добавить зависимость в проект можно так
```bash
uv add ipython
```
Если нужна какая-то конкретная версия, то так
```bash
uv add "ipython==9.9.0"
```
NB! Эта команда обновит `pyproject.toml`, `uv.lock` и окружение одной командой.

Обновить конкретный пакет до последней совместимой версии (с учетом ограничений `pyproject.toml`) можно так
```bash
uv sync --upgrade-package <package-name>
```
или можно обновить lock-файл и окружение
```bash
uv lock --upgrade-package <package-name>
uv sync
```
Это обновит указанный пакет до последней совместимой версии без нарушения ограничений в `pyproject.toml` и запишет новую версию в `uv.lock`.

NB! Если в `pyproject.toml` зависимость описывается как конкретная версия, например, `ipython==9.1.0`, то `uv lock --upgrade-package ...` ничего не изменит. Но если зависимость описать как `ipython>=9.1.0`, то `uv lock --upgrade-package ...` обновит версию и укажет правильную версию в `uv.lock`.


