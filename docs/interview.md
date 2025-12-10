---
title: Вопросы на подумать
description: Вопросы и ответы на сайте-методичке Python FAQ.
---

### У нас 2 объекта data, надо рассчитать сколько полных лет, чисто на python, используя библиотеки?
Для двух дат в Python полные годы обычно считают так: сравнивают разницу по годам и при необходимости вычитают 1, если «день‑месяц» второй даты ещё не достиг «день‑месяц» первой.​

**Вариант на чистом datetime**
```python
from datetime import date

def full_years_between(d1: date, d2: date) -> int:
    if d2 < d1:
        d1, d2 = d2, d1  # чтобы d1 <= d2

    years = d2.year - d1.year
    # если ещё не наступил «день рождения» в году d2, уменьшаем на 1
    if (d2.month, d2.day) < (d1.month, d1.day):
        years -= 1
    return years


a = date(2000, 5, 10)
b = date(2025, 5, 9)
print(full_years_between(a, b))  # 24
```
Здесь используются только стандартные классы date из модуля datetime, без сторонних пакетов.​

**Вариант со сторонней библиотекой**

Если разрешены внешние библиотеки, можно использовать python-dateutil с relativedelta, он сам корректно посчитает разницу в годах:​

```python
from datetime import date
from dateutil.relativedelta import relativedelta

d1 = date(2000, 5, 10)
d2 = date(2025, 5, 9)

delta = relativedelta(d2, d1)
full_years = delta.years  # количество полных лет
print(full_years)  # 24
```
Под «полные годы» в обоих вариантах понимается количество «дней рождения», которые успели пройти между датами.