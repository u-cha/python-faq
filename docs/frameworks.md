---
title: Фреймворки
description: Вопросы и ответы по фреймворкам Python на сайте-методичке Python FAQ.
---

## Что такое ORM? Примеры в Django и SQLAlchemy
ORM (Object-Relational Mapping) — это технология, которая позволяет взаимодействовать с базами данных, 
используя объекты, а не писать SQL-запросы напрямую. Вместо того чтобы писать SQL для выборки данных, 
ORM позволяет работать с объектами Python, которые соответствуют строкам в таблицах базы данных. 
Django и SQLAlchemy - два популярных инструмента Python, использующие ORM. 

### ORM в Django:
Django ORM является частью фреймворка Django и позволяет работать с базой данных через объекты Python. 
Вот пример:

    # Модель (определяет таблицу в базе данных)
    class Book(models.Model):
        title = models.CharField(max_length=200)
        author = models.CharField(max_length=100)
        publication_date = models.DateField()

    # Создание объекта
    book = Book(title="Война и мир", author="Толстой", publication_date="1869-01-01")
    book.save()

    # Получение объекта
    first_book = Book.objects.first()
    print(first_book.title) # Вывод: Война и мир

    # Фильтрация
    books_by_tolstoy = Book.objects.filter(author="Толстой")
    print(books_by_tolstoy.count()) # Вывод: количество книг Толстого

    # Обновление
    first_book.title = "Война и мир (полное издание)"
    first_book.save()


### ORM в SQLAlchemy:
SQLAlchemy — это отдельная библиотека, которая может быть использована с Django или без него. 
Она предоставляет более гибкий и мощный подход к ORM, чем Django ORM, особенно для сложных запросов. 
Python

    from sqlalchemy import create_engine, Column, Integer, String, Date
    from sqlalchemy.orm import sessionmaker
    from sqlalchemy.ext.declarative import declarative_base
    from sqlalchemy import ForeignKey
    from sqlalchemy.orm import relationship

    # Создание движка базы данных
    engine = create_engine('sqlite:///:memory:') # Пример использования in-memory базы данных
    Base = declarative_base()

    # Определение моделей
    class Author(Base):
        __tablename__ = 'authors'
        id = Column(Integer, primary_key=True)
        name = Column(String)
        books = relationship("Book", back_populates="author") # Отношение один-ко-многим
    
        def __repr__(self):
            return f"<Author(name='{self.name}')>"

    class Book(Base):
        __tablename__ = 'books'
        id = Column(Integer, primary_key=True)
        title = Column(String)
        publication_date = Column(Date)
        author_id = Column(Integer, ForeignKey('authors.id')) # Внешний ключ
        author = relationship("Author", back_populates="books")
    
        def __repr__(self):
            return f"<Book(title='{self.title}', author='{self.author.name}')>"

    Base.metadata.create_all(engine)
    
    # Создание сессии
    Session = sessionmaker(bind=engine)
    session = Session()
    
    # Создание объектов
    author1 = Author(name="Толстой")
    book1 = Book(title="Война и мир", publication_date="1869-01-01", author=author1)
    session.add_all([author1, book1])
    session.commit()
    
    # Получение данных
    author = session.query(Author).filter_by(name="Толстой").first()
    print(author.books) # Вывод: [<Book(title='Война и мир', author='Толстой')>]
    
    # Фильтрация и сортировка
    books = session.query(Book).filter(Book.publication_date > "1860-01-01").order_by(Book.title).all()
    print(books)

### Основные различия между Django ORM и SQLAlchemy:
- Гибкость:
SQLAlchemy предоставляет более гибкий и мощный API, позволяя создавать сложные запросы, 
в то время как Django ORM удобен для более простых операций. 
- Отдельная библиотека:
SQLAlchemy - это отдельная библиотека, а Django ORM - часть фреймворка Django.

## Как работает система аутентификации в Django?
Система аутентификации Django обеспечивает проверку подлинности пользователей и 
управление доступом к ресурсам. Она включает в себя модель пользователя, middleware 
для обработки запросов и представлений для управления входом, выходом и другими операциями 
с учетными записями. Аутентификация в Django основана на сессиях и cookie, 
делая атрибут `request.user` доступным в представлениях, который содержит информацию о текущем пользователе. 

### Основные компоненты системы аутентификации Django:
1. Модель пользователя (User Model):
Django предоставляет стандартную модель пользователя, которая включает поля для хранения 
информации о пользователе, такой как имя пользователя, пароль, email и т.д. 
Вы можете расширить эту модель или создать свою собственную. 
2. Middleware:
Middleware аутентификации обрабатывает входящие запросы, связывая пользователя 
с запросом на основе сессии. Он добавляет атрибут request.user к каждому запросу, 
который может быть использован в представлениях для определения текущего пользователя. 
3. Представления (Views):
Django предоставляет набор встроенных представлений для работы с аутентификацией:
`LoginView`: Для входа в систему.
`LogoutView`: Для выхода из системы.
`PasswordChangeView`: Для изменения пароля.
`PasswordResetView`: Для сброса пароля. 


4. Аутентификационные бэкенды:
Django позволяет использовать различные бэкенды аутентификации, такие как аутентификация 
по имени пользователя и паролю, аутентификация через социальные сети и т.д. 
Вы можете настроить используемые бэкенды в настройках проекта. 
5. Разрешения:
Django предоставляет систему разрешений, которая позволяет управлять доступом 
к различным частям приложения на основе ролей и прав пользователей. 
Как работает аутентификация в Django:
Пользователь отправляет запрос на вход в систему.
Представление входа, например `LoginView`, получает данные пользователя (имя пользователя, пароль) и 
вызывает функцию `authenticate()`. 

`authenticate()` перебирает настроенные бэкенды аутентификации и 
пытается аутентифицировать пользователя. Если один из бэкендов успешно аутентифицирует пользователя, 
возвращается объект пользователя. 

Если аутентификация прошла успешно, Django создает сессию и сохраняет информацию о пользователе в cookie.
При каждом последующем запросе, middleware аутентификации использует сессию и cookie для определения 
текущего пользователя и делает его доступным в `request.user`. 

В представлениях вы можете проверить, аутентифицирован ли пользователь, 
используя `request.user.is_authenticated`. 

Вы также можете использовать систему разрешений для контроля доступа к определенным ресурсам или функциям. 

**Пример использования**:
    from django.shortcuts import render
    from django.contrib.auth.decorators import login_required
    @login_required
    def my_protected_view(request):
        # Эта функция доступна только для аутентифицированных пользователей
        return render(request, 'my_template.html', {'user': request.user})

В этом примере, `@login_required` декоратор гарантирует, что функция `my_protected_view` доступна 
только для аутентифицированных пользователей. Если пользователь не аутентифицирован, 
Django перенаправит его на страницу входа. 

## Что такое middleware в Django?
Middleware в Django — это промежуточный слой обработки запросов и ответов, представляющий собой набор функций/классов, которые обрабатывают запросы и ответы перед тем, как они достигнут view или после того, как view сгенерирует ответ.
Каждый middleware компонент отвечает за определенную функцию, такую как аутентификация пользователей, управление сессиями или защита от атак. 

Как работает middleware:

1. Обертывает запрос: middleware выступает как обертка вокруг других функций или middleware.
2. Обрабатывает запрос: перед тем как запрос достигнет целевого обработчика (например, вашей функции просмотра), middleware может модифицировать его или выполнить необходимую логику.
3. Обрабатывает ответ: после того как запрос был обработан, middleware может модифицировать ответ перед его отправкой клиенту.
4. Передает управление: middleware может либо передать управление следующему middleware (или просмотру), либо вернуть ответ самостоятельно, прервав дальнейшую обработку. 

Архитектура Middleware

    Запрос → MIDDLEWARE[0] → MIDDLEWARE[1] → ... → View
    Ответ  ← MIDDLEWARE[1] ← MIDDLEWARE[0] ← ... ← View

### Стандартные middleware в Django
    # settings.py

    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
    ]
Примеры использования middleware:

- SecurityMiddleware: Обеспечивает защиту от различных атак, например, от XSS или CSRF.
- AuthenticationMiddleware: Связывает пользователя с запросом на основе сессии.
- SessionMiddleware: Обеспечивает поддержку механизма сессий в Django.
- CsrfViewMiddleware: Проверяет запросы на наличие CSRF-токена для предотвращения подделки запросов.

### Создание кастомного middleware

Пример: Логирование запросов

    import time
    
    class TimingMiddleware:
        def __init__(self, get_response):
            self.get_response = get_response
    
        def __call__(self, request):
            start_time = time.time()
            
            response = self.get_response(request)
            
            duration = time.time() - start_time
            print(f"Запрос {request.path} выполнен за {duration:.2f} секунд")
            
            return response

В чем польза middleware:

- Гибкость: позволяет добавлять глобальную логику обработки без необходимости изменять код приложения.
- Модульность: каждый компонент отвечает за свою функцию, что делает код более чистым и легко поддерживаемым. 
- Переиспользуемость: один и тот же middleware может использоваться в разных проектах для выполнения одинаковых задач
- Middleware — это мощный механизм для сквозной функциональности в Django-приложениях.

---

## Какой паттерн в основе Django?
В основе Django лежит архитектурный паттерн
Model-View-Template (MVT), который является модификацией более распространенного паттерна Model-View-Controller (MVC). 

Обычно MVC описывают подобным образом:

- Model — доступ к хранилищу данных

- View — это интерфейс, с которым взаимодействует пользователь 

- Controller — некий связывающий объект между model и view.

MVT (Model-View-Template)

**Model** (Модель)

- Отвечает за данные и бизнес-логику
- Определяет структуру базы данных
- Содержит валидацию и связи между данными

**View** (Представление)

- Обрабатывает запросы и возвращает ответы
- Содержит логику приложения
- Взаимодействует с моделями и шаблонами

**Template** (Шаблон)

- Отвечает за представление данных (UI)
- HTML с Django Template Language
- Отделяет логику от представления

    
    Запрос → URLconf → View → Model → Template → Ответ
        ↓
    (Controller - реализован в самом фреймворке)

![img.png](img.png)

---
## Какой путь проходит запрос в Django?
Когда к приложению приходит запрос, то URL dispatcher определяет, с каким ресурсом сопоставляется данный запрос и передает этот запрос выбранному ресурсу. Ресурс фактически представляет функцию или View, который получает запрос и определенным образом обрабатывает его. В процессе обработки View может обращаться к моделям и базе данных, получать из нее данные, или, наоборот, сохранять в нее данные. Результат обработки запроса отправляется обратно, и этот результат пользователь видит в своем браузере. Как правило, результат обработки запроса представляет сгенерированный html-код, для генерации которого применяются шаблоны (Template)
    
    HTTP Запрос → WSGI Сервер → Middleware → URLconf → View → Model 
    → Template → Middleware → HTTP Ответ

---
## FastAPI vs Django: когда что использовать?

### Когда выбирать Django:

**✅ Для сложных веб-приложений с богатым функционалом**
```python
# Админка из коробки
from django.contrib import admin
from .models import Product

@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):
    list_display = ['name', 'price', 'created_at']
    # Готовая CRUD панель за 5 строк кода!
```

**✅ Проекты с требовательной бизнес-логикой**
- Системы управления контентом (CMS)
- E-commerce платформы
- Социальные сети
- CRM/ERP системы

**✅ Когда нужна скорость разработки**
```python
# Полная аутентификация за 5 минут
INSTALLED_APPS = [
    'django.contrib.auth',
    'django.contrib.sessions',
]

# В urls.py
path('accounts/', include('django.contrib.auth.urls'))
```

**✅ Проекты с классическим HTML-интерфейсом**
- Серверный рендеринг (SSR)
- Формы с валидацией
- Пагинация, фильтрация
- Админ-панель для управления данными

### Когда выбирать FastAPI:

**✅ API-ориентированные приложения**
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float

@app.post("/items/")
async def create_item(item: Item):
    # Автоматическая валидация + документация
    return {"item": item}
```

**✅ Микросервисная архитектура**
```python
# Каждый сервис независим
@app.get("/users/{user_id}")
async def read_user(user_id: int):
    return {"user_id": user_id, "name": "John"}

# Легко масштабируется горизонтально
```

**✅ Высокие требования к производительности**
```python
# Асинхронные endpoint-ы
@app.get("/data/")
async def get_live_data():
    data = await fetch_from_database()  # Не блокирует другие запросы
    return data
```

**✅ Проекты с автоматической документацией**
```python
# Документация доступна сразу:
# - /docs (Swagger UI)
# - /redoc (ReDoc)
# Генерируется автоматически!
```

### **Сравнительная таблица**

| Критерий | Django | FastAPI |
|----------|---------|----------|
| **Производительность** | Хорошая | **Очень высокая** |
| **Скорость разработки** | **Очень высокая** | Высокая |
| **Асинхронность** | Ограниченная | **Полная поддержка** |
| **API документация** | Сторонние пакеты | **Автоматическая** |
| **Админ-панель** | **Встроенная** | Требует разработки |
| **ORM** | **Встроенный Django ORM** | Любая (SQLAlchemy, Tortoise) |
| **Кривая обучения** | Пологая | Крутая (требует знаний типов) |
| **Сообщество** | **Огромное** | Растущее |
| **Микросервисы** | Не идеально | **Идеально** |

---
## Что такое Django REST Framework (DRF)?
**Django REST Framework (DRF)** — это мощный инструмент для создания **Web API** поверх Django. Он предоставляет архитектурные компоненты для построения RESTful API.
DRF расширяет Django, добавляя специализированные компоненты для работы с API, сохраняя при этом все преимущества Django (ORM, аутентификацию, миграции).

### Ключевые компоненты

1. **Сериализаторы**
- **Назначение**: Преобразование данных между форматами (Python объекты ↔ JSON/XML)
- **Аналогия**: Как Django Forms, но для API
- **Функции**: Валидация данных, преобразование типов, контроль доступа к полям

2. **ViewSets и ViewClasses**
- **ViewSets**: Автоматическое создание CRUD операций для моделей
- **APIView**: Более гибкий контроль над отдельными endpoint'ами
- **Generic Views**: Готовые шаблоны для стандартных операций

3. **Роутеры**
- **Назначение**: Автоматическая генерация URL маршрутов для ViewSets
- **Преимущество**: Сокращает рутинный код маршрутизации

4. **Аутентификация и Права доступа**
- **Аутентификация**: Определение пользователя (Token, Session, JWT)
- **Права доступа**: Контроль действий пользователя (ReadOnly, IsAuthenticated, кастомные)

### Архитектурные преимущества

**Согласованность API**

- Единый подход ко всем endpoint'ам
- Стандартизированные форматы ответов
- Предсказуемая структура ошибок

**Безопасность**

- Встроенные механизмы защиты от CSRF
- Гибкая система прав доступа
- Валидация данных на уровне сериализаторов

**Производительность**

- Пагинация для больших наборов данных
- Оптимизация запросов к БД
- Кэширование на разных уровнях

### Сценарии использования

**✅ Идеально для:**
- Backend для мобильных приложений
- API для SPA (React, Vue, Angular)
- Микросервисная архитектура
- Публичные API для сторонних разработчиков

**❌ Менее подходит для:**
- Чисто серверрендерных приложений (только HTML)
- Простых сайтов-визиток
- Когда не нужен програмный интерфейс

### Главные преимущества перед "простым" Django

1. **Специализация** — оптимизирован specifically для API
2. **Стандартизация** — единый подход ко всем endpoint'ам  
3. **Документирование** — автоматическая генерация документации
4. **Экосистема** — богатый набор готовых компонентов
5. **Сообщество** — активная разработка и поддержка

DRF превращает Django из фреймворка для веб-сайтов в мощную платформу для создания API любого масштаба.

---
## Разница между APIView и ViewSet?
**APIView** и **ViewSet** — это два разных уровня абстракции для создания API endpoint'ов в DRF.

**APIView** — Базовый уровень (ручное управление)

**Концепция**:

- **Один класс = Один endpoint**
- **Явное определение** HTTP-методов
- **Полный контроль** над логикой

**Структура**:

```python
class UserAPI(APIView):
    def get(self, request): pass    # GET /users/
    def post(self, request): pass   # POST /users/
    
class UserDetailAPI(APIView):
    def get(self, request, pk): pass     # GET /users/1/
    def put(self, request, pk): pass     # PUT /users/1/
    def delete(self, request, pk): pass  # DELETE /users/1/
```

**Когда использовать**:

- **Нестандартная логика** обработки запросов
- **Специфичные endpoint'ы** (не CRUD)
- **Тонкая настройка** каждого метода
- **Микросервисы** с небольшой функциональностью

 **ViewSet** — Высокий уровень (автоматизация)

**Концепция:**

- **Один класс = Все операции с ресурсом**
- **Автоматическая** маршрутизация
- **Стандартные** CRUD операции

**Структура**:

```python
class UserViewSet(ViewSet):
    def list(self, request): pass      # GET /users/
    def create(self, request): pass    # POST /users/
    def retrieve(self, request, pk): pass   # GET /users/1/
    def update(self, request, pk): pass     # PUT /users/1/
    def destroy(self, request, pk): pass    # DELETE /users/1/
```

**Когда использовать**:

- **Стандартные CRUD** операции
- **Быстрая разработка** API
- **Ресурс-ориентированная** архитектура
- **Админ-панели** и бэкенды

**ModelViewSet** — Максимальная автоматизация

**Концепция**:

- **Автоматические** CRUD операции для модели
- **Минимальный** код

```python
class UserViewSet(ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    # Полный CRUD готов!
```

### Сравнительная таблица

| Критерий             | APIView | ViewSet |
|----------------------|---------|---------|
| **Уровень контроля** | ✅ **Полный** | ⚠️ **Ограниченный** |
| **Количество кода**  | ❌ **Много** | ✅ **Мало** |
| **Стандартизация**   | ❌ **Свободная** | ✅ **Единая** |
| **Маршрутизация**    | ❌ **Ручная** | ✅ **Ручная** |
| **CRUD операции**    | ❌ **Явные** | ✅ **Автоматические** |

---
## Как работают сериализаторы? (ModelSerializer vs Serializer)
Сериализатор в DRF — это компонент, который преобразует сложные типы данных (например, экземпляры моделей Django QuerySet'ы) в простые форматы (JSON, XML и т.д.) для передачи по API, и наоборот — валидирует входящие данные и преобразует их обратно в сложные типы.

**Как они работают:**

- _Направление данных_: Получают данные из представления (например, JSON из HTTP-запроса).
- _Валидация_: Проверяют корректность данных с помощью определенных полей и валидаторов.
- _Десериализация_: Если данные прошли валидацию, они преобразуются в объект Python (validated_data).
- _Сериализация_: Если требуется, эти данные из объекта Python сериализуются обратно в формат, понятный для клиента (например, JSON).
- _Сохранение_: Метод .save() сохраняет данные в базе данных с помощью встроенных методов create() или update()

ModelSerializer и Serializer работают одинаково, но **ModelSerializer** автоматически создает поля на основе модели Django, 
тогда как **Serializer** используется для более гибкого контроля или когда связь с моделью не требуется. 
Оба сериализатора преобразуют данные (например, из запроса) в форматы, понятные для Django (или других фреймворков), и наоборот.

**Serializer**

 - _Назначение_: Когда вам нужна большая гибкость в определении полей или когда вы не работаете с моделью Django.
 - _Пример_: Вы можете использовать его для создания JSON, который не соответствует напрямую модели, например, для агрегированных данных или данных из нескольких моделей. 

        from rest_framework import serializers
        from .models import Book
        
        class BookSerializer(serializers.Serializer):
            id = serializers.IntegerField(read_only=True)
            title = serializers.CharField(max_length=200)
            author = serializers.CharField(max_length=100)
            published_date = serializers.DateField()
            is_available = serializers.BooleanField(default=True)


**ModelSerializer**

 - _Назначение_: Быстрое создание сериализатора для модели Django.
   - _Пример_: Вы просто указываете model и fields в классе, и DRF (Django REST Framework) автоматически генерирует поля и валидаторы на основе этой модели. Это избавляет от необходимости вручную описывать каждое поле

          from rest_framework import serializers
          from .models import Book
        
          class BookModelSerializer(serializers.ModelSerializer):
              class Meta:
                  model = Book
                  fields = ['id', 'title', 'author', 'published_date', 'is_available']
                  # fields = '__all__'  # Все поля модели
                  read_only_fields = ['id']  # Поля только для чтения
            
              # Дополнительная валидация (опционально)
              def validate_title(self, value):
                  if len(value) < 2:
                      raise serializers.ValidationError("Название слишком короткое")
                  return value

**Когда что использовать?**

✅  ModelSerializer когда:

- Работаете с моделями Django 
- Нужна быстрая разработка 
- Поля сериализатора соответствуют полям модели 
- Стандартное поведение create/update подходит

✅  Базовый Serializer когда:

- Данные не привязаны к модели 
- Нужна сложная кастомная логика 
- Требуется нестандартная валидация 
- Источник данных — несколько моделей или внешние API

Статья на хабре - [Django Rest Framework для начинающих: как работает ModelSerializer](https://habr.com/ru/companies/yandex_praktikum/articles/598349/)
https://habr.com/ru/companies/yandex_praktikum/articles/598349/

---
## Как оптимизировать QuerySet? (select_related, prefetch_related)

**QuerySet** — это фундаментальное понятие в Django, которое представляет собой коллекцию объектов из базы данных.

QuerySet — это "ленивый" (lazy) объект, который:

- Описывает SQL запрос к базе данных 
- Не выполняется до момента реального использования данных 
- Позволяет строить сложные запросы цепочкой методов

N+1 проблема возникает когда:

- Сначала выполняется 1 запрос для получения основной коллекции объектов
- Затем для каждого объекта выполняется дополнительный запрос для получения связанных данных

        class Book(models.Model):
            title = models.CharField(max_length=200)
            authors = models.ManyToManyField(Author)  # ManyToMany
    
        class Author(models.Model):
            name = models.CharField(max_length=100)

            books = Book.objects.all()
            for book in books:
                print(book.title)           # 1 запрос
                print(book.author.name)     # N запросов (по одному на каждую книгу)

**Select_related** используется для:

- Для ForeignKey и OneToOneField 
- Выполняет JOIN на уровне SQL
- Загружает связанные объекты в одном запросе

_Синтаксис_:
    
    queryset.select_related('related_field')

    books = Book.objects.select_related('author').all()

**Prefetch_related** используется для:

- Для ManyToManyField и reverse ForeignKey
- Выполняет отдельный запрос для каждого отношения
- Оптимизирует загрузку с помощью Python

_Синтаксис_:
    
    queryset.prefetch_related('related_field')
    
    authors = Author.objects.prefetch_related('book_set').all()

Статьи на хабре - [Как работают select_related и prefetch_related в Django ](https://habr.com/ru/articles/752574/)https://habr.com/ru/articles/752574/

[Django против N+1 запросов: оптимизация с помощью select_related и prefetch_related](https://habr.com/ru/companies/otus/articles/875384/) https://habr.com/ru/companies/otus/articles/875384/


---
## Как реализована связь ManyToMany на уровне БД?
Связь ManyToMany в реляционных базах данных реализуется через промежуточную таблицу (join table).

Модели Django:

    class Student(models.Model):
        name = models.CharField(max_length=100)
    
    class Course(models.Model):
        title = models.CharField(max_length=100)
        students = models.ManyToManyField(Student)  # Связь ManyToMany

Соответствующая структура БД:
    
    -- Таблица студентов
    CREATE TABLE app_student (
        id SERIAL PRIMARY KEY,
        name VARCHAR(100) NOT NULL
    );
    
    -- Таблица курсов  
    CREATE TABLE app_course (
        id SERIAL PRIMARY KEY,
        title VARCHAR(100) NOT NULL
    );
    
    -- ПРОМЕЖУТОЧНАЯ таблица (автоматически создается Django)
    CREATE TABLE app_course_students (
        id SERIAL PRIMARY KEY,
        course_id INTEGER REFERENCES app_course(id) ON DELETE CASCADE,
        student_id INTEGER REFERENCES app_student(id) ON DELETE CASCADE,
        UNIQUE(course_id, student_id)  -- Уникальная пара
    );

Реализация в Django - атоматическая промежуточная таблица (поле ManyToManyField)
    
    class Course(models.Model):
        title = models.CharField(max_length=100)
        students = models.ManyToManyField(Student)
        # Django автоматически создаст app_course_students

    # Создание объектов
    math = Course.objects.create(title="Математика")
    physics = Course.objects.create(title="Физика")
    
    ivan = Student.objects.create(name="Иван")
    maria = Student.objects.create(name="Мария")
    
    # ДОБАВЛЕНИЕ связей
    math.students.add(ivan)           # Один объект
    math.students.add(ivan, maria)    # Несколько объектов
    math.students.add(*[ivan, maria]) # Список объектов
    
    # СОЗДАНИЕ через промежуточную таблицу (если through)
    enrollment = Enrollment.objects.create(
        student=ivan, 
        course=math, 
        grade='A'
    )
    
    # УДАЛЕНИЕ связей
    math.students.remove(ivan)        # Удалить одного
    math.students.clear()             # Удалить всех
    
    # ПРОВЕРКА связей
    if ivan in math.students.all():
        print("Иван записан на математику")
    
    # КОЛИЧЕСТВО
    student_count = math.students.count()

QuerySet операции:

    # Все студенты курса
    students = math.students.all()
    
    # Все курсы студента  
    courses = ivan.course_set.all()
    
    # Фильтрация через связь
    math_courses = Course.objects.filter(students__name='Иван')
    smart_students = Student.objects.filter(course__title='Математика')

---

## Как работают миграции в Django?
Миграции в Django — это система контроля версий для структуры базы данных. Они позволяют изменять схему БД без потери данных и синхронизировать изменения между разными окружениями.
Как работают миграции?

Основной принцип:
- Миграции описывают изменения в моделях Django 
- Django генерирует SQL на основе этих описаний 
- Применяет SQL к БД в правильном порядке

### Процесс работы с миграциями Django

**Создание миграций**
```bash
# После изменения models.py
python manage.py makemigrations
```
- Django анализирует изменения моделей
- Генерирует файлы миграций в папке `migrations/`
- **Результат:** файлы типа `0002_add_field.py`

**Проверка (опционально)**
```bash
# Посмотреть SQL который выполнится
python manage.py sqlmigrate app_name 0002

# Посмотреть план применения
python manage.py showmigrations
```

**Применение миграций**
```bash
# Применить все миграции
python manage.py migrate

# Применить для конкретного приложения
python manage.py migrate app_name
```
- Django выполняет SQL команды в БД
- Записывает факт применения в таблицу `django_migrations`

**Откат при необходимости**
```bash
# Откатить до конкретной миграции
python manage.py migrate app_name 0001

# Полный откат приложения
python manage.py migrate app_name zero
```

### **Основные команды**:

| Команда | Назначение |
|---------|------------|
| `makemigrations` | Создать миграции после изменения моделей |
| `migrate` | Применить миграции к БД |
| `showmigrations` | Показать статус миграций |
| `sqlmigrate` | Показать SQL для миграции |

**Важно**:

- **Миграции создаются** после изменения `models.py`
- **Миграции применяются** на каждой копии проекта (dev/stage/prod)
- **Порядок важен** - миграции применяются последовательно
- **Файлы миграций** нужно коммитить в Git

**Процесс:** Изменил модели → `makemigrations` → `migrate` → Повторить на всех серверах

---
## Как работают сигналы (signals)? Когда их использовать?
Сигналы работают как механизм уведомления о произошедших событиях или изменениях.
Они используются для разделения логики, создания модульности и реализации многоуровневой обработки событий
Они реализуют паттерн "наблюдатель", когда одни части кода могут "подписываться" на события из других частей.

### Основные типы сигналов

**Модельные сигналы (самые используемые)**:

- `pre_save` - вызывается перед сохранением объекта
- `post_save` - вызывается после сохранения объекта  
- `pre_delete` - перед удалением объекта
- `post_delete` - после удаления объекта
- `m2m_changed` - при изменении ManyToMany связи

### Как работают сигналы?

Сигналы состоят из двух частей:

1. **Отправитель** (sender) - тот, кто генерирует событие
2. **Получатель** (receiver) - функция, которая реагирует на событие

Базовый пример:
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

# Получатель - функция с декоратором @receiver
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    """
    Автоматически создаем профиль при создании пользователя
    """
    if created:  # created=True только при создании нового объекта
        Profile.objects.create(user=instance)
```

### Практические примеры использования

- Автоматическое создание связанных объектов
```python
@receiver(post_save, sender=User)
def setup_new_user(sender, instance, created, **kwargs):
    if created:
        # Создаем профиль, настройки и корзину для нового пользователя
        Profile.objects.create(user=instance)
        UserSettings.objects.create(user=instance)
        Cart.objects.create(user=instance)
```

- Кеширование данных
```python
@receiver(post_save, sender=Product)
@receiver(post_delete, sender=Product)
def clear_product_cache(sender, **kwargs):
    """Очищаем кеш при изменении продуктов"""
    cache.delete('featured_products')
    cache.delete('product_list')
```

- Уведомления
```python
@receiver(post_save, sender=Order)
def notify_about_order(sender, instance, created, **kwargs):
    if created:
        # Отправляем email менеджеру о новом заказе
        send_mail(
            'Новый заказ',
            f'Поступил новый заказ #{instance.id}',
            'noreply@shop.com',
            ['manager@shop.com']
        )
```

### Когда стоит использовать сигналы?

- **Автоматические действия** - когда нужно выполнить действие автоматически в ответ на событие:
    - Создание связанных записей
    - Отправка уведомлений
    - Очистка кеша
    - Синхронизация данных

- **Сквозная функциональность** - логирование, аудит, безопасность

- **Интеграции** - когда разные части приложения слабо связаны

### Проблемы и ограничения сигналов

1. **Сложность отладки** - не очевидно, какие обработчики выполняются
2. **Циклические зависимости** - могут возникнуть бесконечные циклы
3. **Производительность** - много сигналов могут замедлить приложение
4. **Тестирование** - сложнее тестировать код с сигналами

Статья на хабре - [Signals в Django](https://habr.com/ru/companies/otus/articles/873118/ ) https://habr.com/ru/companies/otus/articles/873118/ 

---

## Какие преимущества дает использование async в FastAPI?

**1. Высокая производительность и масштабируемость**

При использовании асинхронного программирования FastAPI может обрабатывать тысячи одновременных подключений на одном процессе. Это достигается за счет неблокирующей природы async - когда один запрос ожидает ответа от базы данных или внешнего API, event loop может переключиться на обработку других запросов. В результате сервер не простаивает в ожидании I/O операций, что кардинально увеличивает пропускную способность приложения.

**2. Эффективное управление ресурсами**

Традиционные синхронные приложения требуют создания отдельных потоков или процессов для каждого одновременного подключения, что потребляет значительные ресурсы памяти и процессора. Асинхронный подход позволяет обслуживать множество запросов в рамках одного потока, drastically снижая нагрузку на систему и позволяя серверу работать с ограниченными ресурсами более эффективно.

**3. Улучшенная обработка I/O операций**

Подавляющее большинство веб-приложений являются I/O-bound, то есть их производительность ограничена операциями ввода-вывода: запросами к базам данных, вызовами внешних API, чтением файлов. Async операции идеально подходят для таких сценариев, поскольку позволяют приостанавливать выполнение функции на время ожидания ответа от внешних систем, освобождая ресурсы для обработки других запросов.

**4. Естественная поддержка современных протоколов**

FastAPI с async отлично работает с современными веб-протоколами, такими как WebSockets для реального времени и HTTP/2 для улучшенной производительности. Асинхронная модель идеально подходит для long-lived соединений, где требуется постоянная двусторонняя связь между клиентом и сервером без блокировки других операций.

**5. Упрощенный код для сложных операций**

Хотя асинхронное программирование требует определенного обучения, оно упрощает написание кода для сложных сценариев, таких как параллельное выполнение нескольких внешних запросов или обработка множества событий одновременно. Конструкции типа `asyncio.gather()` позволяют легко организовывать параллельное выполнение независимых операций.

**6. Совместимость с современной экосистемой**

Большинство современных библиотек для Python, особенно для работы с базами данных, HTTP-клиентов и кэширования, теперь предоставляют асинхронные интерфейсы. Это позволяет создавать полностью асинхронные приложения, где все компоненты работают в единой неблокирующей парадигме, максимизируя производительность.

**7. Будуще-ориентированность**

Асинхронное программирование становится стандартом для высокопроизводительных веб-приложений. Инвестируя в изучение и использование async сейчас, разработчики готовятся к будущим требованиям и технологиям, обеспечивая долгосрочную жизнеспособность своих приложений.

Важно отметить, что async не является панацеей и наиболее эффективен именно для I/O-bound приложений. Для CPU-intensive задач, где ограничением является процессорное время, традиционные многопоточные или многопроцессные подходы могут оказаться более подходящими. Однако для типичных веб-API, которые в основном работают с базами данных и внешними сервисами, переход на async обеспечивает существенный прирост производительности при меньшем потреблении ресурсов.

---
## Dependency Injection в FastAPI
Dependency Injection (DI) — это архитектурный паттерн, при котором компоненты получают свои зависимости из внешних источников, вместо того чтобы создавать их внутри себя. В FastAPI это встроенная система, которая автоматически управляет зависимостями и внедряет их там, где они нужны.
Dependency Injection в FastAPI — это мощный инструмент для создания чистого, модульного и тестируемого кода. Он помогает разделять ответственность и упрощает поддержку приложения.

FastAPI использует механизм Depends() для объявления зависимостей. Когда запрос поступает на endpoint, FastAPI автоматически:

- Определяет, какие зависимости требуются 
- Выполняет эти зависимости 
- Внедряет результаты в endpoint функцию

Базовый пример

    from fastapi import FastAPI, Depends
    
    app = FastAPI()
    
    def get_database():
        return "Database Connection"
    
    @app.get("/items/")
    async def read_items(db: str = Depends(get_database)):
        return {"data": db}
В этом примере get_database — это зависимость, которая автоматически выполняется при каждом запросе к /items/, и ее результат передается в параметр db.

### **Практическое применение**

1. **Аутентификация**

    def get_current_user(token: str = Header(...)):
        user = decode_jwt(token)
        return user
    
    @app.get("/profile")
    async def profile(user: dict = Depends(get_current_user)):
        return user

2. **Валидация данных**
    
    def validate_ids(ids: str = Query(...)):
        return [int(id) for id in ids.split(",")]
    
    @app.get("/items/")
    async def get_items(ids: list = Depends(validate_ids)):
        return {"ids": ids}

### **🎯 Преимущества Dependency Injection**

**🧪 Тестируемость**
Mock-зависимости для изолированного тестирования

**🔄 Переиспользование**
Одна зависимость — множество endpoints

**🏗 Архитектура**
Четкое разделение ответственности

**⚡ Автоматизация**
FastAPI сам управляет зависимостями

**🔧 Гибкость**
Легко менять реализации для разных окружений

**🛠 Поддерживаемость**
Изменения в одном месте применяются везде

Статьи на хабре [FastAPI и Dependency Injection: правда или вымысел?](https://habr.com/ru/articles/867040/)

[FastAPI + Dependency Injector / Хабр](https://habr.com/ru/articles/528634/)


---
## Валидация данных (Pydantic модели)
Валидация данных с помощью Pydantic-моделей — это
процесс проверки данных на соответствие ожидаемым типам и форматам с использованием классов, основанных на подсказках типов Python. Pydantic автоматически проверяет входные данные, выполняет их преобразование при необходимости, и выдает подробные сообщения об ошибках при несоответствии. Это упрощает разработку, обеспечивает корректность данных и упрощает документацию API. 

**Как это работает**

- _Определение моделей_: Вы создаете классы, наследуемые от BaseModel (или с помощью датаклассов) и указываете типы для каждого поля, например int, str, datetime.
- _Автоматическая валидация_: При создании объекта модели Pydantic проверяет, соответствуют ли переданные значения указанным типам. Если вы передаете, например, строку вместо целого числа, возникнет ошибка.
- _Преобразование данных_: Pydantic может автоматически преобразовывать данные. Если вы ожидаете дату, а получаете строку, он попытается преобразовать строку в объект datetime.
- _Подробные сообщения об ошибках_: В случае ошибки Pydantic предоставляет подробное сообщение, указывающее, что ожидалось, что было получено и где искать проблему.
- _Сериализация/десериализация_: Модели Pydantic позволяют легко преобразовывать данные между форматом модели и форматами, такими как JSON. 

```python
from pydantic import BaseModel, EmailStr, Field
from typing import List, Optional

class UserCreate(BaseModel):
    email: EmailStr                    # Автоматическая валидация email
    age: int = Field(gt=0, le=120)     # Ограничения: 0 < age <= 120
    name: str = Field(min_length=2, max_length=50)
    tags: List[str] = []               # Список с значением по умолчанию
```

### **✅ Типы валидации**

#### **Встроенные валидаторы**
```python
from pydantic import validator

class UserCreate(BaseModel):
    email: EmailStr
    
    @validator('name')
    def name_must_contain_space(cls, v):
        if ' ' not in v:
            raise ValueError('Must contain space')
        return v.title()  # Автоматическое преобразование
```

#### **Кастомные ограничения**
```python
class Product(BaseModel):
    price: float = Field(gt=0)           # Только положительные значения
    quantity: int = Field(ge=0)          # Неотрицательное число
    category: str = Field(regex='^[A-Z][a-z]+$')  # Регулярные выражения
```

### **🚀 Использование в эндпоинтах**

```python
@app.post("/users/")
async def create_user(user: UserCreate):  # ← Автовалидация
    # Данные уже проверены и приведены к правильным типам
    return {"message": "User created", "user": user.dict()}

@app.put("/items/{item_id}")
async def update_item(
    item_id: int,
    item: UserCreate,                    # Тело запроса
    user: User = Depends(get_current_user)  # Зависимости + валидация
):
    return {"item_id": item_id, **item.dict()}
```

### **⚡ Преимущества**

**Автоматическая документация**

- Swagger UIсразу показывает схемы данных
- Примеры запросов генерируются автоматически
- Ошибки валидации - детальные сообщения

**Производительность**

- Валидация при парсинге - один проход для всех проверок
- JSON → Python объекты - быстрое преобразование
- Подсказки типов - лучше автодополнение в IDE

**Безопасность**

- Защита от мусорных данных - только объявленные поля
- Приведение типов - "123" → 123 (если ожидается int)
- Чистые данные - гарантированная структура

Статья на хабре [Pydantic 2: Полное руководство для Python-разработчиков — от основ до продвинутых техник](https://habr.com/ru/companies/amvera/articles/851642/) https://habr.com/ru/companies/amvera/articles/851642/

---
## Работа с асинхронными драйверами БД (asyncpg, aiosqlite)
В современных веб-приложениях работа с базой данных часто становится узким местом производительности. Традиционные синхронные драйверы блокируют выполнение кода до завершения запроса к БД, что приводит к простою потоков и неэффективному использованию ресурсов.

Асинхронные драйверы решают эту проблему, позволяя приложению обрабатывать другие запросы пока текущий ожидает ответа от базы данных. Это особенно критично для приложений с высокой нагрузкой, где тысячи пользователей одновременно работают с системой.

### **Преимущества асинхронных драйверов**

- Высокая производительность - один поток обрабатывает множество запросов 
- Масштабируемость - тысячи одновременных подключений к БД 
- Эффективное использование ресурсов - нет простоев в ожидании I/O

### 📊 Основные асинхронные драйверы

**PostgreSQL - asyncpg**

asyncpg считается одним из самых производительных драйверов для PostgreSQL. Он разработан специально для асинхронной работы и показывает лучшие результаты по сравнению с другими драйверами.

    import asyncpg
    
    async def get_db():
        return await asyncpg.connect("postgresql://user:pass@localhost/db")

Ключевые особенности:

- Поддержка подготовленных выражений 
- Встроенный пул соединений 
- Прямой парсинг PostgreSQL протокола 
- Высокая скорость работы

**SQLite - aiosqlite**

aiosqlite предоставляет асинхронную обертку вокруг стандартной библиотеки SQLite. Хотя SQLite не является полноценной серверной СУБД, он отлично подходит для небольших приложений и прототипирования.

    import aiosqlite
    
    async def get_db():
        async with aiosqlite.connect("app.db") as db:
            yield db

Преимущества:

- Простота настройки 
- Не требует отдельного сервера БД 
- Полная совместимость с SQLite 
- Идеален для разработки и тестирования