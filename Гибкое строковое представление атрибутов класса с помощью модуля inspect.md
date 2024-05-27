Чтобы не привязывать на мертво реализацию дандер-метода `__repr__()` к именам атрибутов экземпляра класса, можно воспользоваться такой реализацией
```python
import typing as t
import inspect 

class ...
    def __repr__(self) -> str:
        _args: t.List[str] = []
        _class_args = tuple(inspect.signature(type(self)).parameters.keys())
        _obj_attrs = self.__dict__

    for key, value in _obj_attrs.items():
        if key in _class_args:
            _args.append(f"{key}={value!r}")

    args = ", ".join(_args) 

    return f"{type(self).__name__}({args})"
```