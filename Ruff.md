Ruff -- это линтер и форматер https://docs.astral.sh/ruff/ 
![[Screenshot 2026-08-15 at 15.36.06.png|568]]

Ruff может использоваться вместо flake8, isort, black, pydocstyle etc.
![[Screenshot 2026-08-15 at 15.38.37.png|700]]

Установить `ruff` можно глобально (прочие варианты описываются здесь https://docs.astral.sh/ruff/installation/)
```bash
# Install Ruff globally
uv tool install ruff@latest
```

В режиме check
```bash
ruff check                  # Lint files in the current directory.
ruff check --fix            # Lint files in the current directory and fix any fixable errors.
ruff check --watch          # Lint files in the current directory and re-lint on change.
ruff check path/to/code/    # Lint files in `path/to/code`.
```

Чтобы подавить предупреждения линтера, можно добавить в `pyproject.toml` список кодов правил
```bash
# pyproject.toml
...
[tool.ruff.lint]
select = ["E", "F"]
ignore = ["F401"]
```

Режим format
```bash
ruff format                   # Format all files in the current directory.
ruff format path/to/code/     # Format all files in `path/to/code` (and any subdirectories).
ruff format path/to/file.py   # Format a single file.
```