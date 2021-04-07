#SdamGIA Api

**SdamGIA Api** – Python модуль для взаимодействия с образовательным порталом СДАМ ГИА

## Структура СдамГИА
Чтобы было проще понять, как устроена база заданий СдамГИА, предлагаю воспользоваться следующей схемой:
```
СдамГИА
└── Предмет (subject)
    ├── Каталог заданий (catalog)
    │   └── Задание (topic)
    │       └── Категория (category)
    │           └── Задача (problem)
    └── Тест (test)
        └── Задача (problem)       
```
У каждой задачи, категории или теста есть свой идентификатор. 
Задания тоже имеют номера, которые в свою очередь могут иметь такие значения как "Д1" или "C4". Этим они отличаются от идентификаторов.

## Установка

    $ pip3 install sdamgia-api

## Использование

### Инициализация
```python
from sdamgia import SdamGIA

sdamgia = SdamGIA()
```

### Поиск задачи по ее идентификатору
```python
subject = 'math'
id = '1001'
sdamgia.get_problem_by_id(subject, id)
```
```shell
{
  'id': '1001',
  'topic': '4',
  'condition': {
    'text': 'На экзамен вынесено 60 вопросов, Андрей не выучил 3 из них. Найдите вероятность того, что ему попадется выученный вопрос.', 
    'images': []
  }, 
  'solution': {
    'text': 'Решение.Андрей выучил 60\xa0–\xa03\xa0=\xa057 вопросов. Поэтому вероятность того, что на экзамене ему попадется выученный вопрос равна\xa0Ответ: 0,95.',
    'images': ['https://ege.sdamgia.ru/formula/svg/9f/9fbf55ab44a507fb47ba8a2666cd7644.svg']
  }, 
  'answer': '0,95', 
  'analogs': ['1001', '1002', '1003', '1004', '1005', '1006', '1007', '1008', '1009', '1010'], 
  'url': 'https://math-ege.sdamgia.ru/problem?id=1001'
}
```


### Поиск задач по запросу
```python
subject = 'math'
request = 'Найдите количество'
sdamgia.search(subject, request)
```
```shell
['6407', '8795', '8799', '27501', '519508', '519534', '525371', '512436', '6401', '6421', '6427', '7321', '7325', '7801', '7803', '7807', '7809', '8037', '8039', '8045']
```

### Поиск теста по его идентификатору
```python
subject = 'math'
id = '1770'
sdamgia.get_test_by_id(subject, id)
# Возвращает список задач, входящих в тест
```
```shell
['77345', '28765', '77374', '27903', '26675', '27700', '77411', '27506', '27132', '28008', '26703', '99592']
```

### Поиск категории по ее идентификатору
```python
subject = 'math'
id = '1'
sdamgia.get_category_by_id(subject, id)
# Возвращает список задач, входящих в категорию
```
```shell
['77334', '323512', '501201', '509077', '509106']
```

### Получение каталога
```python
subject = 'math'
sdamgia.get_catalog(subject)
```
```shell
[
  {
    'topic_id': '1',
    'topic_name': 'Простейшие текстовые задачи', 
    'categories': [
      {'category_id': '174', 'category_name': 'Вычисления'}, 
      {'category_id': '1', 'category_name': 'Округление с недостатком'}, 
      {'category_id': '2', 'category_name': 'Округление с избытком'},
      {'category_id': '249', 'category_name': 'Проценты'},
      {'category_id': '5', 'category_name': 'Проценты и округление'}
    ]
  },
  {
    ...
  }        
]
```

### Генерация теста
По умолчанию генерируется тест, включающий по одной задаче из каждого задания предмета. <br>
Так же можно вручную указать одинаковое количество задач для каждого из заданий: {'full': <кол-во задач>} <br>
Указать определенные задания с определенным количеством задач для каждого: {<номер задания>: <кол-во задач>, ... }
```python
subject = 'math'
problems = {1: 1, 2: 2, 3: 4}
sdamgia.generate_test(subject, problems)
# Возвращает идентификатор сгенерированного теста
```
```shell
38299510
```
Обратите внимание, что в этом случае идентификатор задания - только науральное число. Т.е. если после задания 15 идет задание Д1, оно должно будет записываться как 16 задание.

### Генерация pdf-версии теста
```python
sdamgia.generate_pdf('math', '38299510', pdf='h')
```
```shell
https://math-ege.sdamgia.ru/pdf/1fe7d7d8408f8d5195fabfd8ab393d63.pdf
```
Список параметров:
```
subject: Наименование предмета
testid: Идентифигатор теста
solution: Пояснение
nums: № заданий
answers: Ответы
key: Ключ
crit: Критерии
instruction: Инструкция
col: Нижний колонтитул
pdf: Версия генерируемого pdf документа
    По умолчанию генерируется стандартная вертикальная версия
    h - горизонтальная версия
    z - версия с крупным шрифтом
    m - версия с большим полем
```