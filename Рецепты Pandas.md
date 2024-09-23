Группировка по ключу
```python
df.groupby("gender").agg(
	avg_ratings=pd.NamedAgg(column="rating", aggfunc="mean"),
	num_ratings=pd.NamedAgg(column="userId", aggfunc="nunique"),
)
```
