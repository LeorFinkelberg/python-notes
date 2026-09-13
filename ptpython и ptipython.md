Альтернатива IPython новый пакет `ptipython`. Установить можно так
```bash
uv add ptipython
```

Конфиг для оболочки выглядит так
```python
# ~/.config/ptpython
def configure(repl):
    repl.vi_mode = True
    repl.confirm_exit = False
```