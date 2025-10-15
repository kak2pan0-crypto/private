---
layout: default
title: "Установка Efros ACS на Linux Astra 1.7.3"
description: "Краткая база знаний ПО ГИС и другие рабочие моменты"
---

✏️ Грубич В.Е. — Grubich-V@gaz-is.ru  
📆 Дата создания: июнь 2024  
🔖 Последнее изменение: 14.11.2024  


# Установка Efros ACS на Linux Astra 1.7.3

На момент написания инструкции актуальные сертифицированные образы:

- Astra-Linux-installation-1.7.3.16-06.02.24_14.21   <br>
- Efros ACS_3.2.6.22 с внешней СУБД ФСТЭК (Efros ACS_3.2.6.22 с внешней СУБД ФСТЭК.rar)   <br>
- Astra_Linux_1.7.5_base.iso (для обновления и установки пакетов)   <br>
- Jatoba 4.10   <br>

## Образы и ПО

Образы и ПО, использованное в данной инструкции, доступны на сетевом диске:

P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Linux\ГДУ\ <br>
P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros ACS\Efros ACS_3.4.10.43 <br>
P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Jatoba\jatoba 4.10 <br>

---

▶️ Устанавливаем Astra **1.7.3** c ядром 5.15 hardened (либо другое актуальное ядро - смотрим требования вашего проектаес  )

![Astra install preview](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bn2cck0rwb005.jpg)

## Разметка диска

**sda** - (системный диск с ОС)

- sda1 -- efi — 1024 mb  
- sda2 -- /boot — 10 gb  
- sda3 -- / — 197 gb (вычисляем размер в зависимости от системного диска)  
- sda4 -- /var/log/audit — 15 gb  
- sda5 -- swap — 2× размера оперативной памяти, но не более 16 GB

**sdb** - (под хранилище)

- sdb1 -- /backup — всё пространство (сервер Cyber Backup)  
- sdb1 -- /var/lib/jatoba — всё пространство (везде где Jatoba)

![Разметка диска](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/w07sqovlqp005.jpg)

![Пример выбора компонентов](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5e3gvgs5qj005.jpg)

Дополнительные настройки: оставляем только запрос пароля для `sudo` и устанавливаем пароль на GRUB.

![Опции установки](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/f6dkll21qi005.jpg)

![Опции установки 2](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qst69wguqm005.jpg)

# На этом Astra Linux установлена. 
# Переходим к установке и настройке Efros ACS

## ▶️ Настройка bond (на виртуальной машине можно пропустить этот этап)

**Пример настройки (файл `/etc/network/interfaces`):**

```text
# The loopback network interface
auto lo
iface lo inet loopback

auto eth0 eth1 bond0
iface eth0 inet manual
iface eth1 inet manual

iface bond0 inet static
address 10.53.119.38
netmask 255.255.255.240
gateway 10.53.119.33
bond-mode 802.3ad
bond-miimon 100
bond-downdelay 200
bond-updelay 200
bond lacp rate slow
bond-xmit-hash-policy 1
bond-slaves eth0 eth1
````

Если настройки сети не известны, можно временно поднять сеть (после перезагрузки сбрасывается):

```bash
ip link set eth0 up
ip addr add 10.53.119.38/255.255.255.0 dev eth0
```

Для виртуалки по DHCP:

```text
auto lo
iface lo inet loopback

allow-hotplug eth0
iface eth0 inet dhcp
```

Для виртуалки временная статика:

```text
auto eth0
iface eth0 inet static
address 192.168.0.65
netmask 255.255.255.0
gateway 192.168.0.1
```

> Перед настройкой `bond` необходимо установить пакет:
>
> ```bash
> apt install ifenslave
> ```
>
> (примонтируй диск Astra-Linux-base-1.7.5.16 и пропиши его в sources.list)

---

Что бы наверняка узнать какой и куда провода подключены, можно пользоваться командой

```bash
ip -br -с a
```

Выводится всё в удобном формате и видно где есть провод а где нет (при условии что сам интерфейс включен) <br>
Так же для подробного вывода можно использовать просто `ip -c a` (параметр `-c` добавляет цветности, что может быть удобно) <br>

![1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0qa0c91273.jpg)

![1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/115luoijaf.jpg)

## Установка Jatoba

Для исключения ошибок при установке рекомендую примонтировать диск `base` от Astra 1.7.5 <br>
(некоторые пакеты могут быть только там) `Либо установочный диск Астры`

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

* Создает каталог /var/run/jatoba
* Меняет владельца каталога /var/run/jatoba на пользователя postgres и группу postgres
* Создает пустой файл конфигурации /usr/lib/tmpfiles.d/jatoba-4.conf
* Записывает строку конфигурации в файл /usr/lib/tmpfiles.d/jatoba-4.conf
* Меняет владельца каталога /usr/jatoba-4 на пользователя root и группу root
* Рекурсивно меняет владельца каталогаи всех его подкаталогов на пользователя root и группу root
* Переходит в каталог /usr/jatoba-4/bin/ <br>

> [!TIP] 
> Данные, закодированные в UTF-8, будут правильно отображаться как на Windows, так и на Linux, поэтому рекомендуется сразу указывать кодировку

‼️ Инициализируем БД и тут можно просто проинициализировать командой `./jatoba-setup initdb jatoba-4` <br>
‼️ Либо зададим кодировку, что бы все последующие БД создавались с этой же кодировкой по умолчанию. <br>
‼️ При инициализации обязательно нужно находиться в директории `/usr/jatoba-4/bin/` <br>

```bash
# Простая инициализация:
./jatoba-setup initdb jatoba-4
# ✅ Инициализация с кодировкой (✳️ РЕКОМЕНДУЕТСЯ ✳️):
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" /usr/jatoba-4/bin/jatoba-setup initdb jatoba-4
```
![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wq4xwb48ti003.jpg)

Прописываем путь к лицензии jatoba

```bash
nano /var/lib/jatoba/4/data/postgresql.conf
```

Попутно раскомментируем строки `port` оставляем `5432` (зависит от проекта и описания настроек) и <br>
`listen_addresses` и поставим там `*`, а значение `lic_file_path` меняем на `/usr/jatoba-4/bin/jatoba.cer`

Так же можно это сделать одной командой:

```bash
sudo sed -i -e "s|^#listen_addresses.*|listen_addresses = '*'|" \
-e "s|^listen_addresses.*|listen_addresses = '*'|" \
-e "s|^#port.*|port = 5432|" \
-e "s|^port.*|port = 5432|" \
-e "s|^#lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
-e "s|^lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
/var/lib/jatoba/4/data/postgresql.conf
grep -E '^(listen_addresses|port|lic_file_path)' /var/lib/jatoba/4/data/postgresql.conf
```

Последняя команда покажет применились ли новые значения или нет

Для макетирования на виртуалке прописываем сервер лицензий **`lic_server_addr = '10.116.241.22'`**

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/eqi50lvh59006.jpg)

## 🔶 На реальных объектах:

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0xiv59wo6e006.jpg)

## 🔶 Для макетирования:

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/u77ry8deod.jpg)


## Делаем запрос на активацию:

```bash
cd /usr/jatoba-4/bin/
./jactivator
```

> [!TIP] 
> Создал лицензию, можно пользоваться ей, если перестанет работать, тогда создаём свою ... <br>
> ключ на 1000 активаций:  `0G6RN-BT8N4-7RDJY-DZW`
>
> ## 🔗 Есть сайт создания лицензий: <br> 
> [http://license.ls.dev.da.lan/](http://license.ls.dev.da.lan/) (логин: `test`, пароль: `test`)

вводим нашу корп. почту <br>
выбираем Offline-активация <br>
ждём и вводим путь куда сохранить файл запроса <br>

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x016o63l67006.jpg)

Переходим на сайт для активации лицензий: <br>
Для макетирования: http://license.ls.dev.da.lan/offlineActivate/ <br>
На объектах: https://license.gaz-is.ru/offlineActivate/ <br>

И закидываем на сайт наш реквест, на почту приходит license.bin <br>
переименовываем файл license.bin в jatoba.cer <br>
закидываем файл лицензии в папку /usr/jatoba-4/bin <br>

Добавляем Jatoba в автозагрузку и перезапускаем сервис и смотрим на статус, должно работать:

```bash
systemctl enable jatoba-4
systemctl restart jatoba-4
systemctl status jatoba-4
```

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/22badk8ma8004.jpg)

На этом установка Jatoba завершена.

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
* Переключается на пользователя postgres с загрузкой его окружения
* Запускает интерактивную оболочку PostgreSQL
* Изменяет пароль пользователя postgres на 'PASSWORD-1' с использованием шифрования
* Создает базу данных efrosdb
* Создает пользователя efros с паролем 'PASSWORD-2' с использованием шифрования
* Предоставляет все привилегии на базу данных efrosdb пользователю efros
* Изменяет владельца базы данных efrosdb на пользователя efros
* Показывает список всех баз данных
* Показывает список всех пользователей и их ролей
* Выходит из интерактивной оболочки PostgreSQL

Если необходимо сменить пароль у уже существующего пользователя, выполняем команду ниже:

```bash
alter user efros_user with ENCRYPTED PASSWORD 'newpassword123';
```

Далее включить запрос пароля при аутентификации пользователя при подключении к СУБД в файле:

```bash
nano /var/lib/jatoba/4/data/pg_hba.conf
```

Настройка доступа (`pg_hba.conf`):

В секции local в поле method ставим вместо `peer` значение `md5` <br>
В секции IPv4 убираем все строки, прописываем только одну c нулями... <br>

```text
local all all md5 
host all all 0.0.0.0/0 md5
# закомментировать секцию IPv6
```

**По сути тут правильно указать имена баз данных и пользователей, которые к ним могут подключаться и с каких IP адресов, зависит от проекта и требований заказчика.**

Обязательно перезапуск службы:

```bash
systemctl restart jatoba-4
systemctl status jatoba-4
```

---

## Установка Efros ACS

> [!TIP] 
> Примечание: проверить что установочный диск Астры закоментирован в source.list <br>
> При выполнении на виртуалке рекомендуется так же извлечь сам диск (iso из VMWare) <br>
> А диск Base.iso лучше скопировать внутрь виртуалки, а не подключать внешне. <br>
> Установочный диск efros-acs.iso так же скопировать внутрь виртуалки <br>

Добавление cdrom:

```bash
apt-cdrom add
mount efros-acs.iso /media/cdrom/
```
## 🔴 Тут не торопимся и делаем всё внимательно !!!
## 🔷 Не спешим нажимать `Enter`

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5w3m13ugtn003.jpg)

**В параллельной сессии ssh монтируем образ в /media/cdrom**
Не забываем, что мы должны находиться в директории с образом.

```bash
mount efros-acs.iso /media/cdrom/
```

Затем возвращаемся в нашу основную сессию и жмём **`Enter`** и получаем ошибку :

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/37kipagsog003.jpg)

И вводим команду `apt-get install efros-acs` и продолжаем установку

```bash
apt-get install efros-acs
```

И повторно монтируем в параллельной сессии образ в `/media/cdrom`

```bash
mount efros-acs.iso /media/cdrom/
```

Возвращаемся в основную сессию и нажимаем Enter - начнётся установка Efros ACS по её завершению появится окно настройки

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xn5s4kgff0003.jpg)

В процессе установки появится окно настроек: ip Docker оставляем по умолчанию, указываем IP сервера, порт базы (по умолчанию 5432), имя БД и пользователя, которые мы задали ранее.

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/24ut5bhrt0003.jpg)
![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/twdpnj7i36003.jpg)
![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/rtm3qqerpy003.jpg)
![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/jxjv6pylyp003.jpg)
![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nxti40583e003.jpg)
![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wvv71lacyr003.jpg)

Далее продолжается установка...примерно ~ 5 минут, ниже скрин успешной установки, если всё сделали правильно и соблюдали версионность продуктов:

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fu1j4n7b6t003.jpg)

## ❗❗❗ ВАЖНО ❗❗❗
Прежде чем идти в web-интерфейс необходимо перереконфигурацию Efros ACS выполнив команду :

```bash
dpkg-reconfigure efros-acs
```

снова вводим данные от БД

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/950kt018ef003.jpg)

Далее переходим в браузер по ip нашего сервера (может быть ошибка 502 - подождать и обновить страницу либо перезагрузить сервер и снова запустить перереконфигурацию)

🔐 Учётные данный Efros ACS по умолчанию:

```text
administrator
$Qwerty123456$
```

Активируем комплекс <br>
Премиум лицензия неограниченая для макетирования и внутреннего использования <br>
(почту указываем свою корп. - офлайн активация) <br>
Номер лицензии - `JLF8V-WLBH1-8FC7L-2SV` <br>
〽️ Либо запрашиваем у нашей ТП ключ для соответствующей версии для макетирования, <br>
〽️ Если сайт выпуска тестовых лицензий не помог.

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uxsv1tg7mt003.jpg)

В загрузку автоматом упадёт файл, на моём примере это: Grubich-V@gaz-is.ru (5).json <br>
Активируем на https://license.gaz-is.ru/offlineActivate/ выбирая наш файл: <br>
Либо на сайте тестовых лицензий...смотря какая у вас лицензия.

Далее следуем инструкциям по активации, сохраняем файл license.bin который нам придёт на почту либо с сайта активации упадёт в загрузки и указываем его в веб-интерфейсе.

Активируем систему. Выходим из Web интерфейса Efros ACS.
Заходим по новой и нас попросят сменить пароль.

# Настройка автозапуска контейнеров

Теперь что бы при перезагрузке сервера у нас автоматически поднялись контейнеры с ACS выполним ряд действий.
Узнаем имена контейнеров, обычно их 4 - выполним docker ps

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2rp7rcn0am.jpg)

копируем значения столбца CONTAINER ID и подставляем их в команду ниже:

```bash
docker update --restart=always ea4acf54a0ae
docker update --restart=always a5b11a39fbdf
docker update --restart=always 96d6bfc196c8
docker update --restart=always f9016071e99e
```

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ht6no0k4cl.jpg)

Так же учитываем, если выполнили перереконфигурацию, то контейнеры удаляются и создаются новые, т.е. их снова надо будет добавить в автозагрузку.

Для версии 1.7.3 этого достаточно, что бы контейнеры стартовали при перезагрезке сервера, но на Астра 1.7.5 надо внести ещё несколько настроек, которые описаны в следующей статье.

## Удаление Efros ACS (при необходимости)

```bash
apt remove --purge efros-acs
apt autoremove
```

**На этом сама установка закончена**
