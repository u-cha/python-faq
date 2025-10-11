## PIP

**PIP** (Pip Installs Packages) — это стандартная система управления пакетами в Python. Это инструмент командной строки, который позволяет устанавливать, удалять и управлять дополнительными библиотеками и зависимостями, не входящими в стандартную библиотеку Python.
Он взаимодействует с Python Package Index (PyPI), чтобы находить, загружать и устанавливать пакеты.

### **🛠 Основные возможности**

**Установка пакетов**
```bash
# Установка последней версии
pip install requests

# Установка конкретной версии
pip install django==4.2.0

# Установка из файла требований
pip install -r requirements.txt
```

**Поиск и информация**
```bash
# Поиск пакетов в репозитории
pip search "web framework"

# Показать информацию о пакете
pip show django

# Список установленных пакетов
pip list
```

**Управление зависимостями**
```bash
# Генерация файла зависимостей
pip freeze > requirements.txt

# Установка из файла зависимостей
pip install -r requirements.txt
```

**Обновление и удаление**
```bash
# Обновление пакета
pip install --upgrade requests

# Удаление пакета
pip uninstall django

# Проверка устаревших пакетов
pip list --outdated
```

**📁 Файл requirements.txt**

Файл `requirements.txt` — это стандартный способ хранения списка зависимостей проекта:

```txt
# requirements.txt
Django==4.2.0
requests>=2.25.0
numpy<1.22.0
celery[redis]
```

**Генерирация список всех установленных пакетов**
    
    pip freeze > requirements.txt

**Установка всех зависимостей**
    
    pip install -r requirements.txt

**Установка в режиме разработки**
    
    pip install -r requirements/dev.txt

**Обновить все пакеты до последних версий**
    
    pip install --upgrade -r requirements.txt

### **⚡ Ключевые особенности**

**Работа с репозиторием PyPI**

PIP по умолчанию работает с **PyPI** (Python Package Index) — официальным репозиторием пакетов Python, содержащим тысячи библиотек.

**Разрешение зависимостей**

PIP автоматически определяет и устанавливает все необходимые зависимости пакетов, обеспечивая корректную работу устанавливаемых библиотек.

**Виртуальные окружения**

PIP часто используется вместе с `venv` для создания изолированных сред, где зависимости проекта не конфликтуют с системными пакетами.

### **🚀 Практическое использование**

### **Типичный рабочий процесс**
```bash
# Создание виртуального окружения
python -m venv myproject_env

# Активация окружения
source myproject_env/bin/activate  # Linux/Mac
myproject_env\Scripts\activate     # Windows

# Установка зависимостей проекта
pip install -r requirements.txt

# Добавление нового пакета
pip install fastapi
pip freeze > requirements.txt
```

### **Совместимость с системами**
- Работает на всех основных ОС (Windows, Linux, macOS)
- Поддерживает все версии Python (начиная с Python 3.4)
- Интегрируется с инструментами CI/CD

### **Безопасность**
- Проверка цифровых подписей пакетов
- Поддержка HTTPS для загрузки
- Возможность использования приватных репозиториев

---
## Poetry

**Poetry** — это современный инструмент для управления зависимостями и упаковки Python-проектов. Он объединяет функциональность pip, virtualenv и setuptools в одной удобной системе.

**🚀 Ключевые преимущества**

1. **Единый инструмент**
     - Управление зависимостями
     - Виртуальные окружения
     - Сборка пакетов
     - Публикация в PyPI

2. **Надежность**
    - Детерминированная установка — одинаковые версии на всех окружениях 
    - Автоматическое разрешение зависимостей — интеллектуальный алгоритм 
    - Lock-файл — гарантия воспроизводимости

### **🛠 Основные команды**

#### **Инициализация проекта**
```bash
# Создание нового проекта
poetry new my-project

# Инициализация в существующей директории
poetry init
```

#### **Управление зависимостями**
```bash
# Добавление пакета
poetry add requests

# Добавление с указанием версии
poetry add "django>=4.2.0"

# Добавление пакета для разработки
poetry add --dev pytest

# Удаление пакета
poetry remove package-name
```

#### **Установка и запуск**
```bash
# Установка всех зависимостей
poetry install

# Установка без зависимостей разработки
poetry install --no-dev

# Запуск скрипта в виртуальном окружении
poetry run python script.py

# Активация виртуального окружения
poetry shell
```

### **📁 Структура проекта**

**pyproject.toml**

Основной файл конфигурации Poetry:
```toml
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = "A beautiful project"
authors = ["Your Name <email@example.com>"]

[tool.poetry.dependencies]
python = "^3.8"
requests = "^2.28.0"
django = "^4.2.0"

```

**poetry.lock**

Это автоматически генерируемый файл, который фиксирует точные версии всех зависимостей вашего проекта. 
Это гарантирует, что все разработчики и окружения используют одинаковые версии пакетов.
Он содержит хеши  пакетов для проверки целостности проекта (должен сходится суммарный итоговый хэш с суммой хешей всех пакетов).

## **🎯 Основные возможности**

### **Управление версиями Python**
```toml
[tool.poetry.dependencies]
python = ">=3.8,<3.12"
```

### **Группы зависимостей**
```toml
# Основные зависимости
[tool.poetry.dependencies]
fastapi = "^0.104.0"

# Зависимости для разработки
[tool.poetry.group.dev.dependencies]
pytest = "^7.0.0"
black = "^23.0.0"

# Дополнительные группы
[tool.poetry.group.docs.dependencies]
sphinx = "^7.0.0"
```

### **Экстра-зависимости**
```toml
[tool.poetry.dependencies]
celery = { version = "^5.3.0", extras = ["redis", "aws"] }
```

### **Рабочий процесс разработки**
```bash
# 1. Создать проект
poetry new awesome-api
cd awesome-api

# 2. Добавить зависимости
poetry add fastapi uvicorn
poetry add --dev pytest black

# 3. Активировать окружение
poetry shell

# 4. Установить все зависимости
poetry install

# 5. Запустить приложение
poetry run uvicorn main:app --reload
```

### **Миграция с requirements.txt**
```bash
# Создать pyproject.toml на основе requirements.txt
poetry init --no-interaction
poetry add $(cat requirements.txt)
```

### **🏗 Продвинутые возможности**

#### **Скрипты и команды**
```toml
[tool.poetry.scripts]
my-app = "my_package.main:app"

[tool.poetry.plugins."console_scripts"]
my-command = "my_package.cli:main"
```

#### **Публикация пакетов**
```bash
# Сборка пакета
poetry build

# Публикация в PyPI
poetry publish

# Публикация в приватный репозиторий
poetry publish -r private-repo
```

