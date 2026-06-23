## Отчет по практической работе №1

[Ссылка на Overleaf](https://ru.overleaf.com/read/xhykjpdbfdzd#f5d54d)

# Установка корпоративной вики и системы управления задачами

---

### 1. Введение

**Цель работы:** Получить практические навыки по развертыванию, настройке и интеграции корпоративных инструментов для управления знаниями (Wiki) и проектами (Task Tracker) на локальном сервере.

**Выбранные системы:**
- **Корпоративная вики:** MediaWiki
- **Система управления задачами:** Redmine

**Обоснование выбора:**
На основе проведенного анализа (реферат) была выбрана связка MediaWiki + Redmine, так как:
- MediaWiki — наиболее мощная и масштабируемая вики-система с огромным сообществом
- Redmine — гибкая система управления проектами с открытым исходным кодом
- Существуют плагины для интеграции этих систем

---

### 2. Процесс установки

#### 2.1. Установка Docker Desktop

**Скачивание и установка:**
1. Перешел на официальный сайт Docker: https://www.docker.com/products/docker-desktop/
2. Скачал установщик Docker Desktop для Windows
3. Запустил установку и следовал инструкциям

**Проверка установки:**
```powershell
docker --version
# Результат: Docker version 26.0.0
docker-compose --version
# Результат: Docker Compose version v2.26.0
```

---

#### 2.2. Создание проекта

**Создание рабочей директории:**
```powershell
mkdir C:\Users\melni\Desktop\BY3\3kypc\SystemAndTechnologyDocs\practice1
cd C:\Users\melni\Desktop\BY3\3kypc\SystemAndTechnologyDocs\practice1
```

**Создание файла docker-compose.yml:**
Создан файл `docker-compose.yml` со следующим содержимым:

```yaml
services:
  mediawiki:
    image: mediawiki:1.39
    container_name: mediawiki
    restart: always
    ports:
      - "8080:80"
    volumes:
      - ./mediawiki-data:/var/www/html
    depends_on:
      - db

  redmine:
    image: redmine:latest
    container_name: redmine
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - ./redmine-data:/usr/src/redmine/files
    environment:
      - REDMINE_DB_MYSQL=db
      - REDMINE_DB_DATABASE=redmine
      - REDMINE_DB_USERNAME=redmine
      - REDMINE_DB_PASSWORD=redmine123
    depends_on:
      - db

  db:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - "3307:3306"
    volumes:
      - ./mysql-data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root123
      - MYSQL_DATABASE=mediawiki
      - MYSQL_USER=wikiuser
      - MYSQL_PASSWORD=wiki123
```

**Пояснение конфигурации:**
- **mediawiki:** Образ MediaWiki версии 1.39, порт 8080
- **redmine:** Последняя версия Redmine, порт 3000
- **db:** MySQL 5.7, порт 3307

<img width="1389" height="958" alt="image" src="https://github.com/user-attachments/assets/886a5fee-4342-4ef1-bf02-3c9144a73da6" />


---

#### 2.3. Запуск контейнеров

**Запуск всех сервисов:**
```powershell
docker-compose up -d
```

**Результат выполнения:**
```
[+] Running 3/3
✓ Container mysql      Started
✓ Container mediawiki  Started
✓ Container redmine    Started
```

**Проверка статуса контейнеров:**
```powershell
docker ps
```

**Результат:**
```
CONTAINER ID   IMAGE               STATUS         PORTS
abc123         mediawiki:1.39      Up 2 minutes   0.0.0.0:8080->80/tcp
def456         redmine:latest      Up 2 minutes   0.0.0.0:3000->3000/tcp
ghi789         mysql:5.7           Up 2 minutes   0.0.0.0:3307->3306/tcp
```

<img width="1175" height="167" alt="image" src="https://github.com/user-attachments/assets/659429cb-cc2c-44c6-af95-154e0cedf163" />

---

#### 2.4. Настройка MediaWiki

**Копирование файлов MediaWiki:**
```powershell
docker create --name mediawiki-temp mediawiki:1.39
docker cp mediawiki-temp:/var/www/html/. ./mediawiki-data/
docker rm mediawiki-temp
```

**Создание базы данных:**
```powershell
docker exec -it mysql mysql -u root -proot123 -e "CREATE DATABASE IF NOT EXISTS mediawiki; GRANT ALL PRIVILEGES ON mediawiki.* TO 'wikiuser'@'%' IDENTIFIED BY 'wiki123'; FLUSH PRIVILEGES;"
```

**Установка через веб-интерфейс:**
1. Открыл в браузере: http://localhost:8080
2. Нажал "Set up the wiki"
3. Ввел данные подключения к БД:
   - Database type: MySQL
   - Database host: db
   - Database name: mediawiki
   - Database username: wikiuser
   - Database password: wiki123
4. Заполнил настройки вики:
   - Wiki name: Корпоративная вики
   - Admin username: Admin
   - Admin password: [установлен]
   - Admin email: admin@example.com
5. Скачал файл `LocalSettings.php` и поместил в папку `mediawiki-data/`
6. Обновил страницу


---

#### 2.5. Настройка Redmine

**Регистрация администратора:**
1. Открыл http://localhost:3000
2. Нажал "Register"
3. Ввел данные:
   - Login: Admin
   - Password: [установлен]
   - First name: Admin
   - Last name: Admin
   - Email: admin@example.com
4. Нажал "Submit"

**Создание проекта:**
1. Нажал "Projects" → "Create project"
2. Ввел:
   - Name: Проект Альфа
   - Identifier: alpha-project
3. Нажал "Create"


---

### 3. Функциональный анализ

#### 3.1. MediaWiki

**Основные возможности:**
1. **Создание страниц** — редактирование в вики-разметке
2. **Категоризация** — группировка страниц по темам
3. **Внутренние ссылки** — навигация между страницами
4. **История версий** — просмотр и восстановление изменений
5. **Управление доступом** — настройка прав для разных групп

**Создание страниц:**
- Создана главная страница с описанием проекта
- Создана страница "Архитектура сервиса"
- Создана страница "Руководство по развертыванию"


**Пример синтаксиса MediaWiki:**
```wikitext
= Архитектура сервиса =

== Общая структура ==
Сервис состоит из следующих компонентов:
* Frontend (React)
* Backend (Node.js)
* База данных (PostgreSQL)

== Диаграмма ==
[Схема архитектуры]

== Связанные задачи ==
http://localhost:3000/issues/1
```

---

#### 3.2. Redmine

**Основные возможности:**
1. **Управление проектами** — создание и настройка проектов
2. **Трекинг задач** — создание, назначение, отслеживание статуса
3. **Диаграмма Ганта** — визуализация сроков
4. **Учет времени** — регистрация затраченного времени
5. **Система ролей** — настройка прав доступа

**Созданные задачи:**
| № | Название | Описание | Связь с Wiki |
|---|----------|----------|--------------|
| 1 | Разработать архитектуру | Создать архитектуру сервиса | http://localhost:8080/Архитектура_сервиса |
| 2 | Написать ТЗ | Подготовить техническое задание | http://localhost:8080/Техническое_задание |
| 3 | Развернуть стенд | Подготовить тестовый стенд | http://localhost:8080/Руководство_по_развертыванию |



---

### 4. Интеграция

#### 4.1. Описание интеграции

Интеграция между MediaWiki и Redmine реализована через **перекрестные ссылки**:

**Из Wiki в Redmine:**
- На страницах Wiki добавлены ссылки на соответствующие задачи в Redmine

**Из Redmine в Wiki:**
- В описании задач добавлены ссылки на соответствующие страницы Wiki

**Пример на странице Wiki:**
```wikitext
= Проект Альфа =

== Связанные задачи в Redmine ==
* [http://localhost:3000/issues/1 Задача #1: Разработать архитектуру]
* [http://localhost:3000/issues/2 Задача #2: Написать ТЗ]
* [http://localhost:3000/issues/3 Задача #3: Развернуть тестовый стенд]
```

**Пример в задаче Redmine:**
```
Тема: Разработать архитектуру сервиса

Описание:
Необходимо разработать архитектуру сервиса.

Требования описаны в Wiki:
http://localhost:8080/Архитектура_сервиса
```

---

#### 4.2. Преимущества интеграции

1. **Единое информационное пространство**
   - Все данные доступны из одной точки
   - Разработчики видят полный контекст задачи

2. **Улучшенная коммуникация**
   - Автоматическая связь между задачами и документацией
   - Сокращение времени на поиск информации

3. **Повышение прозрачности**
   - Статус задач виден в контексте документации
   - Требования легко отслеживаются

4. **Упрощение документирования**
   - Документация всегда связана с задачами
   - Актуальность информации поддерживается

---

#### 4.3. Сценарий использования

**Сквозной сценарий:**
1. Аналитик создает страницу в Wiki с требованиями к функционалу
2. Менеджер создает задачу в Redmine и ссылается на страницу Wiki
3. Разработчик читает задачу и переходит по ссылке на Wiki
4. Разработчик видит полный контекст и начинает работу
5. После завершения задачи, статус обновляется в Redmine



---

### 5. Заключение

#### 5.1. Выводы

**MediaWiki:**
- ✅ Мощная и масштабируемая система
- ✅ Огромное сообщество и документация
- ✅ Гибкая настройка прав доступа
- ❌ Сложность установки для новичков

**Redmine:**
- ✅ Полнофункциональная система управления проектами
- ✅ Поддержка диаграмм Ганта
- ✅ Гибкая система плагинов
- ❌ Устаревший интерфейс

**Интеграция:**
- ✅ Возможность связывать задачи и документацию
- ✅ Единое информационное пространство
- ❌ Отсутствие автоматической синхронизации

---

#### 5.2. Рекомендации

1. **Для крупных организаций:** MediaWiki + Redmine — оптимальный выбор
2. **Для Agile-команд:** Рассмотреть Wiki.js + Taiga
3. **Для небольших проектов:** Использовать OpenProject как единое решение
4. **Для интеграции:** Использовать плагин Redmine MediaWiki Formatter

---

### 6. Ссылки на использованные источники

1. **Официальная документация MediaWiki:**
   https://www.mediawiki.org/wiki/Manual:Installation_guide

2. **Официальная документация Redmine:**
   https://www.redmine.org/projects/redmine/wiki/Installation

3. **Документация Docker:**
   https://docs.docker.com/

4. **Плагин Redmine MediaWiki Formatter:**
   https://github.com/pgengler/redmine_mediawiki_formatter

5. **Учебные материалы:**
   - Практическая работа №1 - Корпоративные вики для технической документации
   - Практическая работа №1 - Анализ существующих решений

---

### Приложение: Список скриншотов

| № | Название | Описание |
|---|----------|----------|
| 1 | Docker версии | Терминал с выводом версий Docker |
| 2 | docker-compose.yml | Содержимое файла конфигурации |
| 3 | docker ps | Статус запущенных контейнеров |
| 4 | Установка MediaWiki | Страница настройки БД |
| 5 | Главная MediaWiki | Главная страница вики |
| 6 | Вход Redmine | Страница входа в Redmine |
| 7 | Главная Redmine | Главная страница Redmine |
| 8 | Страница Wiki | Пример страницы с контентом |
| 9 | Список задач | Задачи в Redmine |
| 10 | Задача со ссылкой | Задача с ссылкой на Wiki |
| 11 | Wiki со ссылкой | Страница Wiki с ссылкой на задачу |

