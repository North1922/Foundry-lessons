# Установка и настройка Foundry 

* Настроил Wsl и разобрался в навигации в нём.

* в WSL установил Foundry
```bash
# Download foundry installer `foundryup`
curl -L https://foundry.paradigm.xyz | bash
# Install forge, cast, anvil, chisel
foundryup
```

* Создали директорию в которой будет находиться проект :
```bash
mkdir foundry-f23
cd foundry-f23
```

* Далее в папке foundry-f23 создали папку проекта и перешли в неё:

```bash
mkdir foundry-simple-storage-f23
cd foundry-simple-storage-f23
```

* Создали в папке foundry-simple-storage-f23 проект Foundry: 
```bash
forge init
#эта команда предполагает что папка будет пуста , #если в папке имеются файлы то используем команду #forge init --force
```

* Создали имя и адрес электронной почты при помощи команд :
```bash
git config --global user.email "yourEmail@provider.com"
git config --global user.name "yourUsername"
# эти данные отображаются при коммитах
```


* Настроил взаисодействие с репозиторием на GitHub:
1. Создал на GitHub репозиторий

2. Сгенерировал SSH-ключ в wsl:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# Email в кавычках — это комментарий (метка) для идентификации ключа. Он выполняет несколько функций:

# Идентификация владельца: Когда вы смотрите на свой публичный ключ, видите комментарий с email

# Организация: Если у вас несколько ключей (личный, рабочий), можно различать их по email

# Конвенция GitHub: GitHub рекомендует использовать email, привязанный к аккаунту

# Обратная совместимость: Исторически сложилось, что в RSA-ключах часто указывали email
```

3. "Подружил" удалённый репозиторий с локальным
```bash
# Инициализировал git
git init
# Проверил какие удалённые репозитории подключены
git remote -v
# Добавил удалённый репозиторий (origin) с SSH URL
git remote add origin git@github.com:username/your-repo-name.git
#проверил название локальной ветки  
git branch
# запушил на GitHub
git push -u origin main #"-u" флаг которыйсвязывает удалённую ветку origin с локальной main
```
