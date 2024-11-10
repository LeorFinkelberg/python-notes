### Группировка по ключу

Группировка по ключу
```python
df.groupby("gender").agg(
	avg_ratings=pd.NamedAgg(column="rating", aggfunc="mean"),
	num_ratings=pd.NamedAgg(column="userId", aggfunc="nunique"),
)
```

### `apply` по нескольким атрибутам кадра данных

Пусть есть кадр данных с атрибутами `col1` , `col2` и `col3` и требуется построить новый атрибут как 2 * col1 + col2 ** 2
```python
df.apply(
	lambda x: 2 * x["col1"] + x["col2"] ** 2,
	axis=1  # ОБЯЗАТЕЛЬНО axis=1, так как по умолчанию axis=0
)
```
или так
```python
def get_res_udf(x):
    return 2 * x["col1"] + x["col2"] ** 2

df.apply(
	get_res_udf,
	axis=1
)
```
или так
```python
def get_res_udf(col1: float, col2: float) -> float:
    return 2 * col1 + col2 ** 2

df.apply(
	lambda x: get_res_udf(x["col1"], x["col2"]),
	axis=1
)
```