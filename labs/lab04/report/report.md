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
title: "Отчёт по лабораторной работе №4"
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

Приобрести практические навыки по установке и базовому конфигурированию HTTP-сервера Apache.

# Задание

1.	Установить необходимые для работы HTTP-сервера пакеты.
2.	Запустить HTTP-сервер с базовой конфигурацией и проанализируйте его работу.
3.	Настроить виртуальный хостинг.
4.	Написать скрипт для Vagrant, фиксирующий действия по установке и настройке HTTPсервера во внутреннем окружении виртуальной машины server. Соответствующим образом внесите изменения в Vagrantfile

# Выполнение лабораторной работы

## Установка HTTP-сервера

Загрузим операционную систему и перейдем в рабочий каталог с проектом: cd C:\Users\nikita\work\study\nvsakhno\vagrant
Запустим виртуальную машину server: make server-up.
На виртуальной машине server войдем под своим пользователем и откроем терминал. Перейдем в режим суперпользователя.
Установим из репозитория стандартный веб-сервер (HTTP-сервер и утилиты httpd, криптоутилиты и пр.) ([рис. @fig-001]).

![Вывод списка групп](image/1.png){#fig-001 width=70%}

![Установка веб-сервера](image/2.png){#fig-002 width=70%}

## Базовое конфигурирование HTTP-сервера

Внесем изменения в настройки межсетевого экрана узла server, разрешив работу с http: ([рис. @fig-003]).

![Команда firewall](image/3.png){#fig-003 width=70%}

В дополнительном терминале запустим в режиме реального времени расширенный лог системных сообщений, чтобы проверить корректность работы системы ([рис. @fig-004]).

![Расширенный лог системных сообщений](image/4.png){#fig-004 width=70%}

## Анализ работы HTTP-сервера

Запустим виртуальную машину client: make client-up.
На виртуальной машине server просмотрим лог ошибок работы веб-сервера и мониторинг доступа к веб-серверу: ([рис. @fig-005]).

![Лог ошибок и мониторинг доступа](image/5.png){#fig-005 width=70%}

На виртуальной машине client запустии браузер и в адресной строке введите 192.168.1.1.([рис. @fig-006]).

![Тестовая страница](image/6.png){#fig-006 width=70%}

В браузере открылась тестовая страница HTTP-сервера, на котороц написано сообщение: “Если вы можете это читать, то ПО работает корректно”. Следовательно, базовое конфигурирование HTTP-сервера выполнено правильно ([рис. @fig-007]).

![Результат мониторинга](image/7.png){#fig-007 width=70%}

## Настройка виртуального хостинга для HTTP-сервера

АНастроим виртуальный хостинг по двум DNS-адресам: server.nvsakhno.net и www.nvsakhno.net.
Для этого сначала остановим работу DNS-сервера для внесения изменений в файлы описания DNS-зон: systemctl stop named
Добавим запись для HTTP-сервера в конце файла прямой DNS-зоны /var/named/master/fz/nvsakhno.net: ([рис. @fig-008]).

![Файл прямой зоны](image/8.png){#fig-008 width=70%}

и в конце файла обратной зоны /var/named/master/rz/192.168.1: ([рис. @fig-009]).

![Файл обратной зоны](image/9.png){#fig-009 width=70%}

В обоих файлах изменим серийный номер файла зоны, указав текущую дату в нотации ГГГГММДДВВ. Также из соответствующих каталогов удалим файлы журналов DNS: nvsakhno.net.jnl и 192.168.1.jnl. ([рис. @fig-010]).

![Удаление файлов журналов DNS](image/10.png){#fig-010 width=70%}

Перезапустим DNS-сервер: systemctl start named
В каталоге /etc/httpd/conf.d создайте файлы server.nvsakhno.net.conf и www.nvsakhno.net.conf:
cd /etc/httpd/conf.d
touch server.nvsakhno.net.conf
touch www.nvsakhno.net.conf
Откроем на редактирование файл server.nvsakhno.net.conf и внесем следующее содержание

Перейдем в каталог /var/www/html, в котором должны находиться файлы с содержимым (контентом) веб-серверов, и создадим тестовые страницы для виртуальных веб-серверов server.nvsakhno.net и www.nvsakhno.net. Для виртуального веб-сервера server.nvsakhno.net:
cd /var/www/html
mkdir server.nvsakhno.net
cd /var/www/html/server.nvsakhno.net
touch index.html
Откроем на редактирование файл index.html и внесем следующее содержание: Welcome to the server.nvsakhno.net server. ([рис. @fig-011]).

![Редактирование файла](image/11.png){#fig-011 width=70%}

Для виртуального веб-сервера www.nvsakhno.net:
cd /var/www/html
mkdir www.nvsakhno.net
cd /var/www/html/www.nvsakhno.net
touch index.html
Откроем на редактирование файл index.html и внесем следующее содержание: Welcome to the www.nvsakhno.net server.
Скорректируем права доступа в каталог с веб-контентом: chown -R apache:apache /var/www
Восстановим контекст безопасности в SELinux и перезапустим HTTP-сервер ([рис. @fig-012]).

![Редактирование файла](image/12.png){#fig-012 width=70%}

# Выводы
В процессе выполнения лабораторной работы я приобрел пра ктические навыки по установке и базовому конфигурированию HTTP-сервера Apache.