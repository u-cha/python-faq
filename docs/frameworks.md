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

###### Пример использования:
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

## **Сравнительная таблица**

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

### 1. **Сериализаторы**
- **Назначение**: Преобразование данных между форматами (Python объекты ↔ JSON/XML)
- **Аналогия**: Как Django Forms, но для API
- **Функции**: Валидация данных, преобразование типов, контроль доступа к полям

### 2. **ViewSets и ViewClasses**
- **ViewSets**: Автоматическое создание CRUD операций для моделей
- **APIView**: Более гибкий контроль над отдельными endpoint'ами
- **Generic Views**: Готовые шаблоны для стандартных операций

### 3. **Роутеры**
- **Назначение**: Автоматическая генерация URL маршрутов для ViewSets
- **Преимущество**: Сокращает рутинный код маршрутизации

### 4. **Аутентификация и Права доступа**
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

#### **✅ Идеально для:**
- Backend для мобильных приложений
- API для SPA (React, Vue, Angular)
- Микросервисная архитектура
- Публичные API для сторонних разработчиков

#### **❌ Менее подходит для:**
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

### **APIView** — Базовый уровень (ручное управление)

#### Концепция:
- **Один класс = Один endpoint**
- **Явное определение** HTTP-методов
- **Полный контроль** над логикой

#### Структура:
```python
class UserAPI(APIView):
    def get(self, request): pass    # GET /users/
    def post(self, request): pass   # POST /users/
    
class UserDetailAPI(APIView):
    def get(self, request, pk): pass     # GET /users/1/
    def put(self, request, pk): pass     # PUT /users/1/
    def delete(self, request, pk): pass  # DELETE /users/1/
```

#### Когда использовать:
- **Нестандартная логика** обработки запросов
- **Специфичные endpoint'ы** (не CRUD)
- **Тонкая настройка** каждого метода
- **Микросервисы** с небольшой функциональностью

### **ViewSet** — Высокий уровень (автоматизация)

#### Концепция:
- **Один класс = Все операции с ресурсом**
- **Автоматическая** маршрутизация
- **Стандартные** CRUD операции

#### Структура:
```python
class UserViewSet(ViewSet):
    def list(self, request): pass      # GET /users/
    def create(self, request): pass    # POST /users/
    def retrieve(self, request, pk): pass   # GET /users/1/
    def update(self, request, pk): pass     # PUT /users/1/
    def destroy(self, request, pk): pass    # DELETE /users/1/
```

#### Когда использовать:
- **Стандартные CRUD** операции
- **Быстрая разработка** API
- **Ресурс-ориентированная** архитектура
- **Админ-панели** и бэкенды

### **ModelViewSet** — Максимальная автоматизация

#### Концепция:
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
tbd

---
## Как оптимизировать QuerySet? (select_related, prefetch_related)
tbd

---
## Как реализована связь ManyToMany на уровне БД?
tbd

---
## Как работают миграции в Django?
tbd

---
## Как работают сигналы (signals)? Когда их использовать?
tbd

---
## Какие преимущества дает использование async в FastAPI?
tbd

---
## Dependency Injection в FastAPI
tbd

---
## Валидация данных (Pydantic модели)
tbd

---
## Работа с асинхронными драйверами БД (asyncpg, aiosqlite)
