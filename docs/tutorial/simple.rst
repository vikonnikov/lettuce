.. _tutorial-simple:

########
Введение
########

Lettuce_ это очень простой и изящный инструмент для BDD_ (Behavior
Driven Development / Разработка, основанная на функционировании).
Он позволяет интерпретировать привычные для человека текстовые инструкции
по тестированию определенного функционала и затем выполнять их как автоматические
тесты для Python_ проектов, точно так же как Cucumber_ делает это для Ruby_.

Lettuce_ делает процесс разработки и тестирования по настоящему простым,
масштабируемым и понятным. Он позволяет человеку, не знакомому с внутренним устройством системы,
успешно тестировать её в процессе разработки.

.. image:: ./flow.png

*********
Установка
*********

Убедившись что у вас уже установлен Python, выполните в терминале команду:

.. highlight:: bash

::

   user@machine:~$ [sudo] pip install lettuce

*****************
Постановка задачи
*****************

Давайте поставим перед Lettuce_ задачу:
**Дано число, чему будет равен факториал?**

.. Note::
   Факториал положительного числа n, обозначаемый как n!, это
   результат перемножения всех пложодительных чисел, которые
   меньше или равны n. Факториал 0 равен 1.

*****************
Структура проекта
*****************

Создайте директории в соответствии с приведенной ниже структурой, файлы 
``zero.feature`` и ``steps.py`` пока оставьте пустыми.

.. highlight:: bash

::

    /home/user/projects/mymath
         | tests
               | features
                    - zero.feature
                    - steps.py

************
Посалатим :) 
************

Давайте опишем и решим поставленную задачу...

Первый этап
===========

[a] Описываем поведение
-----------------------

Начнем описывать ожидамое поведение программы вычисления факторила.
Описание помещаем в файл ``zero.feature``. Поведение описывается на Английском,
позже будет описана процедура локализации и использования русского языка в тестовых сценариях.

.. highlight:: ruby

::

    Feature: Compute factorial
        In order to play with Lettuce
        As beginners
        We'll implement factorial

        Scenario: Factorial of 0
            Given I have the number 0
            When I compute its factorial
            Then I see the number 1

.. Внимание::

	Файл ``zero.feature`` должен существовать, располагаться в директории ``features``
	и иметь расширение ``.feature``. Имя файла может быть любым.

[b] Опишем шаги на Python-е
---------------------------

Для того чтобы Lettuce смог интерпретировать инструкции из нашего тестового сценария,
нам необходимо описать каждый шаг. Создайте файл ``steps.py``, который будет содержать
Python-код, описывающий шаги тестовго сценария.

Python:

.. highlight:: python

::

   from lettuce import *

   @step('I have the number (\d+)')
   def have_the_number(step, number):
       world.number = int(number)

   @step('I compute its factorial')
   def compute_its_factorial(step):
       world.number = factorial(world.number)

   @step('I see the number (\d+)')
   def check_number(step, expected):
       expected = int(expected)
       assert world.number == expected, \
           "Got %d" % world.number

   def factorial(number):
       return -1

.. Внимание::

   Файл ``steps.py`` должен располагаться в директории ``features``, но при этом
   не обязательно называть его ``steps.py``, главное не забыть про расширение ``*.py``
   Lettuce_ рекурсивно просматривает все питоновские файлы, лежащие в директории ``features``.

Вообще функция вычисления факториала может быть описана где угодно,
но, поскольку это первый пример, то для того чтобы лучше понять как работает Lettuce_,
мы реализуем эту функцию внутри модуля ``steps.py``.

**Обратите внимание, что мы еще не реализовали функцию вычисления факториала (пока что она просто возвращает -1).**

[c] Запуcтим и увидим упавший тест
----------------------------------

Перейдите в директорию с тестами и в терминале выполните следующую команду:

.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce

Ничего удивительного в том что наш тест упал нет, мы ведь еще не описали функцию
вычисления факториала и возвращаемое значение -1 не соответствует ожидаемому. 

.. image:: ./screenshot1.png

Давайте попробуем исправить...

[d] Немного поправим код
------------------------

Итак, по определению факториал 0 равен 1.
В нашем тесте другие значения пока не задаются.
Пусть теперь функция факториала возвращает 1.  

.. highlight:: python

::

    from lettuce import *

    @step('I have the number (\d+)')
    def have_the_number(step, number):
        world.number = int(number)

    @step('I compute its factorial')
    def compute_its_factorial(step):
        world.number = factorial(world.number)

    @step('I see the number (\d+)')
    def check_number(step, expected):
        expected = int(expected)
        assert world.number == expected, \
            "Got %d" % world.number

    def factorial(number):
        return 1

[e] Запуcтим и увидим что тест прошел
-------------------------------------

Снова выполним в терминале команду:

.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce


Как мы можем увидеть теперь реализация функции вычисления факториала ведет себя так как мы и ожидали:

.. image:: ./screenshot2.png

Великолепно! :)

Однако функция вычисления факториала работает для одного значения и вычисляет
только факториал 0, для решения поставленной задачи этого не достаточно.
Давайте "посалатим" снова.

Второй этап
===========

Для более точной реализации задачи вычисления факториала, нам необходимо
на необходимо протестировать функцию на большем диапазоне чисел,
а следовательно нам необходимо больше тестов.

[a] Опишем поведение
--------------------

Давайте добавим еще 2 сценария для чисел 1 и 2:

.. highlight:: ruby

::

    Feature: Compute factorial
      In order to play with Lettuce
      As beginners
      We'll implement factorial

      Scenario: Factorial of 0
        Given I have the number 0
        When I compute its factorial
        Then I see the number 1

      Scenario: Factorial of 1
        Given I have the number 1
        When I compute its factorial
        Then I see the number 1

      Scenario: Factorial of 2
        Given I have the number 2
        When I compute its factorial
        Then I see the number 2

[b] Описание шагов на Python-е
------------------------------

Поскольку шаги у нас идентичные, в коде мы ничего менять не будем.

[c] Запуcтим и увидим упавший тест
----------------------------------


.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce

Когда мы запускали Lettuce мы конечно знали, что наша предыдущая реализация
функции вычисления факториала работает только для двух значений: для 0 и для 1.
При попытке вычислить факториал 2 тест парадет.


.. image:: ./screenshot3.png

[d] Исправим код
----------------

Давайте исправим код таким образом, чтобы факториал вычислялся
для всех значений, перечисленных в тестовых сценариях, особенно для числа 2:

.. highlight:: python

::

    from lettuce import *

    @step('I have the number (\d+)')
    def have_the_number(step, number):
        world.number = int(number)

    @step('I compute its factorial')
    def compute_its_factorial(step):
        world.number = factorial(world.number)

    @step('I see the number (\d+)')
    def check_number(step, expected):
        expected = int(expected)
        assert world.number == expected, \
            "Got %d" % world.number

    def factorial(number):
        number = int(number)
        if (number == 0) or (number == 1):
            return 1
        else:
            return number

[e] Запуcтим и увидим что тест прошел
-------------------------------------

.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce

.. image:: ./screenshot4.png

Замечательно! Три сценария описаны и выполняются так как нам нужно.

third round
===========

Let's provide more tests so our problem is better described and we get
new errors so we'll be able to solve them.

[a] describe behaviour
----------------------

.. highlight:: ruby

::

    Feature: Compute factorial
      In order to play with Lettuce
      As beginners
      We'll implement factorial

      Scenario: Factorial of 0
        Given I have the number 0
        When I compute its factorial
        Then I see the number 1

      Scenario: Factorial of 1
        Given I have the number 1
        When I compute its factorial
        Then I see the number 1

      Scenario: Factorial of 2
        Given I have the number 2
        When I compute its factorial
        Then I see the number 2

      Scenario: Factorial of 3
        Given I have the number 3
        When I compute its factorial
        Then I see the number 6

      Scenario: Factorial of 4
        Given I have the number 4
        When I compute its factorial
        Then I see the number 24

[b] define steps in python
--------------------------

As we haven't changed the definition, no need to make changes on this
step.

[c] run and watch it fail
-------------------------

.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce

.. image:: ./screenshot5.png

[d] write code to make it pass
------------------------------

.. highlight:: python

::

    from lettuce import *

    @step('I have the number (\d+)')
    def have_the_number(step, number):
        world.number = int(number)

    @step('I compute its factorial')
    def compute_its_factorial(step):
        world.number = factorial(world.number)

    @step('I see the number (\d+)')
    def check_number(step, expected):
        expected = int(expected)
        assert world.number == expected, \
            "Got %d" % world.number

    def factorial(number):
        number = int(number)
        if (number == 0) or (number == 1):
            return 1
        else:
            return number*factorial(number-1)

[e] run again and watch it pass
-------------------------------

.. highlight:: bash

::

   user@machine:~/projects/mymath/tests$ lettuce

.. image:: ./screenshot6.png

forth round
===========

All steps should be repeated as long as you can keep doing them - the
quality of your software depends on these.

****************
Syntactic sugar
****************

Available for versions > 0.2.19

Steps sentence can now be given by function name or doc.
=========================================================

To take a step sentence from function name or doc,
just decorate it with "@step" without argument.

These two steps below, are identicals than the example above.

.. highlight:: python

::

    from lettuce import *

    @step
    def have_the_number(step, number):
        'I have the number (\d+)'
        world.number = int(number)

    @step
    def i_compute_its_factorial(step):
        world.number = factorial(world.number)



Steps can be grouped in class decorated with "@steps"
======================================================

.. highlight:: python

::

    from lettuce import world, steps

    @steps
    class FactorialSteps(object):
      """Methods in exclude or starting with _ will not be considered as step"""

      exclude = ['set_number', 'get_number']

      def __init__(self, environs):
        self.environs = environs

      def set_number(self, value):
        self.environs.number = int(value)

      def get_number(self):
        return self.environs.number

      def _assert_number_is(self, expected, msg="Got %d"):
          number = self.get_number()
          assert number == expected, msg % number

      def have_the_number(self, step, number):
        '''I have the number (\d+)'''
          self.set_number(number)

      def i_compute_its_factorial(self, step):
          number = self.get_number()
          self.set_number(factorial(number))

      def check_number(self, step, expected):
          '''I see the number (\d+)'''
          self._assert_number_is(int(expected))

    # Important!
    # Steps are added only when you instanciate the "@steps" decorated class
    # Internally decorator "@steps" build a closure with __init__

    FactorialSteps(world)

    def factorial(number):
        number = int(number)
        if (number == 0) or (number == 1):
            return 1
        else:
            return number*factorial(number-1)


Have a nice lettuce...! ;)
