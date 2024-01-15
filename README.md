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
