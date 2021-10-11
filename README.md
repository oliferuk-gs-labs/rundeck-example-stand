# Стенд для демонстрации ПО RUNDECK

Стенд предназначен для демонстрации возможностей ПО [Rundeck](https://www.rundeck.com/open-source)

Для разворачивания стенда используются [VirtualBox](https://www.virtualbox.org/), [Vagrant](https://www.vagrantup.com/) и [Ansible](https://www.ansible.com/)

# Зависимости
* virtualbox==6.1.26
* vagrant==2.2.18
* ansible==2.9.6
* vagrant plugin vbguest

# Запуск
```vagrant up```

В браузере - *http://127.0.0.1:4440*, login/pass - *admin/admin*

[Обмен ключами SSH](https://www.opennet.ru/man.shtml?topic=ssh-copy-id&category=1&russian=0) между rundeck'ом и другими нодами произведён на этапе разворотки стенда.

Для настройки проекта можноиспользовать файлы описания нод отсюда - */var/lib/rundeck/nodes*