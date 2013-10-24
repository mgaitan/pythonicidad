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

    Un lenguaje que no afecta tu manera de pensar sobre la programación,
    no merece ser aprendido.


    -- Alan Perlis, Epigrams on Programming

-----

:data-y: r1000

*python* afecta, para bien
--------------------------

Así se itera en muchos lenguajes

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

No es sólo un *huevo de pascua*

**Es la guía filosófica de la pythonicidad**

------

Por qué programar *pythónicamente*
-----------------------------------

- eficiencia y eficacia
- consistencia
- comunicación
- aprendizaje

------

PEP8
----

la **guía** de estilo de codificación.

.. epigraph::

    Programs must be written for people to read, and only incidentally for machines to execute.

    -- Abelson & Sussman, Structure and Interpretation of Computer Programs

------

Atenti...
----------

- *Although practicality beats purity.*

   - Priorizar el estilo preexistente.
   - 79 caracteres. Really?

- flake8 (pep8.py + pyflakes) FTW! (en el editor o como VCS hook)

------

Otras herramientas pythonistas
-------------------------------

- virtualenv / virtualenvwrapper
- sphinx
- readthedocs.org
- tu *testsrunner* favorito (nose + progressive ?)

-------

Para los exquisitos: imports
-----------------------------

* uno por linea al principio del archivo
* no usar ``from module import *``
* primero imports de ``stdlib``
* segundo paquetes de terceros
* tercero paquetes propios

-----

Algunos conceptos: Ducktyping
-----------------------------

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

-------

getter y setters
----------------

.. epigraph::


    Lo triste es que esta pobre gente trabajó mucho más de lo necesario, para producir mucho más código del necesario, que funciona mucho más lento que el código python idiomático equivalente.

    -- Phillip J. Eby en `Python no es Java <http://dirtsimple.org/2004/12/python-is-not-java.html>`_

No se hacen falta

.. code::

    x1 = p.get_x()      # buuh
    p.set_x(x1)

    x1 = p.x
    p.x = x1

-----

- Cuando de verdad hacen falta, se pueden definir con ``property``

.. code:: python

    @property
    def edad(self):
        return (date.today() - self.fecha_nacimiento).days / 365


--------

Condiciones


.. code:: python

    if x > 0 and x < 100:       # buuh
        ...

    if 0 < x < 100:
        ...

    if x == 0 or x == 2 or x == 4:
        ....

    if x in (0, 2, 4):


-----

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

Unir cadenas

.. code:: python

    names = ['x-ip', 'facundobatista', 'nessita', 'lipe_p']

    s = names[0]
    for name in names[1:]:
        s += ', ' + name

    s = ', '.join(names)


----

Packing/Unpacking

.. code:: python

    p = u'Martín', u'Gaitán', 31

    fname = p[0]        # buuhh
    lname = p[1]
    age = p[2]

    fname, lname, age = p

.. note::

    en python 3 el unpacking es mucho más poderoso


-----

Packing/Unpacking 2

.. code:: python

    def fibonacci(n):
        x, y = 0, 1
        for i in xrange(n):
            yield x             # btw, yield
            x, y = y, x + y


No muevas los datos innecesariamente

------

Construir diccionarios desde secuencias

.. code:: python

    names = ['raymond', 'rachel', 'matthew']
    colors = ['red', 'green', 'blue']

    d = dict(zip(names, colors))
    {'matthew': 'blue', 'rachel': 'green', 'raymond': 'red'}


---------

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


``collections`` tiene estructuras buenísimas

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

.. note::

    py3 tiene ContextDecorator que es una clase que funciona como
    decorador o administrador de contexto.

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

.. note::

   - no abusar de los oneliner
   - regla: una línea == una oración.

--------

Conjuntos
---------

Son muy útiles!

.. code:: python

    pythonistas_cordoba = ['gaucho', 'rafa', 'jairo', 'tin']
    incubadora = ['jairo', 'tin', 'tomas']


--------

Méotod


-----

Para discutir después...

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

últimos consejos
-----------------

- Conocé la **stdlib**.
- Leé código
- ``itertools.product(('lee', 'escribí'), ('blogs', 'documentación', 'pyar'))``
- Github no es sólo hosting git.

    - seguir el trabajo de grosos
    - trendings: que hay de nuevo viejo
    - comunicación en contexto.


---------

Muchas gracias