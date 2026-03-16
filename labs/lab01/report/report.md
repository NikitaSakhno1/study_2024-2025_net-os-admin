---
## Author
author:
  name: Сахно Никита Вячеславович
  email: 1132230298@rudn.ru
  affiliation:
    - name: Российский университет дружбы народов
      country: Российская Федерация
      postal-code: 117198
      city: Москва
      address: ул. Орджоникидзе 3

## Title
title: "Отчёт по лабораторной работе №1"
subtitle: "Администрирование сетевых подсистем"
license: "CC BY"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
---

# Цель работы

Приобрести практические навыки установки Rocky Linux на виртуальную машину с помощью инструмента Vagrant.

# Задание

1.	Сформируйте box-файл с дистрибутивом Rocky Linux для VirtualBox.
2.	Запустите виртуальные машины сервера и клиента и убедитесь в их работоспособности.
3.	Внесите изменения в настройки загрузки образов виртуальных машин server и client, добавив пользователя с правами администратора и изменив названия хостов.

# Выполнение лабораторной работы

## Содержание файлов

В ОС Windows создалим каталог для проекта.
В созданном рабочем каталоге разместим образ варианта операционной системы Rocky Linux (в этом практикуме будем использовать Rocky-9.2- x86_64-minimal.iso — минимальный дистрибутив Rocky Linux).
В этом же каталоге разместим подготовленные заранее для работы с Vagrant файлы:
•	vagrant-rocky.pkr.hcl
•	ks.cfg (файл должен быть расположен в подкаталоге http)
•	Vagrantfile
•	Makefile

![Содержимое каталога packer](image/1.png){#fig-001 width=70%}

![Содержимое подкаталога http](image/2.png){#fig-002 width=70%}

В этом же каталоге создадим каталог provision с подкаталогами default, server и client, в которых будут размещаться скрипты, изменяющие настройки внутреннего окружения базового (общего) образа виртуальной машины, сервера или клиента соответственно.

![Содержимое каталога provision](image/3.png){#fig-003 width=70%}

В каталогах default, server и client разместим заранее подготовленный скрипт заглушку 01-dummy.sh

![Содержимое файла 01-dummy.sh](image/4.png){#fig-004 width=70%}

В каталоге default разместим заранее подготовленный скрипт 01-user.sh по изменению названия виртуальной машины:

![Содержимое файла 01-user.sh](image/5.png){#fig-005 width=70%}

В этом скрипте в качестве значения переменной username вместо user укажем имя пользователя, совпадающее с моим логином, т.е. nvsakhno.
В каталоге default разместите заранее подготовленный скрипт 01-hostname.sh по изменению названия виртуальной машины:

![Содержимое файла 01-hostname.sh](image/6.png){#fig-006 width=70%}

## Развёртывание лабораторного стенда на ОС Linux

Я установил MSYS2 (сборка пакетов для Windows, которая позволяет использовать многие утилиты и приложения, которые обычно доступны только в Unix-подобных операционных системах), поэтому буду использовать команды для Linux.
Перейдем в каталог с проектом:
cd C:\work\study\nvsakhno\vagrant\ 
Для формирования box-файла с дистрибутивом Rocky Linux для VirtualBox в терминале наберем:
make box
Начнётся процесс скачивания, распаковки и установки драйверов VirtualBox и дистрибутива ОС на виртуальную машину. После завершения процесса автоматического развёртывания образа виртуальной машины в каталоге C:\work\study\nvsakhno\vagrant\ временно появится каталог builds с промежуточными файлами. vdi,. vmdk и .ovf, которые затем автоматически будут преобразованы в box-файл сформированного образа: vagrant-virtualbox-rocky9-x86_64.box.


![Появление box-файла](image/7.png){#fig-007 width=70%}

Для регистрации образа виртуальной машины в Vagrant в терминале в каталоге C:\work\study\nvsakhno\vagrant\ наберем make addbox

![Команда make addbox](image/8.png){#fig-008 width=70%}

Это позволит на основе конфигурации, прописанной в файле Vagrantfile, сформировать box-файлы образов двух виртуальных машин - сервера и клиента с возможностью их параллельной или индивидуальной работы.
Запустим виртуальную машину Server, введя make server-up

![Команда make server-up](image/9.png){#fig-009 width=70%}

Запустим виртуальную машину Client, введя make client-up

![Команда make client-up](image/10.png){#fig-010 width=70%}

Убедимся, что запуск обеих виртуальных машин прошёл успешно, залогинемся под пользователем vagrant с паролем vagrant. Затем выключим обе виртуальные машины.
![Окно server](image/11.png){#fig-011 width=70%}

![Окно client](image/12.png){#fig-012 width=70%}


## Внесение изменений в настройки внутреннего окружения виртуальной машины

Для отработки созданных скриптов во время загрузки виртуальных машин убедимся, что в конфигурационном файле Vagrantfile до строк с конфигурацией сервера имеется следующая запись:

config.vm.provision "common user",
type: "shell",
preserve_order: true,
path: "provision/default/01-user.sh"
config.vm.provision "common hostname",
type: "shell",
preserve_order: true,
run: "always",
path: "provision/default/01-hostname.sh"

Зафиксируем внесённые изменения для внутренних настроек виртуальных машин, введя в терминале:
make server-provision

![Команда make server-provision](image/13.png){#fig-013 width=70%}

Затем make client-provision

![Команда make client-provision](image/14.png){#fig-014 width=70%}

Залогинемся на сервере и клиенте под созданным пользователем. Убедимся, что в терминале приглашение отображается в виде user@server.user.net на сервере и в виде user@client.user.net на клиенте, где вместо user указан мой логин - nvsakhno. Выключим виртуальные машины.


# Выводы

В процессе выполнения данной лабораторной я приобрела практические навыки установки Rocky Linux на виртуальную машину с помощью инструмента Vagrant.
