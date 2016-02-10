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

Настройки для ролей хранятся в ```roles/role_name/vars/main.yml```. Общие настройки окружения в директории ```env_vars```.

**Протестировано:** Ubuntu 12.04 LTS x64, Ubuntu 14.04 LTS x64 на vscale.io

## Быстро посмотреть

С помощью Vagrant и Virtualbox можно быстро развернуть playbook и оценить, что там к чему.

### Требования

- [Ansible](http://docs.ansible.com/intro_installation.html)
- [Vagrant](http://www.vagrantup.com/downloads.html)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

Пример настроек находится в файле base.sample.yml директории env_vars. Скопируйте его в новый файл base.yml

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


**Ключи доступа к серверу**
В папке deploy/vars/ находятся публичный и зашифрованный секретный ключи доступа. Эти ключи нужны для доступа к приватным репозиториям git. Приватный ключ расшифровывается и копируется на разворачиваемый сервер, а публичный нужно прописать в настройках доступа по ключу нужных репозиториев. Ключ шифруется в Ansible Vault командой.

'''
ansible-vault encrypt secret_key.yml
'''

Чтобы ключ был расшифрован, Ansible Playbook нужно выполнять с опцией *--ask-vault-pass* или прописать ключ в файл и запускать с опцией *--vault-password-file*

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

Сначала создайте файл inventory file:

```
# development

[all:vars]
env=dev

[webservers]
webserver1.example.com
webserver2.example.com

[dbservers]
dbserver1.example.com
```

Затем создайте playbook для сервера. См. для примера [webservers.yml](webservers.yml).

Запустите сценарий:

```
ansible-playbook -i development webservers.yml --ask-vault-pass
```

## Полезные ссылки, которые уже поздно читать

- [Ansible - Getting Started](http://docs.ansible.com/intro_getting_started.html)
- [Ansible - Best Practices](http://docs.ansible.com/playbooks_best_practices.html)
- [Setting up Django with Nginx, Gunicorn, virtualenv, supervisor and PostgreSQL](http://michal.karzynski.pl/blog/2013/06/09/django-nginx-gunicorn-virtualenv-supervisor/)
- [How to deploy encrypted copies of your SSL keys and other files with Ansible and OpenSSL](http://www.calazan.com/how-to-deploy-encrypted-copies-of-your-ssl-keys-and-other-files-with-ansible-and-openssl/)
