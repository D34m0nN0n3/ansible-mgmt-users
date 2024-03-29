[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

# Управление учетными записями пользователей в Linux на основе CSV файла.
Copyright (C) 2020 Dmitriy Prigoda deamon.none@gmail.com This script is free software: Everyone is permitted to copy and distribute verbatim copies of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License.

Протестировано на:
- CentOS 7 & 8
- Ansible = 2.9.5

## Общее описание
Сценарий для массового создание, изменения и удаления пользователей по списку из CSV файла.

Чтобы скачать последнюю версию необходимо выполнить команду на клиенте с которого будет запущен данный playbook:

```
> cd ~
> git clone https://github.com/D34m0nN0n3/ansible-mgmt-users.git
```
### Запуск Playbook'а
Для запуска данного сценария необходимо:
1. Убедится что есть сетевой доступ с управляющего сервера до управляемых узлов по 22 порту (ssh). Подробно описано в документации: `Connection methods` и `Ansible passing sudo`. 
2. В наличии учетной записи с правами администратора из под которой будет производится подключение.
3. Заполненный файл [users_list.csv](users_list.csv) со списком пользователей.
4. Запустить сценарий.

Пример запуска с указанием пароля пользователя под которым подключаемся к управляемым хостам:
```
bash
# Go to the project folder:
> cd ~/ansible-update-os
# Run playbook:
> ansible-playbook -i hosts account-mgmt-from-csv.yml
# или
> ansible-playbook -i hosts account-mgmt-from-csv.yml --user=<local_user_name> --ask-pass --become
``` 

Key                 |INFO
--------------------|------------------------------------------------------------------
-i                  |Необходимо указать путь к файлу `inventory`
-v or -vv           |Повышает информативность вывода, нужен для анализа ошибок
--become            |Предоставляет повышение полномочий через `sudo` УЗ при подключение
--ask-pass          |Запрашивает пароль УЗ под которой будет выполнен сценарий
--ask-become-pass   |Запрашивает пароль УЗ при запуске `sudo`
-e                  |[Дополнительные переменные](#дополнительные-переменные)

Пример файла: [users_list.csv](users_list.csv)
```
user;password;comment;state
user1;P@ssw0rd;user1@example.com,+7(999)123-45-67;present
user2;!;comment;present
user3;P@ssw0rd;comment;absent
```

Первая строка содержит название полей, заполнение файла начинается со второй строки. Разделитель между столбцами `;`.

Поле                |Описание
--------------------|----------------------------------------------------------------------------------
user                |Логин для учетной записи
password            |Пароль от учетной записи, для блокировки УЗ необходимо установить значение в `!`
comment             |Дополнительная информация в формате GECOS, разделитель `,`
state               |Состояние: `present` - должна присутствовать, `absent` - должна быть удалена

### Дополнительные переменные

Переменная              |Описание                                                                   |По умолчанию
------------------------|---------------------------------------------------------------------------|-------------------
users_file              |Имя CSV файла из которого читать данные                                    |users_list.csv
change_force_password   |Устанавливать принудительную смену пароля при первом входе                 |True
cleanup_force_homedir   |Устанавливать принудительную очистку домашнего католога при входе и выходе |True
user_expires_date       |Время в днях через которое УЗ будет заблокирована                          |-1 (бессрочна)
generate_ssh_key        |Нужна ли генерация SSH ключей                                              |no

**Внимание!!! Данный сценарий создан под определенную задачу, при необходимости следует скорректировать нужные параметры.**

## Дополнительные материалы

- [Ansible User Module](https://docs.ansible.com/ansible/2.5/modules/user_module.html)
- [Defining variables at runtime](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#defining-variables-at-runtime)
- [GECOS](https://ru.wikipedia.org/wiki//etc/passwd#GECOS)
