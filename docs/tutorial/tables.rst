.. _tutorial-tables:

#################################
Обработка данных с помощью таблиц
#################################

Давайте представим, что мы работаем над MVC приложением. Во время разработки
очередного теста вы попадаете в ситуацию, когда в базу данных необходимо
добавить несколько моделей, пр этом необходимо проверить несколько состояний
для данных моделей, проще говоря добавить пару записей в БД.

Lettuce имеет в своем арсенале оцень полезный инструемнт для обработки данных
внтури шагов - таблицы данных.

.. highlight:: ruby

::

   Feature: bill students alphabetically
     In order to bill students properly
     As a financial specialist
     I want to bill those which name starts with some letter

     Scenario: Bill students which name starts with "G"
       Given I have the following students in my database:
         | name     | monthly_due | billed |
         | Anton    | $ 500       | no     |
         | Jack     | $ 400       | no     |
         | Gabriel  | $ 300       | no     |
         | Gloria   | $ 442.65    | no     |
         | Ken      | $ 907.86    | no     |
         | Leonard  | $ 742.84    | no     |
       When I bill names starting with "G"
       Then I see those billed students:
         | name     | monthly_due | billed |
         | Gabriel  | $ 300       | no     |
         | Gloria   | $ 442.65    | no     |
       And those that weren't:
         | name     | monthly_due | billed |
         | Anton    | $ 500       | no     |
         | Jack     | $ 400       | no     |
         | Ken      | $ 907.86    | no     |
         | Leonard  | $ 742.84    | no     |

В приведенном выше примере 4 шага, в 3 шагах содержаться таблицы с тестовыми данными.

Давайте представим что мы используем Django_ и опишем шаги, использующие таблицы.

.. highlight:: python

::

      from lettuce import step
      from school.models import Student

      @step('I have the following students in my database:')
      def students_in_database(step):
          for student_dict in step.hashes:
              person = Student(**student_dict)
              person.save()

Как на счет удобных функций для получения первой и последней строк таблицы?

.. highlight:: python

::

      from lettuce import step
      from school.models import Student

      @step('I have the following students in my database:')
      def students_in_database(step):
          person1 = Student(**step.hashes.first)
          person2 = Student(**step.hashes.last)

          person1.save()
          person2.save()


Легко, пожалуйста!

Каждый шаг имеет свойство hashes, содержащее список словарей.
Каждый словарь явлеется записью из таблицы и содежит пары: заголовк столбца и значение.

Другими словами, Lettuce преобразует таблицу, описанную в первом шаге,
в список эквивалентных словарей.

::

      @step('I have the following students in my database:')
      def students_in_database(step):
          assert step.hashes == [
              {
                  'name': 'Anton',
                  'monthly_due': '$ 500',
                  'billed': 'no'
              },
              {
                  'name': 'Jack',
                  'monthly_due': '$ 400',
                  'billed': 'no'
              },
              {
                  'name': 'Gabriel',
                  'monthly_due': '$ 300',
                  'billed': 'no'
              },
              {
                  'name': 'Gloria',
                  'monthly_due': '$ 442.65',
                  'billed': 'no'
              },
              {
                  'name': 'Ken',
                  'monthly_due': '$ 907.86',
                  'billed': 'no'
              },
              {
                  'name': 'Leonard',
                  'monthly_due': '$ 742.84',
                  'billed': 'no'
              },
          ]

.. _Django: http://djangoproject.com/
