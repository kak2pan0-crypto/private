📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **февраль 2025**

## 🧪 Установка ITM-A

> 📌 **Примечание**: Установка в целом аналогична **ITM-K**, но с некоторыми отличиями. Начало — **одинаковое**.

---

### 📁 Используемые образы и ПО

Доступны на сетевом диске:

- `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\DataPK ITM\DATAPKITM\ITM-A v.1.3.0.0`  
- `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\DataPK ITM\Развертывание на РЕД ОС 7.3`

## 📦 Подготовка к установке

1. 📥 Скопируйте архив `dependencies.zip` на сервер в `/opt`.
2. 🗂️ Распакуйте его:
   
   ```bash
   unzip /opt/dependencies.zip -d /opt
   ```
   
   > В архиве две папки: **`docker`** и **`jatoba`**.  
   > Устанавливайте **сначала пакеты из `docker`**, затем из `jatoba`.

4. 📦 Установите зависимости:
   
   ```bash
   yum install --disablerepo=base,updates,kernels6 *.rpm --skip-broken
   ```

## 🐳 Установка Docker

```bash
mv /usr/bin/docker-compose /usr/local/bin/
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
systemctl enable docker --now
systemctl status docker  # должен быть active (running)
```

✅ **Совет**: Можно выполнить весь блок сразу — просто вставьте в терминал.

## 🗄️ Установка СУБД Jatoba

### 🔧 Необходимые пакеты:
- `jatoba4-client-4.10.1-1185.x86_64`
- `jatoba4-contrib-4.10.1-1185.x86_64`
- `jatoba4-server-4.10.1-1185.x86_64`
- `jatoba4-libs-4.10.1-1185.x86_64`
- `gis-activator11-1.1.0-0.x86_64` ← **обязательно!**
- *(опционально)* `jatoba4-securityprofile-4.10.1-1185.x86_64` — для парольной политики БД

> 💡 Установка:
> 
> ```bash
> yum install --disablerepo=base,updates,kernels6 *.rpm --skip-broken
> ```

## ⚙️ Настройка Jatoba

### 1. Инициализация БД с UTF-8 (рекомендуется!)

> ✅ UTF-8 обеспечивает корректное отображение данных **и на Windows, и на Linux**. <br>
> Есть ДВА варианта и тут можно просто проинициализировать командой `./jatoba-setup initdb jatoba-4` <br>
> либо зададим кодировку, что бы все последующие БД создавались с этой же кодировкой по умолчанию <br>
> (обязательно нужно находиться в директории cd /usr/jatoba-4/bin/) <br>

```bash
chown -R postgres:postgres /var/lib/jatoba
cd /usr/jatoba-4/bin/
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" ./jatoba-setup initdb jatoba-4
```

### 2. Настройка `postgresql.conf`
Раскомментируйте и задайте:

```ini
listen_addresses = '*'
port = 5432
lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'
```

Для макетирования на виртуалке прописываем сервер лицензий:

```ini
lic_server_addr = '10.116.241.22'
```

Автоматическая настройка для макетирования `postgresql.conf`:

```bash
sudo sed -i \
  -e "s|^#listen_addresses.*|listen_addresses = '*'|" \
  -e "s|^#port.*|port = 5432|" \
  -e "s|^#lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
  -e "s|^#lic_server_addr.*|lic_server_addr = '10.116.241.22'|" \
  /var/lib/jatoba/4/data/postgresql.conf
```

Проверка:

```bash
grep -E '^(listen_addresses|port|lic_file_path|lic_server_addr)' /var/lib/jatoba/4/data/postgresql.conf
```

## 🔑 Активация лицензии Jatoba

1. Запустите активатор:
   ```bash
   cd /usr/jatoba-4/bin/
   ./jactivator
   ```
2. Укажите корпоративную почту → выберите **Offline-активацию** → сохраните запрос.
3. Отправьте запрос:
   - **Макет**: [http://license.ls.dev.da.lan/offlineActivate/](http://license.ls.dev.da.lan/offlineActivate/)  
   - **Объект**: [https://license.gaz-is.ru/offlineActivate/](https://license.gaz-is.ru/offlineActivate/)
4. Получите `license.bin` → переименуйте в `jatoba.cer` → поместите в `/usr/jatoba-4/bin/`.

> 🎁 **Ключ для тестов (1000 активаций)**:  
> `0G6RN-BT8N4-7RDJY-DZW`

> 🔐 Не забудьте перезапустить Jatoba после изменений!

## 🚀 Установка DATAPK ITM-A

1. Создайте директорию:
   ```bash
   mkdir /opt/datapkitm
   ```
2. Скопируйте файлы:
   - `datapk_itm-a_v1.3.0.1.tar.gz`
   - `docker-compose.release.yaml`
   - `env_generator.sh`
3. Загрузите образы:
   ```bash
   docker load -i datapk_itm-a_v1.3.0.1.tar.gz
   ```
   > ⚠️ Если ошибка `unexpected EOF` — архив повреждён.

---

## 🗃️ Настройка базы данных

1. Перейдите в оболочку PostgreSQL:
   
   ```bash
   su -l postgres
   psql
   ```
   
3. Выполните:
   
   ```sql
   ALTER USER postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
   CREATE DATABASE datapkitm;
   CREATE USER datapkitm WITH ENCRYPTED PASSWORD 'Gazprom*123';
   GRANT ALL PRIVILEGES ON DATABASE datapkitm TO datapkitm;
   ALTER DATABASE datapkitm OWNER TO datapkitm;
   \q
   ```

4. Настройка `pg_hba.conf`

```bash
sudo tee /var/lib/jatoba/4/data/pg_hba.conf > /dev/null <<EOF
local   all             all                                     md5
host    all             all             0.0.0.0/0               md5
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            ident
EOF
```

---

## 🌐 Генерация `.env` и настройка сети

```bash
cd /opt/datapkitm
chmod +x env_generator.sh
./env_generator.sh
```

> ⚠️ При первом запуске создаётся Docker-сеть (`/16` по умолчанию).  
> Если подсеть пересекается с АСУ ТП сегментом — вводим свои значения сразу.

---

## 🔒 Генерация SSL-сертификата

```bash
domain=192.168.133.139  # ← замените на IP вашего сервера
mkdir -p env/nginx/certs
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
  -keyout env/nginx/certs/nginx.key \
  -out env/nginx/certs/nginx.crt \
  -subj "/C=RU/CN=$domain/O=USSC"
chmod 644 env/nginx/certs/nginx.key
```

---

## 🔗 Интеграция Jatoba и Docker

Чтобы Jatoba запускалась **после Docker**:

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

Примените изменения:

```bash
systemctl daemon-reload
systemctl start jatoba-4
docker-compose up -d
docker ps  # проверка
```

## ✅ Финальная проверка

1. Убедитесь, что нет ошибок подключения к БД.
2. Проверьте, что переменная `Server` в Zabbix Proxy указывает на **внешний IP ITM-K**:
   
   ```bash
   docker exec -it itm_a_zabbix_proxy cat /etc/zabbix/zabbix_proxy.conf | grep Server
   ```
   
   > ❌ Если видите `127.0.0.1` — ошибка в БД или `.env`.

![1](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/mm3rrdoc77.jpg)


---

## 🎉 Завершение

> **На этом всё!**  
> Установка ITM-A завершена.
