Иногда для работы с альтернативными способами создания экземпляра класса бывает полезно использовать классовые методы
```python
import inspect
import typing as t

class Point(t.NameTuple):
	x: float
	y: float
	z: float

    @classmethod
    def make_point(
        cls,
        x: float = 0.,
        y: float = 0.,
        z: float = 0.,
    ) -> "Point":
        _attrs = (x, y, z) 

        if not all(_attrs):
            _attr_names: t.Tuple[str] = tuple(inspect.signature(cls).parameters.keys())
            _attrs = dict.fromkeys(_attr_names, 0.)

            return cls(*_attrs)
        return cls(*_attrs)
```