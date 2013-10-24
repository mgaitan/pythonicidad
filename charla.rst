:title: La pythonicidad al palo
:author: Martín Gaitán
:evento: PyCon Argentina 2013
:css: charla.css

--------

La pythonicidad al palo

.. image:: http://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Baltimore_Aquarium_-_Morelia_viridis.jpg/764px-Baltimore_Aquarium_-_Morelia_viridis.jpg

--------

No da igual cualquier lenguaje
-------------------------------

.. epigraph::

    A language that doesn't affect the way you think about programming, is not worth knowing

    -- Alan Perlis, Epigrams on Programming

-----

Que los afecte *pythonicamente*
--------------------------------

Esto

.. code:: c

    for (i=0; i < mylist_length; i++) {
       do_something(mylist[i]);
    }

-----

Buuuh!

.. code:: python

    i = 0
    while i < len(mylist):
       do_something(mylist[i])
       i += 1

------

Ok, dale que va queriendo

.. code:: python

    for i in range(len(mylist)):
       do_something(mylist[i])

------

Al palo

.. code:: python

    for element in mylist:
       do_something(element)


.. epigraph:: Simple is better than complex. Beautiful is better than ugly.

   -- Tim Peters, The Zen of Python


--------

El Zen
-------

.. code:: python

    >>> import this

- No es sólo un huevo de pascua
- Es una guia filosófica de la pythonicidad

------

Por qué programar *pythónicamente*
-----------------------------------

- mantenible
- eficiencia y eficacia
- consistencia
- comunicación

------

PEP8
----

Guía de estilo de codificación.

.. epigraph::

    Programs must be written for people to read, and only incidentally for machines to execute.

    -- Abelson & Sussman, Structure and Interpretation of Computer Programs

------

Pero...
--------

- Special cases aren't special enough to break the rules.
  (Although practicality beats purity.)

  - Ejemplo: 79 caracteres. Really?

- flake8 FTW! (en el editor o como VCS hook)

------

Imports
-------

* uno por linea al principio del archivo
* no usar ``from module import *``
* primero imports de stdlib
* segundo paquetes de terceros
* tercero paquetes propios
* ordenados por largo

-----

Ducktyping
----------

*Es más fácil pedir perdón que pedir permiso*

.. code:: python

    def f(animal):
        if isinstance(animal, Duck):
            animal.quack()
        else:
            print("%s can't quack" % animal)

    def f(animal):
        try:
            animal.quack()
        except (AttributeError, TypeError):
            print("%s can't quack" % animal)

- Los tipos de excepciones deben se explícitos
  (*Errors should never pass silently.*)

--------

Expresiones condicionales (operador ternario)

.. code:: python

    if condition:
        a = x
    else:
        a = y

    a = x if condition else y

------


Algunos *refactors*
--------------------

------


Unir cadenas

.. code:: python

    names = ['x-ip', 'facundobatista', 'nessita', 'tin_nqn']

    s = names[0]
    for name in names[1:]:
        s += ', ' + name
    print s

    print ', '.join(names)


----

Packing/Unpacking

.. code:: python

    p = u'Martín', u'Gaitán', 31

    fname = p[0]
    lname = p[1]
    age = p[2]

    fname, lname, age = p


-----

Packing/Unpacking 2

.. code:: python

    def fibonacci(n):
        x, y = 0, 1
        for i in xrange(n):
            yield x
            x, y = y, x + y


No muevas los datos innecesariamente

------

Construir diccionarios desde secuencias


.. code:: python

    names = ['raymond', 'rachel', 'matthew']
    colors = ['red', 'green', 'blue']
    for


    d = dict(zip(names, colors))
    {'matthew': 'blue', 'rachel': 'green', 'raymond': 'red'}


----

Evitá las *banderas*

.. code:: python

    def find(seq, target):
        found = False
        for i, value in enumerate(seq):
            if value == tgt:
                found = True
                break
        if not found:
            return -1
        else:
            return i

    def find(seq, target):
        for i, value in enumerate(seq):
            if value == tgt:
                return i
        return -1

---------

Llamar función hasta un resultado sentinela

.. code:: python

    blocks = []
    while True:
        block = f.read(32)
        if block == '':
            break
        blocks.append(block)

    blocks = []
    for block in iter(partial(f.read, 32), ''):
        blocks.append(block)

--------

La legibilidad cuenta: usá los kwargs

.. code:: python

    twitter_search('#PyconAr', False, 20, True)

    twitter_search('#PyconAr', retweets=False, numtweets=20,
        popular=True)

--------

La legibilidad cuenta: namedtuple


.. code:: python

    >>> doctest.testmod()
        (0, 4)

        from collections import namedtuple
        TestResults = namedtuple('TestResults',
            ['failed', 'attempted'])

    >>> doctest.testmod()
        TestResults(failed=0, attempted=4)


``collections`` está buenísimo

---------

Decoradores: factorizá lo administrativo


.. code:: python

    def web_lookup(url, saved={}):
        if url in saved:
            return saved[url]
        page = urllib.urlopen(url).read()
        saved[url] = page
        return page

    @cache
    def web_lookup(url):
        return urllib.urlopen(url).read()

--------

Contextos: sentencia ``with``

- Patrón: ``pre()  X()  post()``
- Fáciles con ``contextlib.contextmanager``

.. code:: python

    @contextmanager
    def tag(name):
        print("<%s>" % name)
        yield
        print("</%s>" % name)

    >>> with tag("h1"):
    ...    print("foo")



---------

Bucles anidados

.. code:: python

    combs = []
    for a in x:
        for b in y:
            for c in z:
                combs.append((a, b, c))

    combs = itertools.product(x, y, z)

``itertools`` es groso!

----------

Listas por comprehensión / Expresiones generadoras

.. code:: python

    result = []
    for i in range(10):
        if i % 2 == 0:
        s = i ** 2
        result.append(s)
    sum(result)

    sum([i**2 for i in xrange(10) if i % 2 == 0])

    sum(i**2 for i in xrange(10) if i % 2 == 0)

.. no abusar de los oneliner
.. regla: una línea == una oración.

---------

Ultimos consejos
-----------------

- Conocé la stdlib.
- Lee código
- ``itertools.product(('lee', 'escribi'), ('blogs', 'documentacion'))``
- Github no es sólo hosting git.

    - seguir el trabajo de grosos
    - trendings: que hay de nuevo viejo
    - comunicación en contexto.


---------

Muchas gracias