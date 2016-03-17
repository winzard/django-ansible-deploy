django-ansible-deploy
====================

Основан на репозитории [ansible-django-stack](https://github.com/jcalazan/ansible-django-stack)

Ansible Playbook предназначен для быстрого развертывания Djngo-проекта на чистом сервере. Сценарий устанавливает и настраивает пакеты и приложения, обычно используемые в production проектов на Django:

- Nginx
- uWSGI (Emperor)
- PostgreSQL or MySQL
- Supervisor
- Virtualenv
- Memcached
- Celery
- RabbitMQ
- Backupninja
- Glances
- Xapian

Настройки для ролей хранятся в ```roles/role_name/vars/main.yml```. Общие настройки окружения в директории ```env_vars```.

**Протестировано:** Ubuntu 12.04 LTS x64, Ubuntu 14.04 LTS x64 на vscale.io

## Быстро посмотреть

С помощью Vagrant и Virtualbox можно быстро развернуть playbook и оценить, что там к чему.

### Требования

- [Ansible](http://docs.ansible.com/intro_installation.html)
- [Vagrant](http://www.vagrantup.com/downloads.html)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
 
Настройки для отдельных проектов могут располагаться в директории inventories. Внутри нее следует создать папку с именем проекта, а в ней файл hosts для указания серверов и папку group_vars для настроек.
Пример настроек находится в файле group_vars/all.yml директории ```inventories/sample```. Названия файлов в group_vars должны соответствовать группам серверов, определенных в hosts.

Структура вашего проекта Django должна быть каноничной:

```
myproject
├── manage.py
├── myapp
│   ├── apps
│   │   └── __init__.py
│   ├── __init__.py
│   ├── settings
│   │   ├── base.py
│   │   ├── __init__.py
│   │   ├── local.py
│   │   └── production.py
│   ├── templates
│   │   ├── 403.html
│   │   ├── 404.html
│   │   ├── 500.html
│   │   └── base.html
│   ├── urls.py
│   └── wsgi.py
├── README.md
└── requirements.txt
```

Если вам случается устанавливать пакеты не из pip, а из своих секретных мест, пропишите их в файле '''requirements.extra''' вашего проекта.
Например:
'''
git+ssh://git@github.com/winzard/django-simple-yandex-map.git@master
git+ssh://git@github.com/winzard/django-constance.git
'''

Если вам нужно поставить дополнительные системные пакеты, укажите названия пакетов в файле внутри репозитория (по одному на строчку), и укажите название файла в переменной '''extra_apt''' в настройках.

**Ключи доступа к приватным репозиториям**
 Если в папке roles/deploy/files/ находятся публичный public.key и секретный secret.key ключи доступа, эти ключи будут скопированы в пользовательскую папку на сервере. Такие ключи нужны для доступа к приватным репозиториям git. Публичный ключ нужно прописать в настройках доступа по ssh нужных репозиториев (Github, Bitbucket, Gitlab и т.п.). Не кладите секретный ключ в репозиторий ansible!

Чтобы развернуть сервер через Vagrant, выполните

```
vagrant up
```

Спустя некоторое время сервер будет доступен по адресу, указанному в переменной '''domain_name''' настроек.

### Что еще можно попробовать в Vagrant

**Достучаться по SSH**

```
vagrant ssh
```

**Перезапустить установку, если что-то изменилось или отвалилось**

```
vagrant provision
```

**Перезагрузить сервер**

```
vagrant reload
```

**Выключить сервер**

```
vagrant halt
```

**Уничтожить сервер**

```
vagrant destroy
```

## Запуск Ansible Playbook на реальном сервере

Допустим, вы хотите развернуть проект kawabanga на сервер kawabanga.org. В папке inventories создайте подпапку kawabanga, а в ней подпапку group_vars. Скопируйте в group_vars файл с настройками из '''sample/group_vars/all.yml'''. Отредактируйте настройки.
Создайте в папке inventories/kawabanga файл hosts. В нем пропишите

```
[all]
kawabanga.org

```

Запустите сценарий:

```
ansible-playbook -i inventories/kawabanga/hosts production.yml
```

Если пользователь, которым вы заходите на сервер, не состоит в sudoers по соображениям безопасности, используйте сценарий nosudoers.yml

```
ansible-playbook -i inventories/kawabanga/hosts nosudoers.yml --ask-become-pass --ask-pass
```


## Полезные ссылки, которые уже поздно читать

- [Ansible - Getting Started](http://docs.ansible.com/intro_getting_started.html)
- [Ansible - Best Practices](http://docs.ansible.com/playbooks_best_practices.html)
- [Setting up Django with Nginx, Gunicorn, virtualenv, supervisor and PostgreSQL](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/)
- [How to deploy encrypted copies of your SSL keys and other files with Ansible and OpenSSL](http://www.calazan.com/how-to-deploy-encrypted-copies-of-your-ssl-keys-and-other-files-with-ansible-and-openssl/)
