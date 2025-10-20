📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **август 2024**
---

# 🛡️ Установка сервера KSC на РЕД ОС 7.3 Муром

> 📁 **Материалы**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Касперский\`

---

## 🗄️ 1. Установка PostgreSQL 15


### Установка пакетов
Первым делом необходимо установить БД. В моём случае это Postgresql-15server. Состоит из 4х пакетов. <br>
Установим их все, переходим в директории где они лежат и выполняем:


```bash
yum install --disablerepo=base,updates,kernels6 *.rpm
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/9xukjn6n6s.jpg)

---

## ⚙️ 2. Инициализация и настройка PostgreSQL

### Инициализация БД
```bash
postgresql-15-setup initdb
systemctl enable postgresql-15.service --now
systemctl status postgresql-15.service
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4qa7l0japq.jpg)

### Настройка `postgresql.conf`
Раскомментируйте и задайте:
```ini
listen_addresses = '*'
port = 5432
```

```bash
nano /var/lib/pgsql/15/data/postgresql.conf
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/h62fm9c13j.jpg)

### Настройка `pg_hba.conf`
Разрешите подключения с любого IP с аутентификацией по паролю:

```bash
nano /var/lib/pgsql/15/data/pg_hba.conf
```

Добавьте:
```conf
host    all             all             0.0.0.0/0               md5
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/gs8s5dlnur.jpg)

### Перезапуск службы
```bash
systemctl restart postgresql-15.service
```

---

## 👤 3. Создание пользователя и БД для KSC

```bash
su -l postgres
psql
```

Выполните в `psql`:
```sql
ALTER USER postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
CREATE DATABASE ksc;
CREATE USER ksc WITH ENCRYPTED PASSWORD 'Gazprom*123';
GRANT ALL PRIVILEGES ON DATABASE ksc TO ksc;
ALTER DATABASE ksc OWNER TO ksc;
\l
\du
\q
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fpr0waw7k0.jpg)

---

## 👥 4. Создание системного пользователя и группы

```bash
adduser ksc
groupadd kladmins
gpasswd -a ksc kladmins
usermod -g kladmins ksc
passwd ksc  # задайте пароль
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/d2q0gqtsig.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3wao4et545.jpg)

---

## 📦 5. Установка KSC и зависимостей

> ⚠️ **Важно**: Если сервер **без интернета**, заранее скачайте зависимости:
> ```bash
> yum install --downloadonly libxcrypt-compat
> ```
> Пакеты будут в `/var/cache/dnf/`.

Установите KSC и зависимости:
```bash
yum install --disablerepo=base,updates,kernels6 *.rpm
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/tl04v6qpeb.jpg)

---

## 🧩 6. Настройка KSC

Запустите скрипт настройки:
```bash
/opt/kaspersky/ksc64/lib/bin/setup/postinstall.pl
```

### Параметры настройки:
- Режим: **Стандарт (1)**
- IP сервера: ваш статический IP
- Порт: `13000`
- Группа: `kladmins`
- Пользователь: `ksc`
- БД: `ksc`, хост `127.0.0.1`, порт `5432`
- Пароль от БД: `Gazprom*123`
- Создайте учётную запись администратора KSC

> ⏳ После ввода пароля БД может показаться, что процесс завис — **подождите**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7o4tpts4gc.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/duekes7ljx.jpg)

---

## ⚡ 7. (Опционально) Оптимизация PostgreSQL

Для производительности на реальных серверах отредактируйте `/var/lib/pgsql/15/data/postgresql.conf`:

```ini
shared_buffers = 25% от RAM (минимум 128MB)
max_stack_depth = (результат `ulimit -s` в КБ) - 1024
temp_buffers = 24MB
work_mem = 16MB
max_connections = 151
max_parallel_workers_per_gather = 0
maintenance_work_mem = 128MB
```

---

## ✅ 8. Проверка служб

Убедитесь, что все службы запущены:
```bash
systemctl status klnagent_srv.service
systemctl status kladminserver_srv.service
systemctl status klactprx_srv.service
systemctl status klwebsrv_srv.service
```

> 💡 Некоторые ошибки подключения к интернету — **норма**, если сервер изолирован.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/amtwpxpjkv.jpg)

---

## 🌐 9. Установка Web Console

### Создайте файл настроек:
```bash
nano /etc/ksc-web-console-setup.json
```

Содержимое (замените IP на ваш):
```json
{
  "address": "192.168.133.149",
  "port": 8080,
  "trusted": "127.0.0.1|13299|/var/opt/kaspersky/klnagent_srv/1093/cert/klserver.cer|KSC Server",
  "acceptEula": true
}
```

### Установите Web Console:
```bash
yum install --disablerepo=base,updates,kernels6 ksc-web-console-15.1.523.x86_64.rpm
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/o5b7abwhps.jpg)

---

## 🚀 10. Завершение

1. Перезапустите службы:
   ```bash
   systemctl restart KSC*
   systemctl status KSC*
   ```

2. Откройте в браузере:
   ```
   https://<ваш_IP>:8080
   ```

   Например:  
   `https://192.168.133.149:8080`

3. Войдите под учётной записью, созданной при настройке KSC.

4. Запустится **мастер первоначальной настройки** — настройте политики, активируйте лицензию и т.д.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/bm3del1bjn.jpg)

---

## 🎉 Завершение

> **На этом установка KSC завершена!** <br>
> ✈️ **Совет**: Всегда проверяйте версии KSC и совместимость с ОС. <br>
> Для РЕД ОС 7.3 и 8.0 требуются разные зависимости!
