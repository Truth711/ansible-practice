# xpaste

## Оглавление
- [О проекте](#о-проекте)
- [Проверка работоспособности](#проверка-работоспособности)
- [Запуск проекта](#запуск-проекта)
- [Описание задачи](#описание-задачи)

## О проекте
**xpaste** — это моя реализация решения учебного практикума по Ansible. В рамках проекта была развернута инфраструктура для деплоя приложения **xpaste**, включая настройку сервера, установку зависимостей, конфигурацию базы данных и балансировщика нагрузки.

## Проверка работоспособности
Проект тестировался в следующем окружении:

- **Операционная система**: Windows 10 x64 Pro
- **Окружение**:
  - Vagrant 2.4.0
  - VirtualBox 6.1.48

## Запуск проекта

1. **Клонируйте репозиторий:**
   ```sh
   git clone https://github.com/Truth711/xpaste.git
   ```
2. **Перейдите в папку проекта:**
   ```sh
   cd ./xpaste
   ```
3. **Запустите виртуальные машины:**
   ```sh
   vagrant up
   ```
4. **Подключитесь к контрольному узлу:**
   ```sh
   vagrant ssh controlnode
   ```
5. **(Только для Windows)** Копируем папку `ansible`, именуем её `internal_ansible` и даем необходимые права:
   ```sh
   cp -R ansible internal_ansible
   chmod -R 775 internal_ansible
   ```
6. **Перейдите в папку Ansible:**
   ```sh
   cd internal_ansible/
   ```
7. **Скачайте зависимости:**
   ```sh
   ansible-galaxy install -r requirements.yml
   ```
8. **Запустите плейбук:**
   ```sh
   ansible-playbook playbook.yml -e "gitlabuser=<your_gitlab_login>" \
                                  -e "gitlabpassword=<your_gitlab_password>" \
                                  -e "xpaste_secret_key=<your_secret_key>" \
                                  -e "xpaste_db_password=<your_db_password>"
   ```

---

## Описание задачи

### Задание:
Подготовить сервер на **CentOS 7** для работы приложения **xpaste**.

### Требования к развертыванию
- Приложение должно запускаться как сервис в **systemd**.
- Переменные передаются через окружение в systemd unit.
- Настроен **PostgreSQL** в качестве базы данных.
- Используется **Nginx** как балансировщик нагрузки.
- Весь деплой автоматизирован с помощью **Ansible**.

### Переменные окружения приложения:

- `SECRET_KEY_BASE` - любая строка
- `RAILS_ENV=production`
- `RAILS_LOG_TO_STDOUT=1`
- `DB_HOST=127.0.0.1`
- `DB_PORT=5432`
- `DB_NAME` - имя базы данных
- `DB_USER` - пользователь базы данных
- `DB_PASSWORD` - пароль базы данных

### Требуемые зависимости
Приложению необходимы следующие пакеты:
- `build-base`
- `libxml2-dev`

### Деплой приложения
1. **Установить зависимости:**
   ```sh
   bundle config build.nokogiri --use-system-libraries
   bundle install --clean --no-cache --without development
   ```
2. **Выполнить миграции и запустить сервер:**
   ```sh
   bundle exec rake db:migrate && \
   bundle exec puma -b unix:///var/run/puma.sock -e $RAILS_ENV config.ru
   ```
