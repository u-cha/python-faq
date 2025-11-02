---
title: Лайвкодинг Python
description: Вопросы и ответы по лайвкодингу Python на сайте-методичке Python FAQ.
---

### 1. Напишите функцию, которая возвращает n-ное число Фибоначчи
<details>
  <summary>Задача</summary>

```text 
    def fibonacci(n):
     """
     (Последовательность Фибоначчи: 0, 1, 1, 2, 3, 5, 8, 13, ...)
     """
        # Ваше решение здесь
        pass
    
    # Тесты
    print(fibonacci(0))  # 0
    print(fibonacci(1))  # 1
    print(fibonacci(5))  # 5
    print(fibonacci(7))  # 13
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text 
1. Последовательность Фибоначчи - каждое следующее число = сумма двух предыдущих
2. Есть несколько способов решения:
    - с помощью рекурсии
    - с помощью цикла for
```
</details>

<details>
  <summary>Решение</summary>

1. Рекурсивное решение (неэффективное)
```python 
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

2. Итеративное решение (оптимальное)
```python 

def fibonacci(n):
    if n <= 1:
        return n
    
    a, b = 0, 1
    for i in range(2, n + 1):
        a, b = b, a + b
    return b
```
3. Генератор чисел Фибоначчи
```python 

def fibonacci_generator(limit):
    a, b = 0, 1
    while a <= limit:
        yield a
        a, b = b, a + b

# Использование
for num in fibonacci_generator(20):
    print(num)  # 0, 1, 1, 2, 3, 5, 8, 13
```
</details>

---

### 2. Напишите функцию, которая возвращает следующий день, относительно переданной даты
<details>
  <summary>Задача</summary>

```text 
    def get_next_day(date_string):
        """
        Args:
           date_string (str): Дата в формате 'YYYY-MM-DD'
    
        Returns:
           str: Следующий день в формате 'YYYY-MM-DD'
        """

        # Ваше решение здесь
        pass

    # Тесты
    print(get_next_day('2024-01-31'))  # 2024-02-01
    print(get_next_day('2024-02-28'))  # 2024-02-29
    print(get_next_day('2023-02-28'))  # 2023-03-01
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text 
1. Применить datetime, strptime, strftime
```
</details>

<details>
  <summary>Решение</summary>

```python 
from datetime import datetime, timedelta

def get_next_day(date_string):
    """
    Возвращает следующий день относительно переданной даты.
    
    Args:
        date_string (str): Дата в формате 'YYYY-MM-DD'
    
    Returns:
        str: Следующий день в формате 'YYYY-MM-DD'
    """
    current_date = datetime.strptime(date_string, '%Y-%m-%d')
    next_date = current_date + timedelta(days=1)
    return next_date.strftime('%Y-%m-%d')
```
</details>

---

### 3. Написать генератор для пагинации по списку
<details>
  <summary>Задача</summary>

```text 
    def paginate(items: list, page_size:int):
        """
        Генератор для пагинации по списку.
    
        Args:
        items: список элементов
        page_size: размер страницы
    
        Yields:
            списки элементов размером page_size
        """

        # Ваше решение здесь
        pass

    # Тесты
    items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    paginator = paginate(items, 3)
    assert next(paginator) == [1, 2, 3]
    assert next(paginator) == [4, 5, 6]
    assert next(paginator) == [7, 8, 9]
    assert next(paginator) == [10]
    
    # Проверяем StopIteration
    try:
        next(paginator)
        assert False, "Ожидалось StopIteration"
    except StopIteration:
        pass  # Ожидаемое поведение

    for page in paginator:
        print(page)  # [1, 2, 3], [4, 5, 6], [7, 8, 9], [10]
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text 
1. Нужно разбить список на части по page_size элементов
2. Используйте срезы списка: items[start:end]
3. Шаг между страницами = page_size
```
</details>

<details>
  <summary>Решение</summary>

Вариант 1: Классический с range (самый популярный)

```python 

def paginate(items, page_size):
    for i in range(0, len(items), page_size):
        yield items[i:i + page_size]
```
Вариант 2: С использованием while цикла

```python 
def paginate(items, page_size):
    i = 0
    while i < len(items):
        yield items[i:i + page_size]
        i += page_size
```
Вариант 3: С итератором и islice

```python 

from itertools import islice

def paginate(items, page_size):
    iterator = iter(items)
    while True:
        chunk = list(islice(iterator, page_size))
        if not chunk:
            return
        yield chunk
```
Вариант 4: Рекурсивный подход

```python 

def paginate(items, page_size):
    if not items:
        return
    yield items[:page_size]
    yield from paginate(items[page_size:], page_size)
```
Вариант 5: С использованием enumerate

```python 

def paginate(items, page_size):
    for i, item in enumerate(items):
        if i % page_size == 0:
            yield items[i:i + page_size]
```
Вариант 6: С группировкой по индексу

```python 

def paginate(items, page_size):
    return (items[i:i + page_size] for i in range(0, len(items), page_size))
```
Вариант 7: С проверкой границ

```python 

def paginate(items, page_size):
    if page_size <= 0:
        raise ValueError("page_size должен быть положительным")
    
    page = []
    for item in items:
        page.append(item)
        if len(page) == page_size:
            yield page
            page = []
    
    if page:  # Последняя неполная страница
        yield page
```        
</details>

---

### 4. Написать генератор, который сделает из вложенного словаря плоский
<details>
  <summary>Задача</summary>

```text 
    def flatten_dict(nested_dict: dict, parent_key: str = '', separator: str = '.'):
        """
        Генератор, который преобразует вложенный словарь в плоский.
        
        Args:
            nested_dict: вложенный словарь
            parent_key: ключ родительского элемента (для рекурсии)
            separator: разделитель между ключами
            
        Yields:
            кортежи (ключ, значение) для каждого элемента плоского словаря
        """

        # Ваше решение здесь
        pass

    # Тесты
    nested = {
        'a': 1,
        'b': {
            'c': 2,
            'd': {
                'e': 3
            }
        },
        'f': 4
    }
    
    # Преобразуем в словарь для проверки
    result = dict(flatten_dict(nested))
    expected = {
        'a': 1,
        'b.c': 2,
        'b.d.e': 3,
        'f': 4
    }
    assert result == expected
    
    # Проверяем работу как генератора
    flat_gen = flatten_dict(nested)
    assert next(flat_gen) == ('a', 1)
    assert next(flat_gen) == ('b.c', 2)
    
    # Проверяем StopIteration
    try:
        next(flat_gen)
        assert False, "Ожидалось StopIteration"
    except StopIteration:
        pass
```        
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text 
1. Нужно обойти все ключи словаря рекурсивно
2. Если значение является словарем - углубляемся в него
3. Формируем составной ключ через разделитель
4. Используйте yield для возврата пар (ключ, значение)
```
</details>

<details>
  <summary>Решение</summary>

Вариант 1: Рекурсивный с yield from

```python
def flatten_dict(nested_dict: dict, parent_key: str = '', separator: str = '.'):
    for key, value in nested_dict.items():
        new_key = f"{parent_key}{separator}{key}" if parent_key else key
        
        if isinstance(value, dict):
            yield from flatten_dict(value, new_key, separator)
        else:
            yield (new_key, value)
```
Вариант 2: С явным yield для каждого случая

```python
def flatten_dict(nested_dict: dict, parent_key: str = '', separator: str = '.'):
    for key, value in nested_dict.items():
        new_key = f"{parent_key}{separator}{key}" if parent_key else key
        
        if isinstance(value, dict):
            for flattened in flatten_dict(value, new_key, separator):
                yield flattened
        else:
            yield (new_key, value)
```
Вариант 3: С обработкой списков и других типов

```python
def flatten_dict(nested_dict: dict, parent_key: str = '', separator: str = '.'):
    for key, value in nested_dict.items():
        new_key = f"{parent_key}{separator}{key}" if parent_key else key
        
        if isinstance(value, dict):
            yield from flatten_dict(value, new_key, separator)
        elif isinstance(value, (list, tuple)):
            for i, item in enumerate(value):
                list_key = f"{new_key}[{i}]"
                if isinstance(item, dict):
                    yield from flatten_dict(item, list_key, separator)
                else:
                    yield (list_key, item)
        else:
            yield (new_key, value)
```
Вариант 4: Итеративный с использованием стека

```python
def flatten_dict(nested_dict: dict, separator: str = '.'):
    stack = [(nested_dict, '')]
    
    while stack:
        current_dict, current_key = stack.pop()
        
        for key, value in current_dict.items():
            new_key = f"{current_key}{separator}{key}" if current_key else key
            
            if isinstance(value, dict):
                stack.append((value, new_key))
            else:
                yield (new_key, value)
```
</details>

### 5. Реализовать класс библиотеки

<details>
  <summary>Задача</summary>

```text 
    class Library:
        """
        Простой класс для управления библиотекой книг.
        """
        
        def __init__(self):
            """Инициализация пустой библиотеки."""
            self.books = []
        
        def add_book(self, title: str, author: str, year: int):
            """
            Добавляет книгу в библиотеку.
            
            Args:
                title: название книги
                author: автор книги
                year: год издания
            """
            # Ваше решение здесь
            pass
        
        def find_by_author(self, author: str) -> list:
            """
            Находит книги по автору.
            
            Args:
                author: автор для поиска
                
            Returns:
                список найденных книг
            """
            # Ваше решение здесь
            pass
        
        def get_all_books(self) -> list:
            """
            Возвращает все книги.
            
            Returns:
                список всех книг
            """
            # Ваше решение здесь
            pass

    # Тесты
    library = Library()
    
    # Добавление книг
    library.add_book("Война и мир", "Лев Толстой", 1869)
    library.add_book("Анна Каренина", "Лев Толстой", 1877)
    library.add_book("Преступление и наказание", "Федор Достоевский", 1866)
    
    # Поиск по автору
    tolstoy_books = library.find_by_author("Лев Толстой")
    assert len(tolstoy_books) == 2
    
    # Получение всех книг
    all_books = library.get_all_books()
    assert len(all_books) == 3
    
    print("Все тесты пройдены!")
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text 
1. Используйте список для хранения книг
2. Каждая книга - словарь с полями title, author, year
3. Для поиска используйте фильтрацию списка
```
</details>

<details>
  <summary>Решение</summary>

```python
class Library:
    def __init__(self):
        self.books = []
    
    def add_book(self, title: str, author: str, year: int):
        book = {
            'title': title,
            'author': author, 
            'year': year
        }
        self.books.append(book)
    
    def find_by_author(self, author: str) -> list:
        return [book for book in self.books if book['author'] == author]
    
    def get_all_books(self) -> list:
        return self.books.copy()
```
</details>

### 6. Задача (с подвохом). Что выведет этот код?

<details>
  <summary>Задача</summary>

```python
def extend_list(val, lst=[]):
    lst.append(val)
    return lst

# Первый вызов: используем список по умолчанию
list1 = extend_list(10)

# Второй вызов: передаём новый пустой список
list2 = extend_list(123, [])

# Третий вызов: снова используем список по умолчанию
list3 = extend_list('a')

print('list1 =', list1)
print('list2 =', list2)
print('list3 =', list3)
```
</details>

<details>
  <summary>Решение</summary>

Значения по умолчанию для аргументов функции вычисляются один раз в момент определения функции, а не при каждом вызове. Поэтому список lst=[] создается единожды и переиспользуется во всех вызовах, где lst не передается явно.

```python
Первый вызов:
list1 = extend_list(10)

# lst не передан → используется дефолтный список []
# Добавляем 10 → список становится [10]
# list1 = [10]
```

```python
Второй вызов:
list2 = extend_list(123, [])

# Передаем новый пустой список `[]` 
# Добавляем `123` → список становится `[123]`
# list2 = [123]
```

```python
Третий вызов:
#  = extend_list('a')

# lst не передан → используется тот же дефолтный список, что и в первом вызове (уже `[10]`)
# Добавляем `'a'` → список становится `[10, 'a']`
# list3 = [10, 'a']**
```

```python
# Итоговый вывод:
list1 = [10, 'a']
list2 = [123] 
list3 = [10, 'a']
```

```text
⚠️ **Важно:** `list1` и `list3` ссылаются на **один и тот же** дефолтный список, поэтому изменения сохраняются между вызовами.
```
</details>

<details>
  <summary>Правильный вывод</summary>

```python
list1 = [10, 'a']
list2 = [123]
list3 = [10, 'a']
```
</details>

### 7. Реализовать базовый класс Robot

<details>
  <summary>Задача</summary>

```python
class Robot:
    """
    Базовый класс для роботов с базовыми боевыми характеристиками.
    """
    
    def __init__(self, name):
        """
        Конструктор робота.
        
        Args:
            name: кличка робота
        """
        self.name = name
        self.health = 100
        self.attack_power = 10
    
    def attack(self, target):
        """
        Атакует другого робота.
        
        Args:
            target: объект робота-цели
        """
        # Ваше решение здесь
        pass
    
    def take_damage(self, amount):
        """
        Обрабатывает полученный урон.
        
        Args:
            amount: количество единиц урона
        """
        # Ваше решение здесь
        pass
    
    @property
    def is_alive(self):
        """
        Проверяет, жив ли робот.
        
        Returns:
            bool: True если здоровье > 0, иначе False
        """
        # Ваше решение здесь
        pass
    
    def __str__(self):
        """
        Возвращает строковое представление робота.
        
        Returns:
            str: информация о роботе
        """
        # Ваше решение здесь
        pass

# Тесты
robot1 = Robot("R2-D2")
robot2 = Robot("C-3PO")

print("До атаки:")
print(robot1)
print(robot2)

robot1.attack(robot2)

print("\nПосле атаки:")
print(robot1)
print(robot2)

print(f"\n{robot2.name} жив: {robot2.is_alive}")
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text
1. Для is_alive используйте property декоратор
2. В attack вызывайте take_damage у цели
3. В take_damage уменьшайте health и проверяйте на смерть
4. В __str__ возвращайте форматированную строку с именем и здоровьем
```
</details>

<details>
  <summary>Решение</summary>

```python
class Robot:
    """
    Базовый класс для роботов с базовыми боевыми характеристиками.
    """
    
    def __init__(self, name):
        self.name = name
        self.health = 100
        self.attack_power = 10
    
    def attack(self, target):
        print(f"{self.name} атакует {target.name} с силой {self.attack_power}!")
        target.take_damage(self.attack_power)
    
    def take_damage(self, amount):
        self.health -= amount
        print(f"{self.name} получает {amount} урона. Здоровье: {self.health}")
        
        if self.health <= 0:
            print(f"💀 {self.name} уничтожен!")
    
    @property
    def is_alive(self):
        return self.health > 0
    
    def __str__(self):
        status = "жив" if self.is_alive else "уничтожен"
        return f"Робот {self.name} | Здоровье: {self.health} | Статус: {status}"

# Тесты
robot1 = Robot("R2-D2")
robot2 = Robot("C-3PO")

print("До атаки:")
print(robot1)
print(robot2)

robot1.attack(robot2)

print("\nПосле атаки:")
print(robot1)
print(robot2)

print(f"\n{robot2.name} жив: {robot2.is_alive}")
```

**Ожидаемый вывод:**
```
До атаки:
Робот R2-D2 | Здоровье: 100 | Статус: жив
Робот C-3PO | Здоровье: 100 | Статус: жив

R2-D2 атакует C-3PO с силой 10!
C-3PO получает 10 урона. Здоровье: 90

После атаки:
Робот R2-D2 | Здоровье: 100 | Статус: жив
Робот C-3PO | Здоровье: 90 | Статус: жив

C-3PO жив: True
```
</details>

### 8. Написать запросы Django ORM для моделей City и Person

<details>
  <summary>Задача</summary>

```python
# Даны модели:
class City(models.Model):
    name = models.CharField(max_length=100)

class Person(models.Model):
    name = models.CharField(max_length=100)
    city = models.ForeignKey(City, on_delete=models.CASCADE)

# Необходимо выполнить следующие запросы:
# 1. Вывести список людей и городов где они живут
# 2. Вывести всех людей, живущих в городе N
# 3. Вывести 5 городов с наибольшим населением, упорядочив по убыванию
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text
1. Для связанных моделей используйте select_related() и двойное подчеркивание
2. Для фильтрации используйте filter() с параметрами через двойное подчеркивание
3. Для агрегации используйте annotate() с Count() и order_by()
4. Для ограничения результатов используйте срезы [:N]
```
</details>

<details>
  <summary>Решение</summary>

```python
from django.db.models import Count

# 1. Вывести список людей и городов где они живут
people_with_cities = Person.objects.select_related('city').all()
for person in people_with_cities:
    print(f"{person.name} - {person.city.name}")

# Альтернативный вариант
people_data = Person.objects.select_related('city').values_list('name', 'city__name')
for name, city_name in people_data:
    print(f"{name} - {city_name}")

# 2. Вывести всех людей, живущих в городе N
city_name = "Москва"
people_in_city = Person.objects.select_related('city').filter(city__name=city_name)
for person in people_in_city:
    print(f"{person.name} живет в {person.city.name}")

# Если известен ID города
city_id = 1
people_in_city_by_id = Person.objects.filter(city_id=city_id)

# 3. Вывести 5 городов с наибольшим населением, упорядочив по убыванию
top_cities = City.objects.annotate(
    population=Count('person')
).order_by('-population')[:5]

for city in top_cities:
    print(f"{city.name} - {city.population} жителей")

# Альтернативный вариант с values
top_cities_data = City.objects.annotate(
    population=Count('person')
).order_by('-population').values('name', 'population')[:5]

for city in top_cities_data:
    print(f"{city['name']} - {city['population']} жителей")
```
</details>

### 9. Реализовать запуск миллиона асинхронных задач с ограничением concurrency

<details>
  <summary>Задача</summary>

```text
Необходимо запустить 1_000_000 асинхронных задач с ограничением:
не более 5 задач могут выполняться одновременно.

Требования:
1. Создать 1_000_000 задач с уникальными идентификаторами
2. Ограничить одновременное выполнение до 5 задач
3. Каждая задача должна имитировать работу (sleep 1 секунду)
4. Логировать начало и завершение каждой задачи
5. Обеспечить корректное выполнение всех задач без перегрузки системы
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text
1. Используйте asyncio.Semaphore(5) для ограничения concurrency
2. Применяйте async with semaphore: внутри функции задачи
3. Используйте asyncio.gather для запуска всех задач
4. Для имитации работы используйте asyncio.sleep(1)
5. Убедитесь, что все 1_000_000 задач будут выполнены
```
</details>

<details>
  <summary>Решение</summary>

```python
import asyncio

async def limited_task(task_id, semaphore):
    """
    Асинхронная задача с ограничением одновременного выполнения.
    
    Args:
        task_id: уникальный идентификатор задачи
        semaphore: семафор для контроля количества одновременных задач
    """
    async with semaphore:
        print(f"🚀 Задача {task_id} начата")
        await asyncio.sleep(1)  # Имитация работы (1 секунда)
        print(f"✅ Задача {task_id} завершена")
        return f"Результат задачи {task_id}"

async def main():
    """
    Основная функция для запуска 1_000_000 задач с ограничением concurrency.
    """
    # Создаем семафор, ограничивающий одновременное выполнение до 5 задач
    semaphore = asyncio.Semaphore(5)
    
    # Создаем 1_000_000 задач с уникальными идентификаторами
    tasks = [limited_task(i, semaphore) for i in range(1_000_000)]
    
    print(f"🎯 Запуск {len(tasks)} задач с ограничением 5 одновременных выполнений")
    
    # Запускаем все задачи конкурентно
    results = await asyncio.gather(*tasks, return_exceptions=True)
    
    # Статистика выполнения
    successful = len([r for r in results if not isinstance(r, Exception)])
    failed = len([r for r in results if isinstance(r, Exception)])
    
    print(f"📊 Статистика: Успешно {successful}, Ошибок {failed}")

# Запуск асинхронного приложения
if __name__ == "__main__":
    asyncio.run(main())
```
</details>

### 10. Написать декоратор с параметрами для форматирования текста

<details>
  <summary>Задача</summary>

```python
# Написать декоратор formatter, который принимает параметр - тег HTML
# и оборачивает результат функции в соответствующий HTML-тег

# Пример использования:
@formatter('b')
def hello(name: str):
    return f'Hello, {name}'

@formatter('i') 
def goodbye(name: str):
    return f'Goodbye, {name}'

# Ожидаемый результат:
# hello('Bob') → '<b>Hello, Bob</b>'
# goodbye('John') → '<i>Goodbye, John</i>'
```
</details>

<details>
  <summary>Спойлеры к решению</summary>

```text
1. Декоратор с параметром должен возвращать функцию-декоратор
2. Внутренняя функция-wrapper должна вызывать оригинальную функцию
3. Результат нужно обернуть в HTML-теги согласно параметру
4. Используйте замыкание для доступа к параметру letter
```
</details>

<details>
  <summary>Решение</summary>

```python
def formatter(letter):
    """
    Декоратор с параметром для оборачивания результата в HTML-тег.
    
    Args:
        letter: символ HTML-тега (например, 'b', 'i', 'p')
    
    Returns:
        декоратор функции
    """
    def letter_formatter(func):
        def wrapper(*args, **kwargs):
            # Вызываем оригинальную функцию
            result = func(*args, **kwargs)
            # Оборачиваем результат в HTML-теги
            formatted_result = f'<{letter}>{result}</{letter}>'
            return formatted_result
        return wrapper    
    return letter_formatter

# Пример использования
@formatter('b')
def hello(name: str):
    return f'Hello, {name}'

@formatter('i')
def goodbye(name: str):
    return f'Goodbye, {name}'

# Тестирование
assert hello('Bob') == '<b>Hello, Bob</b>'
assert goodbye('John') == '<i>Goodbye, John</i>'

print("✅ Все тесты пройдены!")
print(hello('Bob'))      # <b>Hello, Bob</b>
print(goodbye('John'))   # <i>Goodbye, John</i>
```
</details>
