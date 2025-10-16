
**✏️ Автор**: Грубич В.Е.  
**📆 Дата создания**: июнь 2024  
**🔖 Обновлено**: февраль 2025

# 🛠️ Установка DatAPK ITM-K на RedOS 7.3 Murom

📁 **Образы и ПО**, использованное в данной инструкции, доступны на сетевом диске:  
`P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\DataPK ITM\Развертывание на РЕД ОС 7.3`

🐧 **РЕД ОС берём 6 ядро**:  
`P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Linux\ГДУ\РедОС`

---

## 💽 Установка РЕД ОС (ниже блок скринов)

1. **🌐 Язык**: оставить русский по умолчанию → «Продолжить».
2. **⌨️ Клавиатура**:  
   - Выбрать «английский (Английская (американская))».  
   - Переместить его **выше** в списке с помощью стрелки вверх → «Готово».
3. **🕒 Дата и время**:  
   - Настроить правильный часовой пояс.  
   - Проверить корректность даты и времени → «Готово».
4. **📦 Выбор программ**:  
   - Базовое окружение: **«Сервер минимальный»**.  
   - Дополнительное ПО:  
     - 🐍 Python  
     - 🔗 Библиотеки совместимости  
     - 🕸️ Perl для веб-разработки  
   - Нажать «Готово».
5. **💾 Разметка диска**:  
   - Для виртуальной машины — можно оставить автоматическое разбиение.  
   - На объекте:  
     - Выбрать «По-своему».  
     - Отключить LVM (если не требуется по проекту).  
     - Удалить раздел `/home`.  
     - Корневому разделу `/` выделить всё свободное место (ввести `999999` в «Требуемый объем»).
6. **📛 Имя хоста**:  
   Перейти в «Имя сети и узла» → в поле «Имя узла» указать **DATAPK** → «Применить».  
   *(Рекомендуется задавать понятное имя сразу.)*
7. **🔐 Пароль root**:  
   - Установить пароль.  
   - **Обязательно**:  
     - ✔️ Заблокировать учётную запись root  
     - ❌ Снять галочку «Разрешить вход пользователем root с паролем через SSH»  
   - ⚠️ **Внимание**: ошибка в PAM-политиках может привести к потере доступа.
8. **👤 Создание пользователя**:  
   - Создать обычного пользователя.  
   - ✔️ Сделать его администратором.  
   - Нажать «Готово» → «Начать установку».

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mv1pv8iqvt002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/w0vse5v2om002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bdarwfcys4002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/p4uj4uaxqn002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hr36icugs6002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/f27xf5p8o7002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ybfvsbimis002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5wnk273hq4002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/6rkjkmkfvd002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mkjrln64db002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xdlvfwbe7j002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/wdvc8qatm2002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/m08di4y369002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/uf2toksiu1002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/8xmbrnyh0q002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/prqvds9hll002.jpg)
  
# 📦 Подготовка к установке ITM-K

1. 📥 Скопировать архив `dependencies.zip` на сервер в `/opt`.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/xc4dj8d3ge002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3ys2jssfxc002.jpg)

2. 🗂️ Распаковать:
   
   ```bash
   unzip /opt/dependencies.zip -d /opt
   ```
   
   В архиве две папки: **docker** и **jatoba**.  
   Устанавливать сначала пакеты из **🐳 docker**, затем из **🗃️ jatoba**.
   
```bash
yum install --disablerepo=base,updates,kernels6 *.rpm --skip-broken
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/oea2bf4u63002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2joljss6dm002.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g29puxo65h002.jpg)

---

## 🐳 Установка Docker

```bash
mv /usr/bin/docker-compose /usr/local/bin/
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
systemctl enable docker --now
systemctl status docker  # должен быть active (running)
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/rmk058eg2v002.jpg)

---

## 🗄️ Установка СУБД Jatoba

**Необходимые пакеты**:
- `jatoba4-client-4.10.1-1185.x86_64`
- `jatoba4-contrib-4.10.1-1185.x86_64`
- `jatoba4-server-4.10.1-1185.x86_64`
- `jatoba4-libs-4.10.1-1185.x86_64`
- `gis-activator11-1.1.0-0.x86_64`
- *(опционально)* `jatoba4-securityprofile-4.10.1-1185.x86_64` — для парольной политики БД

📦 Установка:

Можно установить командой `rpm`

```bash
rpm -Uvh *.rpm
```
Либо через `yum` или `dnf`

```bash
yum install --disablerepo=base,updates,kernels6 *.rpm --skip-broken
```

> [!TIP]  
> rpm - утилита управления пакетами RPM.
> -U или --upgrade - обновить пакет, если он уже установлен, или установить, если его нет.
> -v или --verbose - подробный вывод процесса установки.
> -h или --hash - показывать хеш-символы (прогресс) во время установки.
> *.rpm - указывает на все RPM-файлы в текущей директории.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/865i5o8cu6.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/hyhthdyle5.jpg)

---

## 🌐 Инициализация Jatoba с UTF-8

> ✅ UTF-8 обеспечивает корректное отображение данных как на Windows, так и на Linux.

```bash
chown -R postgres:postgres /var/lib/jatoba
cd /usr/jatoba-4/bin/
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" ./jatoba-setup initdb jatoba-4
```

## ⚙️ Настройка PostgreSQL (Jatoba)

1. 📝 Отредактировать `/var/lib/jatoba/4/data/postgresql.conf`:
   - Раскомментировать и задать:
     
     ```bash
     listen_addresses = '*'
     port = 5432
     lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'
     ```
     
   - Для макетирования добавить:
     
     ```bash
     lic_server_addr = '10.116.241.22'
     ```
     
## 🔶 На реальных объектах:

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0xiv59wo6e006.jpg)

## 🔶 Для макетирования:

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/u77ry8deod.jpg)

2. 🤖 Автоматическое применение настроек (для макета):
   
   ```bash
   sudo sed -i \
   -e "s|^#listen_addresses.*|listen_addresses = '*'|" \
   -e "s|^#port.*|port = 5432|" \
   -e "s|^#lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
   -e "s|^#lic_server_addr.*|lic_server_addr = '10.116.241.22'|" \
   /var/lib/jatoba/4/data/postgresql.conf
   ```

## 🔑 Активация лицензии Jatoba

1. ▶️ Запустить активатор:
   
   ```bash
   cd /usr/jatoba-4/bin/
   ./jactivator
   ```
   
3. 📧 Указать корпоративную почту → выбрать **Offline-активацию** → сохранить файл запроса.
4. 🌍 Отправить запрос на:
   - **🧪 Макет**: http://license.ls.dev.da.lan/offlineActivate/  
   - **🏢 Объект**: https://license.gaz-is.ru/offlineActivate/  
5. 📥 Получить `license.bin` → переименовать в `jatoba.cer` → поместить в `/usr/jatoba-4/bin/`.

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x016o63l67006.jpg)  

> 💡 **Ключ на 1000 активаций (для тестов)**: `0G6RN-BT8N4-7RDJY-DZW`
> Сайт создания лицензий, он интуитивно понятен, для тестирования наших продуктов <br>
> Но по нему написал отдельную статью на всякий случай.
> Сайт создания лицензий http://license.ls.dev.da.lan/
> логин и пароль: `test`


## 🔄 Перезапустить Jatoba:

   ```bash
   systemctl enable jatoba-4
   systemctl restart jatoba-4
   systemctl status jatoba-4
   ```
![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/22badk8ma8004.jpg)  

# 🚀 Установка DATAPK ITM-K

1. 📁 Создать директорию:
   
   ```bash
   mkdir /opt/datapkitm
   ```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mf0k7j75a1002.jpg)     
   
2. 📤 Скопировать в неё:
   - `datapk_itm-k_v1.6.0.1.tar.gz`
   - `docker-compose.release.yaml`
   - `env_generator.sh`
3. 📦 Загрузить образы:

```bash
   docker load -i datapk_itm-k_v1.6.0.1.tar.gz
```

   *(Если ошибка `unexpected EOF` — архив повреждён.)*

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/dgr3bxfj7v002.jpg)    

## 🗃️ Настройка базы данных

1. 👤 Перейти в оболочку PostgreSQL:

   ```bash
   su -l postgres
   psql
   ```
   
3. 🧾 Выполнить:

   ```sql
   ALTER USER postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
   CREATE DATABASE datapkitm;
   CREATE USER datapkitm WITH ENCRYPTED PASSWORD 'Gazprom*123';
   GRANT ALL PRIVILEGES ON DATABASE datapkitm TO datapkitm;
   ALTER DATABASE datapkitm OWNER TO datapkitm;
   \q
   ```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/os2ednd915002.jpg)    

▶️ Устанавливаем буфер на виртуалке для макетирования можно пропустить данный этап <br>
(рекомендуют 50% от общего объёма ОЗУ на сервере )

```bash
nano /var/lib/jatoba/4/data/postgresql.conf
```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/poxxwvb2hh002.jpg)    

## 🌐 Генерация .env и настройка сети

1. ▶️ Запустить генератор:
   
   ```bash
   cd /opt/datapkitm
   chmod +x env_generator.sh
   ./env_generator.sh
   ```
   
   - Указать IP сервера, порт БД (`5432`), параметры подключения.
   - в проекте на реальном объекте порт может быть другой
   - поэтому внимательно читаем описание настроек
   - Первый пункт про синхранизацию с DATAPK выбираем `нет`

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2wo95medfl002.jpg)   

> ⚠️ **Важно**: при первом запуске создаётся Docker-сеть (по умолчанию `/16`).  
> Если нужно изменить подсеть — отредактируйте **`docker-compose.release.yaml`** и **`env_generator.sh`** до запуска.
> Написал отдельную статью по смене подсети тут будет позже

## 🔐 Настроить `pg_hba.conf` для доступа:

```bash
sudo tee /var/lib/jatoba/4/data/pg_hba.conf > /dev/null <<EOF
local   all             all                                     md5
host    all             all             0.0.0.0/0               md5
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            ident
EOF
cat /var/lib/jatoba/4/data/pg_hba.conf
systemctl restart jatoba-4
```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qeu27rw6ft002.jpg)   

> По сути тут правильно указать имена баз данных и пользователей, которые к ним могут подключаться и с каких IP адресов, зависит от проекта и требований заказчика.
> Если редактировали вручную - незабываем перезапустить jatoba

```bash
systemctl restart jatoba-4
```

## 🔒 Генерация SSL-сертификата

```bash
domain=192.168.133.139  # ← заменить на IP вашего сервера
mkdir -p env/nginx/certs
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout env/nginx/certs/nginx.key \
  -out env/nginx/certs/nginx.crt \
  -subj "/C=RU/CN=$domain/O=USSC"
chmod 644 env/nginx/certs/nginx.key
```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/qeu279t2nhtmaul002.jpg)  

## 🔗 Интеграция Jatoba и Docker

Чтобы Jatoba запускалась **после Docker**, добавьте зависимости в службу:

```bash
systemctl stop jatoba-4.service
systemctl edit jatoba-4.service
```

В открывшийся пустой файл вставьте:

```ini
[Unit]
After=docker.service
BindsTo=docker.service
ReloadPropagatedFrom=docker.service
```

✅ Применить:

```bash
systemctl daemon-reload
systemctl start jatoba-4
docker-compose up -d
docker ps  # проверить, что контейнеры запущены
```

![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/kxoa80i75c002.jpg)  
![Окно активации](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4o595pk3a6002.jpg)  

## 🎉 Завершение

Установка завершена!  
🌐 Откройте в браузере IP-адрес сервера.

**🔐 Логин/пароль по умолчанию**:  
`datapkitm / datapkitm`

