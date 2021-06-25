[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

# ANSIBLE-PULL systemd service & temet
Copyright (C) 2020 Dmitriy Prigoda deamon.none@gmail.com This script is free software: Everyone is permitted to copy and distribute verbatim copies of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License.

## Содержание

  - [Содержание](#содержание)
  - [Общее описание](#общее-описание)
  - [Параметры](#параметры)
  - [Примеры](#примеры)

## Общее описание

Роль настраивает сервис и таймер systemd для локального запуска ansible сценария из git репозитория. Ansible pull используется для извлечения удаленной копии ansible на каждом управляемом узле, каждый из которых запускается через cron/timer и обновляет источник playbook через исходный репозиторий. Это инвертирует стандартную push- архитектуру ansible в pull- архитектуру, которая имеет почти безграничный потенциал масштабирования.

Для установки необходимо сделать клон проекта, настроить параметры в файле `inventory`, запустить `playbook`

```
    # Склонировать проект
    > git clone https://github.com/D34m0nN0n3/ansible-ssr-pull.git
    # Перейти в директорию с проектом
    > cd ansible-ssr-pull
    # Создать файл с параметрами
    > vim inventory/hosts
    # Запустить playbook
    > ansible-playbook -i inventory/hosts playbook.yml --ask-pass --become --ask-become-pass
```

## Параметры

### Общее параметры
|Название переменной    | Тип переменной | Значения по умолчанию | Описание                                                                   |
|:----------------------|:--------------:|:---------------------:|:---------------------------------------------------------------------------|
|git_user               | string         | undef                 | Пользователь для доступа к `git` репозиторию.                              |
|git_password           | string         | undef                 | Токен для авторизации.                                                     |
|sudoers_pull_runner    | string         | undef                 | Дополнительные правила `sudoers` для пользователя `pull-runner`.           |

* По умолчанию все сценарии запускаются из под пользователя `pull-runner`. Если дополнительные правила `sudoers` не заданы, то пользователю разрешено запускать любые команды с повышением привилегий.

### Параметры для создания сервиса и таймера
|Название переменной    | Тип переменной | Значения по умолчанию | Описание                                                                           |
|:----------------------|:--------------:|:---------------------:|:-----------------------------------------------------------------------------------|
|ansible_pull_repo      | array          | undef                 | Список элементов описания исходных сценариев и сервисов. Параметры описаны ниже.   |
|name                   | string         | undef                 | Имя для идентификации сервиса и таймера.                                           |
|repo_url               | string         | undef                 | Адрес исходного git репозитория со сценарием.                                      |
|host_invent            | string         | undef                 | Файл инвентаризации.                                                               |
|tempdir                | string         | undef                 | Временная папка куда помещается исходный сценарий.                                 |
|purge                  | boolean        | undef                 | Задает необходимо ли очищать папка после выполнения сценария.                      |
|id_vault               | string         | undef                 | Идентификатор ключа от файлов с секретами.                                         |
|timeout                | string         | undef                 | Задает максимальное время выполнения сценария.                                     |
|pulling_root           | string         | undef                 | Задает пользователя от которого выполняется запуск сценария.                       |
|description            | string         | undef                 | Задает описание сервиса.                                                           |
|docs                   | string         | undef                 | Задает ссылку на документацию в сервисе.                                           |
|timer_boot             | string         | undef                 | Задает параметр `OnBootSec` в таймере.                                             |
|timer_active           | string         | undef                 | Задает параметр `OnUnitActiveSec` в таймере.                                       |
|timer_calendar         | string         | undef                 | Задает параметр `OnCalendar` в таймере.                                            |

* Обязательным параметром является `repo_url`. Если ни один из параметров `timer_boot`, `timer_active`, `timer_calendar` не задан, то время выполнения устанавливается на 00:00:00 каждого понедельника.

### Парамерты для использования секретов
|Название переменной    | Тип переменной | Значения по умолчанию | Описание                                                                   |
|:----------------------|:--------------:|:---------------------:|:---------------------------------------------------------------------------|
|ansible_pull_secret    | array          | undef                 | Список элементов для создания ключей к секретам. Параметры описаны ниже.   |
|id_vault               | string         | undef                 | Идентификатор ключа от файлов с секретами.                                 |
|pass_vault             | string         | undef                 | Ключа от файлов с секретами.                                               |


## Примеры

### inventory/hosts (Nexus proxy сервер и RH Satellite)

```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_pull_repo=[{'name':'test1_ini', 'repo_url':'https://github.com/yours-org/yours-repo-1.git', 'timer_active':'15min'}, {'name':'test2_ini', 'repo_url':'https://github.com/yours-org/yours-repo-2.git', 'timer_active':'15min', 'purge':True, }]
```
