У CPython есть одна уникальная особенность: эта реализация содержит как ==_среду выполнения_==, так и ==_общую спецификацию языка_==, которая используется всеми остальными реализациями Python. CPython является официальной (или эталонной) реализацией Python.

_Спецификация языка Python_ представляет собой документ с описанием языка Python. Например, в ней сказано, что `assert` - ключевое слово, а `[]` используется для индексирования, срезов и создания пустых списков.

Чтобы загрузить копию последней версии исходного кода CPython, можно воспользоваться `git`
```bash
$ git clone --branch 3.10 https://github.com/python/cpython
```

