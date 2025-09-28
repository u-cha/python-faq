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
tbd

---


## Какой паттерн в основе Django?
tbd

---
## Какой путь проходит запрос в Django
tbd

---
## FastAPI vs Django: когда что использовать?
tbd

---
## Что такое Django REST Framework (DRF)?
tbd

---
## Разница между APIView и ViewSet?
tbd

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
