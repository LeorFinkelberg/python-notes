Сценарий командной оболочки `./configure` генерирует `Makefile` в корне репозитория. Подробности для разработчиков можно найти на [Python Developer's Guide](https://devguide.python.org/)  
Сборка займет несколько минут, и в результате будет сгенерирован исполняемый файл `python`. Каждый раз, когда вы вносите изменения в исходный код, придется перезапускать `make` .

Для того чтобы установить  Python из пользовательского репозитория как специализированную версию, следует выполнить
```bash
# for Linux and MacOS
$ make altinstall
```

Когда вы выполняете `./configure`, утилита `autoconf` ищет в вашей системе библиотеки, необходимые для CPython, и копирует их пути в `Makefile`. Сгенерированный `Makefile` напоминает скрипт командной оболочки и делится на части, называемые _целями_ (targets).

Для примера рассмотрим цель `docclean`. Она удаляет сгенерированные файлы документации командой `rm`
```bash
docclean:
    -rm -rf Doc/build
    -rm -rf Doc/tools/sphinx Doc/tools/pygments Doc/tools/docutils
```

Чтобы реализовать эту цель, выполните команду `make docclean`. Если вызвать `make` без указания цели, команда запустит цель по умолчанию (первая цель, указанная `Makefile`). В CPython это цель `all`, компилирующая все части CPython.

После выполнения установки с помощью `make install` команда `python3` свяжется со скомпилированным двоичным файлом. Однако при использовании `make altinstall` установится только `python$(version)`, а существующая ссылка на `python3` останется неизменной.

### Профильная оптимизация

Сборка на MacOS, Linux и Windows поддерживает флаги _профильной оптимизации_ (PGO). PGO выполняет исходную компиляцию, после чего профилирует приложение, выполняя серию тестов. Затем профиль анализируется, и компилятор вносит в двоичный файл изменения для повышения производительности.

>[!NOTE]
>Если вы хотите использовать специально скомпилированные версии CPython на продакшен, выполните команду `./configure` с флагом `--with-pgo` на Linux и MacOS или добавьте флаг `--pgo` в `build.bat` на Windows [[Литература#^e7aa31]]

Так как оптимизация привязана к платформе и архитектуре, для которой проводилось профилирование, профили PGO не могут совместно использоваться в разных операционных системах или архитектурах процессоров. Дистрибутивы CPython на [python.org](https://www.python.org/) уже прошли PGO, и если вы протестируете производительность скомпилированного в базовой конфигурации двоичного файла, он будет будет медленнее загруженного с [python.org](https://www.python.org) 

Профильные оптимизации на Windows, MacOS и Linux включают следующие проверки [[Литература#^e7aa31]]<с. 57>
- Встроенные функции: если функция часто вызывается из другой функции, она будет вложена (то есть скопирована в вызывающую функцию) для сокращения размера стека.
- Прогнозирование виртуальных вызовов и вложенность: если вызов виртуальной функции часто приходится на конкретную функцию, то PGO может вставить условно выполняемый прямой вызов этой функции. Это позволяет вложить непосредственный вызов.
- Оптимизация распределения регистров: на основании результатов профилирования PGO оптимизирует распределение регистров.
- Оптимизация базовых блоков: этот прием оптимизации позволяет разместить часто выполняемые базовые блоки (которые временной выполняются в заданных границах) в одной локальности или наборе страниц. Таким образом сводится к минимуму количество используемых страниц, что минимизирует лишние затраты памяти.
- Оптимизация активных участков: функции, на выполнение которых программа тратит большую часть времени, могут оптимизироваться для ускорения.
- Оптимизация размещения функции: после того, как PGO проанализирует граф вызовов, функции, которые обычно располагаются на одном пути выполнения, перемещаются в одну секцию скомпилированного приложения.
- Оптимизация условных ветвлений: PGO может проанализировать ветви принятия решений (такие как `if...else if` или `switch`) и выявить самый частый используемый путь. Например, если команда `switch` содержит 10 вариантов (путей) и один из них используется в 95% случаев, то он перемещается в начало, чтобы выполняться раньше других в кодовом пути.
- Отделение мертвых зон: код, не вызываемый в процессе PGO, перемещается в отдельную секцию приложения.

Посмотреть конфигурацию сборки можно так
```bash
$ python -m sysconfig
```

Например, чтобы узнать платформу, следует выполнить
```bash
$ python -m sysconfig | grep -iE platform  # Platform: "macosx-10.9-x86_64"
```

Возможность выполнения модулей как скриптов была изначательно предложена в [PEP 338](https://peps.python.org/pep-0338/), а стандарт явного относительного импортирования был определен в [PEP 366](https://peps.python.org/pep-0366/).

Флаг `-m` означает, что внутри пакета модуля должно быть выполнено все, что находится внутри точки входа `__main__`. 

Если в Python вы захотите получить атрибут для имеющегося объекта, следует вызвать функцию `getattr()`. В C API этому вызову соответствует функция `PyObject_GetAttrString()`, находящаяся в файле `Objects > object.c`

Функция `run_pyc_file()` из файла `Python > pythonru.c` осуществляет _маршалинг_ объектного кода из файла `*.pyc` с использованием дескриптора файла.

_Маршалинг_ -- копирование содержимого файла в память и преобразование его в конкретную структуру данных.

Структура данных объектного кода на диске используется компилятором CPython для кэширования скомпилированного кода, чтобы его не приходилось заново парсить при каждом вызове скрипта.

