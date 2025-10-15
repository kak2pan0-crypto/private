---
layout: default
title: "Установка Efros CI на Linux Astra 1.7.5"
description: "Краткая инструкция по установке Efros CI (Astra Linux)"
---

# Установка Efros CI на Linux Astra 1.7.5

✏️ **Автор инструкции:** Грубич В.Е. <br>
📆 **Дата создания:** июнь 2024

---

> На момент написания инструкции актуальные сертифицированные образы:
>
> - Astra-Linux-installation-1.7.5.16-06.02.24_14.21  
> - efrosci_4.10.103.270-2_amd64.deb  
> - Astra-Linux-base-1.7.5.16-06.02.24_14.21 (для обновления и установки пакетов)  
> - Jatoba 4.10

**Образы и ПО использованное в данной инструкции доступно на нашем сетевом диске и находится в следующих директориях:**

P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Linux\ГДУ\Astra Linux 1.7.5  
P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Efros\Efros CI\EfrosCI 4.10.103.270 Сертифицированная\Диск_1_установочный\Astra Linux  
P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Jatoba\jatoba 4.10

---

Устанавливаем Astra 1.7.5 c ядром 5.15 hardened (либо другое актуальное - смотрим требования вашего)

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

---

# На этом Astra Linux установлена. Переходим к установке и настройке Efros CI

## Настройка bond (на виртуальной машине можно пропустить этот этап)

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

Выводится всё в удобном формате и видно где есть провод а где нет (при условии что сам интерфейс включен)
Так же для подробного вывода можно использовать просто ip -c a (параметр -c добавляет цветности, что может быть удобно)

![1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0qa0c91273.jpg)

![1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/115luoijaf.jpg)

---

## Установка Jatoba

Для исключения ошибок при установке необходимо примонтировать диск base. <br>
(создадим директорию куда будем монтировать /mnt/upd1)

**Пример:**

```bash
mkdir /mnt/upd1
# переходим в директорию, где лежат образы и монтируем ISO:
mount <image>.iso /mnt/upd1
```

![Jatoba — подготовка](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xcjsbijaht005.jpg)
![Jatoba — пакеты](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g5ja8282dg003.jpg)

Правим `nano /etc/apt/sources.list` — комментим cdrom и всё лишнее и добавляем наш образ:

```text
deb file:/mnt/upd1 1.7_x86-64 main contrib non-free
```

![Примеры пакетов](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uthtpaqrbp003.jpg)

Выполним:

```bash
apt update
```

Для установки Jatoba необходимы 4 пакета (устанавливать в этой последовательности), а также `gis-activator`. <br> 
Для Efros CI ещё нужна — `gosjava`.

Для удобства создадим папку `jatoba` закинем в неё нужные пакеты и установим все пакеты командой:

```bash
dpkg -i *
```

(находясь в папке с пакетами — установка пройдёт по порядку, если пакеты пронумерованы)

![Установка пакетов Jatoba](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/83c7ogxh7k003.jpg)
![dpkg output / лог](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/123003.png)

Скорее всего будут ошибки, но это нормально — исправим зависимостями:

```bash
apt --fix-broken install
```

![fix-broken screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/jk7euc697x003.jpg)

Далее настраиваем Jatoba дальше, создаем каталог (если его нет, обычно он уже есть) и даём права
команды ниже можно сразу закинуть "пачкой"

```bash
mkdir /var/run/jatoba
chown postgres: /var/run/jatoba/
touch /usr/lib/tmpfiles.d/jatoba-4.conf
echo "d /run/jatoba 0755 postgres postgres -" > /usr/lib/tmpfiles.d/jatoba-4.conf
chown root:root /usr/jatoba-4
chown -R root:root /usr/jatoba-4
cd /usr/jatoba-4/bin/
```
> [!TIP] 
> Данные, закодированные в UTF-8, будут правильно отображаться как на Windows, так и на Linux, поэтому рекомендуется сразу указывать кодировку

Инициализируем БД и тут можно просто проинициализировать командой `./jatoba-setup initdb jatoba-4`
Либо зададим кодировку, что бы все последующие БД создавались с этой же кодировкой по умолчанию.
При инициализации обязательно нужно находиться в директории `/usr/jatoba-4/bin/`


```bash
# Простая инициализация:
./jatoba-setup initdb jatoba-4
# Инициализация с кодировкой (РЕКОМЕНДУЕТСЯ):
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" /usr/jatoba-4/bin/jatoba-setup initdb jatoba-4
```

![initdb скриншот](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5cxsj9uvnw003.jpg)

Прописываем путь к лицензии Jatoba в 

```bash
nano /var/lib/jatoba/4/data/postgresql.conf
```

раскомментируем `port` и `listen_addresses` (`*`), порт — `5432`, а `lic_file_path` ставим `/usr/jatoba-4/bin/jatoba.cer`.

![Настройка конфигурации Jatoba](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/eqi50lvh59006.jpg)
![Пример конфигурации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0xiv59wo6e006.jpg)

Для макетирования прописываем сервер лицензий:

```text
lic_server_addr = '10.116.241.22'
```

Есть сайт создания лицензий: [http://license.ls.dev.da.lan/](http://license.ls.dev.da.lan/) (логин: `test`, пароль: `test`)

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/22badk8ma8004.jpg)

Делаем запрос на активацию:

```bash
cd /usr/jatoba-4/bin/
./jactivator
```
> [!TIP] 
> Создал лицензию, можно пользоваться ей, если перестанет работать, тогда создаём свою... <br>
> ключ на 1000 активаций:  `0G6RN-BT8N4-7RDJY-DZW`

вводим нашу корп. почту <br>
выбираем Offline-активация <br>
ждём и вводим путь куда сохранить файл запроса <br>

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

![Jatoba service screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1vtfrl5ims003.jpg)

На этом установка Jatoba завершена.
---

Далее необходимо создать базу данных и пользователя для Efros CI

Она создаётся иначе, чем в Efros ACS.

В общих чертах - нам надо через jatoba создать пользователя с возможностью создавать базы данных, а потом установить Efros CI
И через него создать БД используя ранее созданного пользователя. Но большая часть похожа.

## Создание БД и пользователя для Efros CI

Заходим под `postgres` и задаём пароль:

```bash
su -l postgres
psql
alter user postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
```

Если всё прошло — появится `ALTER ROLE`.
Если ничего не появилось, значит допущена синтактическая ошибка в команде <br>
(**точка с запятой в конце обязательны**)

Создаём пользователя efros:

```sql
create role efros_user with login createdb encrypted password 'Gazprom*123';
```

Проверяем: `\du+`. Выход: `\q`.

Если нужно сменить пароль:

```sql
alter user efros_user with ENCRYPTED PASSWORD 'newpassword123';
```

Правим `pg_hba.conf`:

```bash
nano /var/lib/jatoba/4/data/pg_hba.conf
```

* В секции `local` заменяем `peer` на `md5`:

  ```
  local all all md5
  ```
* В секции IPv4 настраиваем одну строку, например:

  ```
  host all all 0.0.0.0/0 md5
  ```
* IPv6 — закомментировать.

По сути тут правильно указать имена баз данных и пользователей, которые к ним могут подключаться и с каких IP адресов, зависит от проекта и требований заказчика.

![pg\_hba example](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g2meuvpolq003.jpg)
![psql screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/gcipb64i88003.jpg)

Перезапускаем Jatoba:

```bash
systemctl restart jatoba-4
systemctl status jatoba-4
```

---

## Установка Efros CI

Переходим в папку с пакетом (например `/home/gis/`) и выполняем:

```bash
dpkg -i efrosci_4.10.103.270-2_amd64.deb
```

Появится окно настроек.

![Installer step](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ks69ixobug003.jpg)

Создаём базу данных — в интерфейсе выбираем «Настройка баз данных», затем «Изменение БД» → «Создать новую базу данных».

![Создание БД 2](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/efxd5oyiko003.jpg)
![Создание БД 1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/tqds0i9l2n003.jpg)

Меняем наш IP где установлена Jatoba, задаём имя будущей БД - efros_db <br>
Включаем защиту данных (алгоритм AES-256) - On <br>
Логин и пароль пользователя, что мы создали ранее с ролью создания баз данных <br>
И включаем - «Использовать данного пользователя для подключения к БД» <br>
И выбираем «создать новую БД»  <br>

Параметры:

* Имя БД: `efros_db`
* Включаем защиту данных (AES-256) — On
* Логин/пароль — тот, который создали ранее (`efros_user`)
* Выбираем «создать новую БД»

![DB created](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/eoig2m9e2a003.jpg)

Внизу должна появиться надпись что всё ОК, если не ок, то проверить запущена ли Jatoba...текст еле читаем, но нам главное цвет.
Так же что бы продолжить навигацию по меню, нажать esc или пробел, что бы исчезла надпись внижу, пока она есть мв не можем перемещаться по меню.

Далее предложит экспортировать ключ защиты
Задаём пароль и путь до ключа и жмём Enter — указываем путь и имя файла 
(пример: `gis/key` — `gis` папка, `key` — файл без расширения).

![Экспорт ключа](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uwtwfeej80003.jpg)

![Диалог экспорта ключа](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/i0dlnnds7o003.jpg)

Может сказать что ошибка ключа, т.к. мы его ещё не указали.
Просто указываем путь и имя файла нашего ключа и тогда соединение успешно проходит.
После указания ключа — проверяем соединение к БД (должна появиться надпись «всё ОК»). 

![Подтверждение OK](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4eha1rnhgn003.jpg)

Выходим из настроек БД - «назад» и заходим в «Дополнительно»
В разделе «Дополнительно» укажи путь к Java Runtime (для GosJava):

```
/usr/lib/jvm/java-8-gosjava-amd64/jre
```

![Путь Java](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/usma4kgk3i003.jpg)

Сохраняемся, выходим и запускаем службу:

```bash
systemctl enable efrosci.service
systemctl stop efrosci.service
systemctl start efrosci.service
systemctl status efrosci.service
```

![Запуск сервиса Efros CI](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1vtfrl5ims003.jpg)

Установка на Astra Linux завершена, теперь устанавливаем Efros Config Inspector Console 4.10.103.270.x64.msi на Windows что бы настроить сервер на Linux.

---

## Работа с Windows (Config Inspector Console)

Установка Efros Config Inspector Console `4.10.103.270 x64.msi` на Windows — для управления сервером на Linux.

* Логин/пароль по умолчанию: `root`
* Порт 20000 (по умолчанию)
* Указываем IP сервера (тот, где установлен CI)

![Windows client screenshot](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3244kcawna003.jpg)
![Windows client screenshot 2](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nknnsop90h003.jpg)

Рекомендуется сменить пароль при первом запуске.

Для ознакомления можно поработать в демо режиме и сделать снапшот на виртуалке, что бы вернуться к пробной версии, тем самым сбросив лицензию к начальному периоду, предварительно экспортировав настройки (добавленное оборудование) если необходимо т.к. используя наш сервер лицензирования прописать лицензию на него пока не получилось.

Первым делом переходим в настройки и добавляем модули

## Модули и добавление серверов

В настройках CI добавляем модули с установочного диска (папка модули для Astra Linux). Добавляем архив (разархивировать не нужно).
Обычно ставятся модули (DATAPK Eltex Jatoba Linux ViPNet Coordinator) в зависимости от проекта …

![Модули](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/1t4ml8qhw1003.jpg)
![Пример модуля](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/kpv0fhttkx003.jpg)

Теперь добавим наш сервер Efros ACS и его базу а так же сам сервер CI с его БД

![Добавление сервера/БД](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2whym9wb4i003.jpg)
![Добавление сервера/БД](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/vopp16kca7003.jpg)

Теперь добавим базу данных Jatoba сервера Efros ACS. Для этого кликнем на нём и создадим устройство на этом сервере.
Данные вводим пользователя postgres и снимаем две галочки с администраторов безопасности и пользователей.

![Добавление сервера/БД](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/nheu56e3al003.jpg)
![Добавление сервера/БД](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hj2r5ubi3j003.jpg)

На этом сама установка закончена
---

[Вернуться в оглавление](./efros.html)

