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

## Документация

https://d34m0nn0n3.github.io/ansible-ssr-pull/index.html
