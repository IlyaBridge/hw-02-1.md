# Домашнее задание к занятию "Система мониторинга Zabbix" - `Казначеев Илья`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1
Установите Zabbix Server с веб-интерфейсом.

Процесс выполнения
Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.
Требования к результаты
Прикрепите в файл README.md скриншот авторизации в админке.
Приложите в файл README.md текст использованных команд в GitHub.

### Решение к заданию № 1
Установка Zabbix server на Ubuntu v24
1. Установка Zabbix server
1.1 Устанавливаем PostgreSQL: $ sudo apt install postgresql
1.2 Выбираем платформу для Zabbex
   ![image](https://github.com/user-attachments/assets/75da1838-3c96-459e-a68b-6106e11964fe)
1.3 Устанавливаем и конфигурируем Zabbix для выбранной платформы:
1.3.1 Стать пользователем root
$ sudo -s
1.3.2 Устанавливаем репозиторий Zabbix
команда # 1
$ wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
команда # 2
$ dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
команда # 3
$ apt update
1.3.3 Устанавливаем Zabbix сервер, веб-интерфейс
$ apt install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-apache-conf zabbix-sql-scripts

1.3.4 Проверим установку Zabbix
Выполним следующие комманды 
$ systemctl status zabbix-server.service

1.3.5 Создадим базу данных
$ sudo systemctl status postgresql

команда # 1
$ sudo -u postgres createuser --pwprompt zabbix

команда # 2
$ sudo -u postgres createdb -O zabbix zabbix

1.3.6 Импортируем БД
Импортируем эту БД в скрипты чтобы её инициализировать
На хосте Zabbix сервера импортируем начальную схему и данные, это нужно для того, чтобы её инициализировать. Нам будет предложено ввести недавно созданный пароль.

$ zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

1.3.7 Настроим базу данных для Zabbix сервера
Отредактируем файл /etc/zabbix/zabbix_server.conf
Введём команду:
$ nano /etc/zabbix/zabbix_server.conf

Изменим пароль:
DBPassword=password
DBPassword=123456789

1.3.8 Перезапускаем процессы Zabbix сервера и агента
Запустите процессы Zabbix сервера и агента и настройте их запуск при загрузке ОС.
$ systemctl restart zabbix-server apache2
Включаем автозапуск Zabbix
$ systemctl enable zabbix-server apache2

1.3.9 Входим в веб-интерфейс Zabbix сервера и агента
В браузере введём IP нашего сервера
![image](https://github.com/user-attachments/assets/dc7530c1-4faa-4804-871f-d8e6f7b1a93d)


Подключение веб-интерфейса Zabbix
Конфигурация БД уже предустановлена, необходимо ввести пароль который мы указывали при установки.
![image](https://github.com/user-attachments/assets/4c1ffbad-2a8a-44ed-8052-e1663ccc40ff)

Авторизуемся в web-интерфейсе:
![image](https://github.com/user-attachments/assets/753fb9b1-95a8-47be-bc54-1842071acbbc)
![image](https://github.com/user-attachments/assets/cd8e124a-d420-422b-9519-84857b292e54)

### Задание 2
Установите Zabbix Agent на два хоста.

Процесс выполнения
Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.
Требования к результаты
Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
Приложите в файл README.md текст использованных команд в GitHub

### Решение к заданию № 2
Установка и подключение Zabbix Agent
![image](https://github.com/user-attachments/assets/186d948e-4db6-42b4-b1f1-9a81ab523b9b)
0. Если мы на хосте ещё ничего не устанавливали, то необходимо проделать следующие команды:
$ wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb

1. Качаем пакет из репозитория
$ dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb

$ apt update

2. Устанавливаем Zabbix Agent
$ apt install zabbix-agent

3. Перезапускаем Zabbix и настраиваем чтобы запускался при каждом запуске сервера:
$ systemctl restart zabbix-agent
$ systemctl enable zabbix-agent

Постановка хоста с Zabbix agent на мониторинг
![image](https://github.com/user-attachments/assets/59316c94-6f10-490b-b9ee-26c6c9e3547e)
Добавим шаблон для мониторинга
Linux by Zabbix agent – это шаблон предоставляющий избыточные данные из коробки распространенных для систем мониторинга, с различными сработками и оповещениями. 
![image](https://github.com/user-attachments/assets/e7b44c6c-aecd-4a18-9e84-d10de73c6459)
Зайдем и выберем любой элемент мониторинга zabbix-agent
![image](https://github.com/user-attachments/assets/ca3d5991-f471-4289-bd83-525c1aba43f1)
![image](https://github.com/user-attachments/assets/97397c8b-27fb-4bd7-ad1e-890d2563aa42)
![image](https://github.com/user-attachments/assets/e91f1b15-50cc-43bf-bd13-916d91b62a33)
У нас zabbix должен ругнуться на то что не может подключиться по ip:
![image](https://github.com/user-attachments/assets/dae29200-84d6-46cc-9242-5869a9531eaf)
Для решения этой проблемы мы можем подключиться к zabbix агенту
Файл конфигурации zabbix-agent (на первом сервере 10.0.2.16):
Найти его можно командой:
	$ find / -name zabbix_agentd.conf
Находим его:
/etc/zabbix/zabbix_agentd.conf
И редактируем:
$ nano /etc/zabbix/zabbix_agentd.conf
В nano (Ctrl + W) ищем строку с Server=
В этот параметр можно через запятую добавить ip которые нам необходимы
Server=127.0.0.1, 10.0.2.16, 10.0.2.17
![image](https://github.com/user-attachments/assets/9eaf89b5-5c48-43a8-9c4d-838646a98158)
Просмотр лога zabbix-agent:
$ tail -f /var/log/zabbix/zabbix_agentd.log
![image](https://github.com/user-attachments/assets/ec0c19ef-3766-4fc3-82cd-544a4890dafc)
Проверим Web-интерфейс
В Web-интерфейсе мы можем видеть что zabbix-server смог подключиться к zabbix-agent:
![image](https://github.com/user-attachments/assets/94aac820-4822-4cd1-b270-7463026265f5)
Зайдем и посмотрим Монитор:
![image](https://github.com/user-attachments/assets/c181dd22-bd15-4240-851d-948d00f03faa)
Нажав на Graph можно посмотреть как данные изменялись за какое время:
![image](https://github.com/user-attachments/assets/49bde867-d9ac-45f9-85f8-2fa478139b83)

Установка и подключение Zabbix Agent на 2-й виртуальной машине с ip 10.0.2.17
1. Выполним процедуру установки Zabbix Agent на 2-ую виртуальную машину с ip адресом 10.0.2.17 следующими командами:
	Перейдём в повышенные права:
$ sudo -s

2. Установим репозиторий Zabbix
$ wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb

$ dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb

$ apt update

3. Установим Zabbix Agent
$ apt install zabbix-agent

4. Запустим процесс Zabbix Agent
Запустим процесс Zabbix агента и настроем его запуск при загрузке ОС.
$ systemctl restart zabbix-agent
$ systemctl enable zabbix-agent

Добавим шаблон для мониторинга на 1-ом сервере для 2-ого сервера с ip 10.0.2.17
Linux by Zabbix agent – это шаблон предоставляющий избыточные данные из коробки распространенных для систем мониторинга, с различными сработками и оповещениями.
![image](https://github.com/user-attachments/assets/08393dbf-dd97-48c5-b758-00bf5e052113)
![image](https://github.com/user-attachments/assets/7afc3c02-5dac-4883-8a60-5b060116c5de)
Обновим Web интерфейс и увидим следующее:
![image](https://github.com/user-attachments/assets/56589a87-a824-451a-8304-c1c6b3b5f519)
Что нужно сделать:
Файл конфигурации zabbix-agent (на 2-ом сервере 10.0.2.17):
Найти его можно командой:
	$ find / -name zabbix_agentd.conf
Находим его:
/etc/zabbix/zabbix_agentd.conf
И редактируем:
$ nano /etc/zabbix/zabbix_agentd.conf
В nano (Ctrl + W) ищем строку с Server=
В этот параметр можно через запятую добавить ip которые нам необходимы
Server=127.0.0.1, 10.0.2.16, 10.0.2.17
![image](https://github.com/user-attachments/assets/ef900b4c-3f7b-498e-a975-7a96e6088314)
Перезагрузим на 2-ом сервере с IP 10.0.2.17, чтобы изменения вступили в силу
$ systemctl restart zabbix-agent

Теперь всё работает:
![image](https://github.com/user-attachments/assets/1cce65d5-b2c9-4ace-ab07-68cfb41486ef)

Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.
![image](https://github.com/user-attachments/assets/9b8b44af-26b8-456b-8c45-d49c0663c4ac)

Приложите в файл README.md
![image](https://github.com/user-attachments/assets/1f048aa1-d4ec-4168-8950-2d10bfe4ec73)
![image](https://github.com/user-attachments/assets/bb74c9c6-93a9-4132-98ab-e82aaa8d1057)
![image](https://github.com/user-attachments/assets/df6e3eb4-3edf-4df7-a059-3e9c025dac1a)

---
