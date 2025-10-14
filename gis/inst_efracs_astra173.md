---
layout: default
title: "Установка Efros ACS на Linux Astra 1.7.3"
description: "Краткая база знаний ПО ГИС и другие рабочие моменты"
---

:calendar: Дата создания: июнь 2024  
:calendar: Последнее изменение: 14.11.2024  
:author: Грубич В.Е. — Grubich-V@gaz-is.ru  

---

Краткие инструкции по установке и настройке различных продуктов GIS  
и другие рабочие моменты

---

# Установка Efros ACS на Linux Astra 1.7.3

На момент написания инструкции актуальные сертифицированные образы:

- Astra-Linux-installation-1.7.3.16-06.02.24_14.21  
- Efros ACS_3.2.6.22 с внешней СУБД ФСТЭК (Efros ACS_3.2.6.22 с внешней СУБД ФСТЭК.rar)  
- Astra_Linux_1.7.5_base.iso (для обновления и установки пакетов)  
- Jatoba 4.10  

---

## Образы и ПО

Образы и ПО, использованное в данной инструкции, доступны на сетевом диске:

[P:\…\Linux\ГДУ\](\\gis.lan\dfs\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Linux\ГДУ\)  
[P:\…\Efros ACS …\Jatoba 4.10](\\gis.lan\dfs\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros ACS\Efros ACS_3.4.10.43\ … \Jatoba\jatoba 4.10)

---

## Установка Jatoba

Для исключения ошибок при установке рекомендую примонтировать диск `base` от Astra 1.7.5 (некоторые пакеты могут быть только там)

```bash
mkdir /mnt/upd1
# монтирование образа:
mount <имя_образа>.iso /путь/куда/примонтировать
````

![Jatoba подготовка](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xcjsbijaht006.jpg)

Правим файл `/etc/apt/sources.list`, закомментируем `cdrom` и добавляем:

```text
deb file:/mnt/upd1 1.7_x86-64 main contrib non-free
```

---

## apt update и установка пакетов

Сохраняем файл, выполняем:

```bash
apt update
```

Для установки Jatoba необходимы 4 пакета (устанавливать в том порядке, в котором пронумерованы), а также `gis-activator`.

```bash
dpkg -i *
```

При ошибках зависимостей:

```bash
apt --fix-broken install
```

---

## Настройка Jatoba + инициализация БД

```bash
mkdir /var/run/jatoba
chown postgres: /var/run/jatoba/
touch /usr/lib/tmpfiles.d/jatoba-4.conf
echo "d /run/jatoba 0755 postgres postgres -" > /usr/lib/tmpfiles.d/jatoba-4.conf
chown root:root /usr/jatoba-4
chown -R root:root /usr/jatoba-4
cd /usr/jatoba-4/bin/
```

Инициализация базы данных:

```bash
./jatoba-setup initdb jatoba-4
```

С опциями:

```bash
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" /usr/jatoba-4/bin/jatoba-setup initdb jatoba-4
```

---

## Создание БД и пользователя для Efros ACS

Под пользователем `postgres`:

```bash
su -l postgres
psql
alter user postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
create database efros_db;
CREATE user efros_user WITH ENCRYPTED PASSWORD 'Gazprom*123';
GRANT ALL PRIVILEGES ON DATABASE efros_db TO efros_user;
ALTER DATABASE efros_db OWNER TO efros_user;
\l
\du+
\q
```

Настройка доступа (`pg_hba.conf`):

```text
local all all md5
host all all 0.0.0.0/0 md5
# закомментировать секцию IPv6
```

Перезапуск службы:

```bash
systemctl restart jatoba-4
systemctl status jatoba-4
```

---

## Установка Efros ACS

Добавление cdrom:

```bash
apt-cdrom add
mount efros-acs.iso /media/cdrom/
```

Затем:

```bash
apt-get install efros-acs
```

В процессе установки появится окно настроек: указываем IP сервера, порт базы (по умолчанию 5432), имя БД и пользователя.

---

## Контейнеры, автозапуск и прочее

* При перезагрузке сервера контейнеры не запускаются автоматически — используем `docker update --restart=always <container_id>`
* Для Astra 1.7.5 могут понадобиться дополнительные настройки — см. `efros_acs_upd_1.7.5.html`

---

## Удаление Efros ACS (при необходимости)

```bash
apt remove --purge efros-acs
apt autoremove
```

---

[Вернуться к содержанию GIS](./efros.html)
