# xpaste

## Проверка работоспособности:

Тестирование проводилось на:

 - OS: Windows 10 x64 Pro
 - Окружение: 
	 - Vagrant 2.4.0
	 - VirtualBox 6.1.48

## Запуск:

 1. Склонируйте репозиторий: `git clone https://github.com/Truth711/xpaste.git`
 2. Перейдите в папку репозитория: `cd ./xpaste`
 3. Запустите виртуальные машины:`vagrant up`
 4. Подключаемся на контрольную машину командой: `vagrant ssh controlnode`
 5. * Для Windows! Копируем папку ansible смонтированную от windows и именуем в internal_ansible `cp -R ansible internal_ansible` и даём права 775 на всю папку, иначе не будет работать файл ansible.cfg `chmod -R 775 internal_ansible`
 6. Переходим в папку ansible (или internal_ansible для windows): `cd internal_ansible/`
 7. Скачиваем galaxy роли: `ansible-galaxy install -r requirements.yml`
 8. Запускаем наш плейбук: `ansible-playbook playbook.yml -e "gitlabuser=<your_gitlab_login>" -e "gitlabpassword=<your_gitlab_password>" -e "xpaste_secret_key=<your_secret_key>" -e "xpaste_db_password=<your_db_password>"`

## Практикум по Ansible:
Цель: Закрепление материала полученного в курсе Ansible, получение практического опыта написания и использования ролей.

Результат: Работающая инфраструктура для приложения. Репозиторий на Github.com с ролями и плейбуками Ansible.

Задание: Необходимо подготовить сервер для работы приложения на основе ОС Centos 7.

Само приложение доступно в репозитории https://gitlab.slurm.io/edu/xpaste_practicum
Приложение написано на Ruby, должно запускаться как сервис в systemd (сервис файл для приложения нужно подготовить самостоятельно).
Настройки для приложения должны передаваться как переменные окружения в сервис файле.
Набор переменных необходимых приложения для работы:
SECRET_KEY_BASE - любая строка
RAILS_ENV=production
RAILS_LOG_TO_STDOUT=1
DB_HOST - адрес хоста postgresql (127.0.0.1)
DB_PORT - порт postgresql (5432)
DB_NAME - имя базы данных
DB_USER - пользователь в базе данных
DB_PASSWORD - пароль для базы данных
Для деплоя приложения на сервер нужно подготовить отдельную Ansible роль, которая должна включать установку зависимостей для приложения, деплой сервис файла из шаблона, деплой кода самого приложения, запуск приложения.
Для установки зависимостей приложения требуются следующие пакеты:
build-base
libxml2-dev
Для установки самих зависимостей нужно выполнить:
bundle config build.nokogiri --use-system-libraries
bundle install --clean --no-cache --without development
Для запуска приложения в сервис файле нужно выполнять команды:
bundle exec rake db:migrate && \
bundle exec puma -b unix:///var/run/puma.sock -e $RAILS_ENV config.ru
Приложению для работы необходима БД PostgreSQL. Для разворачивания базы данных нужно подготовить собственную ansible роль. Роль должна включать в себя установку и настройку Postgres, а также создание пользователя с паролем и базы данных для приложения.
Перед приложением необходимо установить балансировщик Nginx. Роль для балансировщика нужно взять готовую из набора Galaxy. Конфигурация для Nginx находится в файле config/nginx.conf. Самостоятельно исправьте локацию с обращением на бэкенд, для правильной работы приложения.
 

### Практические рекомендации: 

Для выполнения задания достаточно одной виртуальной машины. Запустите все службы (приложение, Nginx и PostgreSQL) на ней.