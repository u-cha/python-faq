---
title: Generics
description: Вопросы и ответы по Generics на сайте-методичке Python FAQ.
---
## Generics 
Generics — это способ написать класс или функцию один раз, но при этом заранее указать, с каким типом объектов он будет работать.
Для этого в Python используется модуль typing и конструкция TypeVar, позволяющая задать универсальный параметр типа. Например, можно создать класс Box, который будет работать с любым типом T, и методы этого класса будут возвращать и принимать значения именно этого типа. Это улучшает читаемость кода и позволяет статическим анализаторам (например, mypy) проверять соответствие типов на этапе разработки.

Кроме TypeVar, часто применяется класс Generic, который позволяет создавать универсальные классы с параметрами типов. В Python 3.9+ появились обобщённые типы для встроенных коллекций (list, dict и др.), которые можно параметризовать напрямую как list[int] или dict[str, float]. Python 3.12 добавляет новый синтаксис для generic-типов, улучшающий удобство и гибкость их использования.

Основные возможности и примеры применения generics в Python:

* Универсальные контейнеры, например корзина или репозиторий, которые могут хранить элементы произвольного типа с типобезопасностью.

* Функции, которые принимают и возвращают значения одного и того же параметризованного типа.

* Поддержка ковариантности и контравариантности через параметры типа.

* Поддержка обобщённых коллекций с аннотациями типов, улучшенная в новых версиях Python.

Пример простого generic-класса:
```
from typing import Generic, TypeVar

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, item: T) -> None:
        self.item = item
    def get_item(self) -> T:
        return self.item
```
Такой класс Box можно использовать с любым типом, например Box[int] или Box[str], и это будет проверяться статическим анализатором.

Теперь сделаем ту же коробку, но с дженериком:
#### Пример 1. Корзина для предметов
```
if __name__ == '__main__':
    apple_box = Box('apple')
    print(apple_box.get_item())  # выведет 'apple'

    number_box = Box(123)
    print(number_box.get_item())  # выведет 123

    apple_box = Box[str](1)  # mypy выдаст ошибку, так как ожидается строка, а передаётся число

    print(apple_box.get_item())  # выведет 1, несмотря на ошибку mypy — в рантайме Python не проверяет типы
    
    print(some_box.get_item())  # вызовет ошибку NameError, так как some_box не определён в коде
```

#### Пример 2. Коллекция с ограничением типов
Сделаем корзину (Basket), куда можно складывать предметы только одного типа:
```
from typing import TypeVar, Generic, List

T = TypeVar('T')


class Basket(Generic[T]):
    def __init__(self):
        self.items: List[T] = []

    def add(self, item: T) -> None:
        self.items.append(item)

    def get_all(self) -> List[T]:
        return self.items

if __name__ == '__main__':
    fruit_basket = Basket[str]()
    fruit_basket.add("apple")
    fruit_basket.add("orange")
    print(fruit_basket.get_all())  # Выведет ['apple', 'orange']

    number_basket = Basket[int]()
    number_basket.add(1)
    number_basket.add(2)
    print(number_basket.get_all())  # Выведет [1, 2]

    fruit_basket.add(2)  # mypy/IDE выдаст предупреждение, т.к. добавляется int вместо str
```
#### Пример 3. Ограниченные дженерики (bound)

Иногда нужно разрешить только числа, а не всё подряд. Тогда мы говорим: «T должен быть числом» (bound=float):
```
from typing import Generic, TypeVar

NumberT = TypeVar('NumberT', bound=float)

class Calculator(Generic[NumberT]):
    def __init__(self, value: NumberT) -> None:
        self.value = value

    def add(self, other: NumberT) -> NumberT:
        return self.value + other

if __name__ == '__main__':
    calc = Calculator(10.5)
    print(calc.add(2))      # сработает, выведет 12.5, но mypy выдаст предупреждение, так как передается int, а ожидается float
    print(calc.add(3.5))    # сработает и выведет 14.0
    print(calc.add('s'))    # вызовет ошибку времени выполнения TypeError при попытке сложить float и str, но mypy выдаст предупреждение заранее
```
#### Пример 4. Репозиторий
Представьте, что у нас есть база данных с разными сущностями: User, Product. Вместо того чтобы писать одинаковый код для каждой, мы можем сделать дженерик-репозиторий:
```
from typing import Generic, TypeVar

T = TypeVar('T')


class Repository(Generic[T]):
    def __init__(self):
        self.items: list[T] = []

    def add(self, item: T) -> None:
        self.items.append(item)

    def get_all(self) -> list[T]:
        return self.items


class User:
    def __init__(self, name: str) -> None:
        self.name = name

    def __repr__(self):
        return f"User: {self.name}"


class Product:
    def __init__(self, title: str) -> None:
        self.title = title

    def __repr__(self):
        return f"Production: {self.title}"

if __name__ == '__main__':
    user_repo = Repository[User]()
    user_repo.add(User('Alice'))
    user_repo.add(User('Bob'))
    print(user_repo.get_all())  # выведет [User: Alice, User: Bob]

    product_repo = Repository[Product]()
    product_repo.add(Product('Iphone'))
    product_repo.add(Product('Laptop'))
    print(product_repo.get_all())  # выведет [Production: Iphone, Production: Laptop]

    user_repo.add(Product('Table'))  # предупреждение mypy, но выполнится — в user_repo появится Product('Table')
    product_repo.add(User('Miki'))    # предупреждение mypy, но выполнится — в product_repo появится User('Miki')
```
#### Пример 5. Дженерики + Protocol
А что если мы хотим складывать объекты (например, числа или строки)? Тогда можно сказать: «принимаю любой тип, у которого есть оператор +».
```
from typing import Protocol, TypeVar, Generic


class Addable(Protocol):
    def __add__(self, other: "Addable") -> "Addable": ...


T = TypeVar("T", covariant=True, bound=Addable)


class Summer(Generic[T]):
    def __init__(self, items: list[T]) -> None:
        self.items = items

    def total(self) -> T:
        result = self.items[0]
        for item in self.items[1:]:
            result += item
        return result


if __name__ == '__main__':
    print(Summer([1, 2, 3]).total())       # выведет 6 (сложение целых чисел)
    print(Summer(['a', 'b', 'c']).total()) # выведет 'abc' (конкатенация строк)
```

Таким образом, generics в Python позволяют писать более универсальный и типобезопасный код, облегчая поддержку и проверку программных систем.