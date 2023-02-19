# Code Style

В данном документе будут предложены примеры кода, которые позволяют
понять как лучше писать код. Все приведенные ниже примеры - дополнению к стандарту PEP8.

Если твой выбор это **vscode**, то настоятельно рекомендую использовать [pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance) и в качестве линтера выбрать flake8.

плюс плагины:
> pip install flake8-{bugbear,builtins,commas,docstrings,fixme,pep3101,polyfill,comprehensions,bandit}

Также не забываем выставить проверку типизации - **basic**:

![image](https://user-images.githubusercontent.com/51759314/198858229-6735e76f-29b7-437e-a0e1-233f18ec37d0.png)

## Импорты


Любые импорты целых модулей пишутся в порядке длины имени модуля.

Пример:

- Хороший вариант
```python
import functools
import collections
import dataclasses
```

- Нежелательно
```python
import dataclasses
import functools
import collections
```

Любые импорты функций/классов из модулей нужно делать читабельными, а
также легкорасширяемыми.

Пример:

- Как делать не надо
```python
from some_module import func1, func2, SomeClassA, LongNameOfSomeClassB
```

- Хороший вариант
```python
from some_module import (
    func1, func2,
    SomeClassA, LongNameOfSomeClassB,
)
```

Недостатки хорошего варианта в том что если прийдется добавить еще одну
функцию, то git воспримет это как изменение строки, гораздо удобнее
выделять все на отдельные строки:

- Лучший вариант
```python
from some_module import (
    func1,
    func2,
    func3,
    SomeClassA,
    SomeClassB,
    SomeClassC,
)
```

Обратите внимание на то, что в заключительной строке стоит запятая -
если будет необходимость добавления через некоторое время новой
функции/класса из модуля, то шанс забыть поставить эту запятую снижается
до нуля.

Также важно понимать, что если вы импортируете из одного модуля все
определённые в нем функции, лучше импортируйте весь модуль:

- Как делать не надо

файл some_module.py
```python
def a(...):
    ...

def b(...):
    ...

def c(...):
    ...
```

импорт:
```python
from some_module import a, b, c
```

- Хороший вариант
импорт:
```python
import some_module

# Use case
res = some_module.a(...)
```

## Type Hints

Пример

- Как делать не надо

```python
def some_function(a, b, c):
    return a + b * c

some_function('a', 'b', 'c')  # runtime exception (TypeError)
```

- Хороший вариант

```python
def some_function(a: int, b: int, c: int) -> int:
    return a + b * c

some_function(10, 'c', 'd')  # raises an error on linting stage
```


### Сложные аннотации типов

Мы придерживаемся конвенции - не более 3х типов вложенных друг в друга:

- Как делать не надо

```python
SomeCustomType = \
    Tuple[int, Optional[Union[int, float]], Dict[str, Tuple[int, int]]]
```

- Хороший вариант

```python
Point = Tuple[int, int]
Number = Union[int, float]

MaybeNumber = Optional[Number]
PointsMapping = Dict[str, Point]

SomeCustomType = Tuple[int, MaybeNumber, PointsMapping]
```

В данном случае второй вариант позволяет понять типы глубже: мы уже не
видим сложных конструкций языка, а видим конкретные применения
абстракций, это позволяет упростить понимание кода.


------------------------------------------------------------------------

## Переносы строк

Приветствуется только один вариант переноса строк внутри функции
и в коде в целом:

```python
some_looooong_name = some_long_operation(
    ...,
    ...,
    ...,
)

# Second variant, only when 80 symbols barrier reached
some_loooong_name = (
    some_long_operator(
        ...,
        ...,
        ...,
    )
)
```

Никакие слеши, и другие ухищерения не используются, если не умещается импорт,
то можно сделать вот так:

```python
# Было
from apps.utils.package_a.package_b.package_c.package_d.package_e.package_f \
import f

# Стало
import apps.utils.package_a.package_b.package_c.package_d.package_e as pkg_e

f = pkg_e.package_f.f
```

## Переносы строк с бинарным оператором

Пишем согласно 

[W504](https://www.flake8rules.com/rules/W504.html), 
[PEP](https://peps.python.org/pep-0008/#should-a-line-break-before-or-after-a-binary-operator)

```python
# Anti-pattern
income = (gross_wages +
          taxable_interest)

# Best practice
income = (gross_wages
          + taxable_interest)
```

## Functions definition

Приветствуются 3 варинта определения любой функции:

- Для функций умещающихся в 80 символов
```python
def func_a(a, b, c):
    ...
```

- Для функций не умещающихся в 80 символов
```python
# Preferable way
def some_long_function_name(
    arg1: int,
    arg2: Tuple[Optional[int], Optional[str]],
) -> Optional[dict]:
    ...

# Second, but also accepted way
def some_long_function_name(
        arg1: int,
        arg2: Tuple[Optional[int], Optional[str]],
) -> Optional[dict]:
    ...
```

**Важное замечание**:

- Предпочтительнее вариант с одним отступом.

- Если функция перестала вмещаться в 80 символов, **ВСЕ** аргументы
  функции должны быть на новой строке

И не делайте вот так совсем **НИКОГДА**:
```python
def some_long_function_name(
        arg1: int,
        arg2: (
            Tuple[
                Optional[Tuple[int, int]],
                Optional[Tuple[Optional[int], Optional[str]]]
            ]
        ),
) -> Optional[dict]:
    ...
```

В такой ситуации лучше замените типы на алиасы типов, и используйте один
отступ а не два.

# Написание кода

## Сложные вложенные условные выражения

- Как делать не надо

```python
def some_func(a, b, c):
    if a:
        if b:
            if c:
                return a + b - c
            else:
                return -2
        else:
            return -1
    else:
        return -3
```

Такой код совсем не реально прочитать (если например условия будут
сложнее), давайте посмотрим на вариант получше

- Хороший вариант

```python
def some_func(a, b, c):
    if not a:
        return -3

    if not b:
        return -1

    if not c:
        return -2

    return a + b - c
```

Данный код гораздо проще прочесть, и понять его смысл.


## Разделение кода на блоки

Очень часто программисты в порыве пишут код строка за строкой не всегда
осознавая что этот код потом прийдется кому то читать.

**Запомним** - пишем код чтобы его можно было прочитать через полгода и
понять что происходит.

- Как делать не надо

```python
def spagetti_function(a, b, c, d, e, f, g, h, i):
    with open('lalal') as f:
        f.write(a)
    os.remove(b)
    os.rename(c, d)
    os.kill(e)
    print(f % g)
    SomeUsefulClass(i).run_some_code()
```

Рзабивайте такие функци на несколько делающих что то одно, это позволит
упростить unit тестирование вашего кода, и упростит читабельность

Так же обязательное разбивайе функцию на логические блоки.

- Хороший вариант

```python
def write_to_file(a, filename):
    with open(filename or 'lalal') as f:
        f.write(a)

def remove_file(b):
    try:
        os.remove(b)

    except Exception as err:
        log.error(err)

def rename_file(file_name_old, file_name_new):
    try:
        os.rename(file_name_old, file_name_new)

    except Exception as err:
        log.error(err)


def kill_process(pid):
    try:
        os.kill(pid)

    except Exception as err:
        log.error(err)

def print_some_class_data_and_run_it(f, g, i):
    print(f % g)

    SomeUsefulClass(i).run_some_code()


# And now we may use that code in one function wich will be simply
# tested by mocking functions used inside

def run_it(a, filename, b, file_old, file_new, pid, f, g, i):
    write_to_file(a, filename)
    remove_file(b)
    rename_file(file_old, file_new)
    kill_process(pid)
    print_some_class_data_and_run_it(f, g, i)
```


Теперь про разбитие кода на блоки:

- Как делать не надо
```python
def some_func(a, b, c):
    if not a:
        return -3
    if not b:
        return -1
    if not c:
        return -2
    return a + b - c
```

- Хороший вариант

```python
def some_func(a, b, c):
    if not a:
        return -3

    if not b:
        return -1

    if not c:
        return -2

    return a + b - c
```

Лишние пустые строки ничего не меняют, но повышают читабельность кода в
разы. Помните: код читают в 10 раз чаще, чем пишут.


## Pythonic way

Часто можно встретить код такого вида:

```python
a = list(set(dict(some_func(some_other_func()))))
```

Разбивайте на блоки, рефакторите, но не пишите код, который сами через
неделю захотите переписать от того что не поймете.

- Как не надо делать

```python
def find_first(
        iterable: Iterable[Item],
        condition: Callable[[Item], bool],
        default: ReturnValue,
) -> Union[ReturnValue, Item]:
    found = next(
        (
            obj
            for obj in iterable
            if condition(obj)
        ),
        default,
    )
    return found

```

Здесь код, написан неявным образом, он работает, но не позволяет с
одного взгляда понять что в нем происходит.

- Хороший вариант

```python
def find_first(iterable, condition, default):
    for item in iterable:
        if condition(item):
            return item

    return default
```

В данном коде, сразу ясно, что он находит первый элемент для которого
condition вернет True либо возвращает default значение.

Отлично, ты дочитал наш гайд по Codestyle. Поздравляю! 
