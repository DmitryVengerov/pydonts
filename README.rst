pydonts
=======

.. _general:

Общие рекомендации
------------------

- Следуй `PEP-8`_. Для автоматической проверки и исправления некоторых
  ошибок можно использовать утилиты `pep8`_ и `autopep8`_. К сожалению,
  они проверяют не всё (например, конвенции именования не проверяются).
  В качестве альтернативы можно использовать статический анализатор
  `flake8`_, который помимо стиля проверит наличие некоторых простых
  ошибок. Плагин для него, проверяющий именование, называется
  `pep8-naming`_.
- Используй операторы ``is`` и ``is not`` **только** для сравнение с
  синглтонами, например, ``None``. Исключение: булевые значения
  ``True`` и ``False``.
- Помни и применяй *falsy*/*truthy* семантику. *Falsy* значения ---
  ``None``, ``False``, нули ``0``, ``0.0``, ``0j``, пустые строки и
  байты и все пустые коллекции. Все остальные значения *truthy*.

  .. code:: python

     # Плохо
     if acc == []:
         # ...

     # Плохо
     if len(acc) > 0:
         # ...

     # Лучше
     if not acc:
         # ...

     # Допустимо
     if acc == 0:
         # ...

- Не копируй без необходимости.

  .. code:: python

     # Плохо
     set([x**2 for x in range(42)])

     for x in list(sorted(xs)):
         # ...

     # Лучше
     {x**2 for x in range(42)}

     for x in sorted(xs):
         # ...

- Не используй ``dict.get`` и коллекцию ``dict.keys`` для проверки
  наличия ключа в словаре:

  .. code:: python

     # Плохо
     if key in g.keys():
         # ...

     if not g.get(key, False):
         # ...

     # Лучше
     if key in g:
         # ...

     if key not in g:
         # ...

- Используй литералы для создания пустых коллекций. Исключение:
  ``set``, литералов пустого множества в Python нет.

  .. code:: python

     # Плохо
     dict(), list(), tuple()

     # Лучше
     {}, [], ()

.. _PEP-8: https://www.python.org/dev/peps/pep-0008
.. _pep8: https://pypi.python.org/pypi/pep8
.. _autopep8: https://pypi.python.org/pypi/autopep8
.. _flake8: https://pypi.python.org/pypi/flake8
.. _pep8-naming: https://pypi.python.org/pypi/pep8-naming


.. _structure:

Структура кода
--------------

- Не эмулируй оператор ``for``, Python --- это не Scala.

  .. code:: python

     # Плохо
     i = 0
     while i < n:
         ...
         i += 1

     # Лучше
     for i in range(n):
         ...

- Предпочитай итерацию по объекту циклам со счётчиком. Ошибка на 1 в
  индексе --- это классика. Если же индекс требуется, помни про
  ``enumerate``.

  .. code:: python

     # Плохо
     for i in range(len(xs)) :
         x = xs[i]

     # Лучше
     for x in xs:
         ...

     # Или
     for i, x in enumerate(xs):
         ...

- Не пиши бессмысленных операторов ``if`` и тернарных операторов.

  .. code:: python

     # Плохо
     if condition:
        return True
     else
        return False

     # Лучше
     return condition

  .. code:: python

     # Плохо
     if condition:
        return False
     else
        return True

     # Лучше
     return not condition

  .. code:: python

     # Плохо
     xs = [x for x in xs if predicate]
     return True if xs else False

     # Лучше
     xs = [x for x in xs if predicate]
     return bool(xs)

     # Ещё лучше
     return any(map(predicate, xs))


.. _functions:

Функции
-------

- Избегай изменяемых значений по умолчанию.
- Не злоупотребляй функциональными идиомами. Часто генератор списка,
  множества или словаря понятнее комбинации функций ``map``, ``filter``
  и ``zip``.

  .. code:: python

     # Плохо
     list(map(lambda x: x ** 2,
              filter(lambda x: x % 2 == 1,
                     range(10))))

     # Лучше
     [x ** 2 for x in range(10) if x % 2 == 1]

- Не злоупотребляй генераторами коллекций. Часто обычный цикл ``for``
  понятней вложенного генератора.
- Не сворачивай функции с эффектами. Первый аргумент
  ``functools.reduce`` не должен изменять состояние имён во внешних
  областях видимости или значение аккумулятора.

  .. code:: python

     # Плохо
     funtools.reduce(lambda acc, s: acc.update(s), sets,

     # Лучше
     acc = set()
     for set in sets:
         acc.update(set)

- Избегай бессмысленных анонимных функций.

  .. code:: python

     # Плохо
     map(lambda x: frobnicate(x), xs)

     # Лучше
     map(frobnicate, xs)

  .. code:: python

     # Плохо
     collections.defaultdict(lambda: [])

     # Лучше
     collections.defaultdict(list)


.. _decorators:

Декораторы
----------

- **Всегда** используй ``functools.wraps`` или
  ``functools.update_wrapper`` при написании декоратора.


.. _strings:

Строки
------

- Используй методы ``str.startswith`` и ``str.endswith``:

  .. code:: python

     # Плохо
     s[:len(p)] == p
     s.find(p) == len(s) - len(p)

     # Лучше
     s.startswith(p)
     s.endswith(p)