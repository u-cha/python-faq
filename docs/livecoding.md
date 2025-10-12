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