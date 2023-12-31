---
tags: Git 
---
Файл `.gitignore` - это текстовый файл, который используется в системе управления версиями Git для указания файлов и директорий, которые должны быть проигнорированы при отслеживании изменений. То есть, файлы, указанные в `.gitignore`, не будут автоматически добавляться в репозиторий и не будут отображаться как изменения.

Как создать и настроить `.gitignore`:

1. **Создание файла**: В корне вашего проекта создайте файл с именем `.gitignore`. Обратите внимание, что имя файла начинается с точки, что делает его скрытым файлом.
    
2. **Добавление правил**: Внутри файла `.gitignore` добавьте строки, каждая из которых описывает шаблон файлов или директорий, которые вы хотите проигнорировать. Правила могут быть как специфичными (например, `filename.txt`), так и использующими символы подстановки (например, `*.tmp` для всех файлов с расширением `.tmp`).
    
3. **Комментарии**: Вы можете добавлять комментарии, начиная строку с символа `#`. Они служат для пояснения правил и делают файл более понятным.
    
4. **Директории**: Чтобы игнорировать директорию, просто добавьте ее имя, заканчивая слэшем (`/`).
    
5. **Применение**: После добавления правил в `.gitignore`, Git автоматически будет игнорировать файлы и директории, соответствующие этим правилам при добавлении их в репозиторий.
    
6. **Учет изменений**: Если вы уже начали отслеживать файлы, которые теперь хотите игнорировать, вам может потребоваться удалить их из индекса Git с помощью команды `git rm --cached filename` (где `filename` - имя файла, который нужно удалить из индекса).
    

После настройки `.gitignore` файлы и директории, указанные в нем, не будут попадать в состав коммитов и не будут отображаться как изменения в репозитории.

## Пример

Пример простого файла `.gitignore`:

```
# Комментарий

# Игнорирование временных файлов
*.tmp
*.log

# Игнорирование директории node_modules
node_modules/

# Игнорирование конфигурационных файлов
config.ini

# Игнорирование файлов в директории data
data/*.csv

```