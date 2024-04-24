Для примера рассмотрим связку асинхронных библиотек
```python
from typing import Optional, TYPE_CHECKING
from prefect import task
from .base import register

if TYPE_CHECKING:
    import pandas as pd

@register(block_type="connector")
@task(name="postgre connector", log_prints=True)
async def postgre_connector(
    query: str,
    host: str,
    port: int,
    user: str,
    database: str,
    password: str,
    chunk_size: Optional[int] = None,
    pool_interval: int = 10,
    prefetch: int = 50,
    repeat: bool = False,
) -> "pd.DataFrame":
    """Коннектор к PostgreSQL.

    Args:
        query (str): Текст SQL запроса
        host (str): Хост
        port (int): Порт
        user (str): Имя пользователя
        database (str): Имя базы данных
        password (str): Пароль
        chunk_size (Optional[int], optional): Размер пакета,
        который будет возвращаться на каждой итерации.
        Если None возвращается весь запрос целиком. По умолчанию None.
        pool_interval (int, optional): Интервал времени в секундах между
        возвращением следующего пакета. По умолчанию 10.
        prefetch (int, optional): Объем предзагрузки строк из базы. По умолчанию 50.
        repeat (bool, optional): Повторение запроса.
        Если True будет повторяться один и тот же запрос в бесконечном цикле.
        По умолчанию False.

    Yields:
        pd.DataFrame: Результат запроса в формате таблицы.
    """
    import asyncio
    import asyncpg
    from aiostream import stream
    import pandas as pd

    # Получаем объект соединения
    connection = await asyncpg.connect(
        host=host, port=port, user=user, database=database, password=password
    )
    while True:
        # Асинхронный менеджер контекста
        async with connection.transaction():
            stmt = await connection.prepare(query)
            columns = [atr.name for atr in stmt.get_attributes()]
            if chunk_size:
                # Асинхронный цикл for
                async for chunk in stream.chunks(
                    connection.cursor(query, prefetch=prefetch), chunk_size
                ):
                    yield pd.DataFrame(chunk, columns=columns)
                    await asyncio.sleep(pool_interval)
            else:
                result = await connection.fetch(query)
                yield pd.DataFrame(result, columns=columns)
        if not repeat:
            break
        await asyncio.sleep(pool_interval)

    await connection.close()
```