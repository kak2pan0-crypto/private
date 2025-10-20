# 🔐 Настройка парольной политики Jatoba

📝 **Автор**: Лопасов И.В.  <br>
👤 **Соавтор**: Грубич В.Е.  <br>
📅 **Дата создания**: июнь 2025 <br> 

> 📌 **Версия Jatoba**: 4.10  
> 📁 **Путь к документации**: `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Jatoba`  
> ⚠️ **Исходные данные**: установлена только **Астра** или **Ред ОС**

## 🛠️ Этап 1. Подготовка системы

Для исключения ошибок при установке рекомендуется примонтировать диск `base` от **Astra 1.7.5**, чтобы зависимости подтянулись автоматически.

**📌 Для удобства использую команду, которая примонтирует iso образ с любым именем, <br> который находится в `/srv` плюс прописывается в sources.list**

### 📂 Монтируем ISO-образ из `/srv`

Если образ уже лежит в `/srv` закидываем эти команды пачкой:

```bash
mkdir /mnt/upd
cd /srv
mount -o loop "$(find /srv -maxdepth 1 -type f -print -quit)" /mnt/upd
echo "deb file:/mnt/upd 1.7_x86-64 main contrib non-free" | sudo tee -a /etc/apt/sources.list
ls /mnt/upd
nano /etc/apt/sources.list
```

> 💡 **Пояснение**:  
> `find /srv -maxdepth 1 -type f -print -quit`
> ищет первый попавшийся файл в /srv (без подкаталогов).
> <br>
> `-maxdepth 1`
> ограничивает поиск только самой папкой /srv.
> <br>
> `-type f`
> ищет только файлы (не папки).
> <br>
> `-print -quit`
> выводит путь к файлу и сразу завершает поиск.
> <br>
> `mount -o loop`
> монтирует образ как loop-устройство.
> <br>
> `Кавычки $(...)`
> правильно обрабатывают имена файлов с пробелами.
> <br>

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/6344t7fmrb.jpg)

Закомментируйте все строки в `/etc/apt/sources.list`, кроме добавленной, затем обновите репозитории:

```bash
apt update
```

---

## 📦 Этап 2. Установка Jatoba

Поместите папку с пакетами Jatoba (например, `jatoba 4.10`) в `/srv`.  <br>
> ✅ **Важно**: имя папки **должно начинаться с `jatoba`** (остальное — любое). <br>
> 🛠️ **Либо ставим вручную как вам удобно** <br>

Создайте и запустите скрипт установки:

```bash
touch sj.sh
nano sj.sh          # вставьте содержимое ниже
chmod +x sj.sh
./sj.sh
```

### 📜 Скрипт автоматической установки (`sj.sh`)

```bash
#!/bin/bash
# Переходим в папку jatoba с любой версией в /srv
JATOBA_DIR=$(find /srv -maxdepth 1 -type d -name "jatoba*" | head -n 1)
if [[ -n "$JATOBA_DIR" ]]; then
    cd "$JATOBA_DIR"
else
    echo "Папка jatoba не найдена в /srv"
    exit 1
fi
# Устанавливаем все .deb пакеты из текущей директории
dpkg -i *
apt --fix-broken install -y
# Создаем директорию /var/run/jatoba и настраиваем права
mkdir -p /var/run/jatoba
chown postgres: /var/run/jatoba/
# Создаем файл конфигурации tmpfiles.d
touch /usr/lib/tmpfiles.d/jatoba-4.conf
echo "d /run/jatoba 0755 postgres postgres - " > /usr/lib/tmpfiles.d/jatoba-4.conf
# Настраиваем права для директории /usr/jatoba-4
chown root:root /usr/jatoba-4
chown -R root:root /usr/jatoba-4
# Инициализируем базу данных PostgreSQL
cd /usr/jatoba-4/bin/
PGSETUP_INITDB_OPTIONS="--locale=ru_RU.utf8 --encoding=UTF-8" /usr/jatoba-4/bin/jatoba-setup initdb jatoba-4
# Запрашиваем у пользователя номер порта
read -p "Введите номер порта (по умолчанию 5432): " PORT
PORT=${PORT:-5432}
# Запрашиваем у пользователя значение для lic_server_addr
echo "Укажите где будет использоваться БД Jatoba:"
echo "Нажмите 1 - если для макетирования"
echo "Нажмите 2 - если на объекте заказчика"
read -p "Выберите вариант (по умолчанию 2): " LIC_SERVER_CHOICE
LIC_SERVER_CHOICE=${LIC_SERVER_CHOICE:-2}
if [[ "$LIC_SERVER_CHOICE" == "1" ]]; then
    LIC_SERVER_ADDR="10.116.241.22"
elif [[ "$LIC_SERVER_CHOICE" == "2" ]]; then
    LIC_SERVER_ADDR="https://license.gaz-is.ru"
else
    echo "Неверный выбор. Используется значение по умолчанию (объект заказчика)."
    LIC_SERVER_ADDR="https://license.gaz-is.ru"
fi
# Редактируем файл postgresql.conf
sudo sed -i -e "s|^#listen_addresses.*|listen_addresses = '*'|" \
            -e "s|^listen_addresses.*|listen_addresses = '*'|" \
            -e "s|^#port.*|port = $PORT|" \
            -e "s|^port.*|port = $PORT|" \
            -e "s|^#lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
            -e "s|^lic_file_path.*|lic_file_path = '/usr/jatoba-4/bin/jatoba.cer'|" \
            -e "s|^lic_server_addr.*|lic_server_addr = '$LIC_SERVER_ADDR'|" \
            /var/lib/jatoba/4/data/postgresql.conf
# Выводим настройки listen_addresses, port, lic_file_path и lic_server_addr
grep -E '^(listen_addresses|port|lic_file_path|lic_server_addr)' /var/lib/jatoba/4/data/postgresql.conf
# Переходим в директорию /usr/jatoba-4/bin/ и запускаем jactivator
cd /usr/jatoba-4/bin/
./jactivator
```

> ## 🔗 Подробнее об автоматической установке: [Автоустановка Jatoba](./auto_setup_jatoba.html)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/7uu2kb0dky.jpg)

После завершения скрипта:
- Получите лицензионный ключ (например: `0G6RN-BT8N4-7RDJY-DZW`)
- Активируйте его на [сайте лицензирования](http://license.ls.dev.da.lan/admin)
- Переименуйте полученный `license.bin` → `jatoba.cer`
- Поместите в `/usr/jatoba-4/bin/`

Добавьте Jatoba в автозагрузку:

```bash
systemctl enable jatoba-4
systemctl restart jatoba-4
systemctl status jatoba-4
```

---

## 🔒 Этап 3. Настройка парольной политики

> ⚠️ **Перед началом — сделайте бэкап!**

```bash
cp /var/lib/jatoba/4/data/postgresql.conf /var/lib/jatoba/4/data/postgresql.conf.bak
```

Откройте конфиг:

```bash
nano /var/lib/jatoba/4/data/postgresql.conf
```

Добавьте в файл (лучше в раздел безопасности):

```ini
# Security profile setting GIS
shared_preload_libraries = 'securityprofile'
securityprofile.db_name = 'postgres'
securityprofile.special_chars = '!@#$%^&*()_+{}|<>?='
securityprofile.special_count = 3
securityprofile.minimum_length = 16
securityprofile.lower_case_count = 1
securityprofile.upper_case_count = 1
securityprofile.numbers_count = 2
securityprofile.failed_login_attempts_max_time_sec = 300
securityprofile.password_lock_time = 300
securityprofile.password_life_time = -1
```

> 💡 **Примечание**: параметр `shared_preload_libraries` активирует модуль, но **политика применится только после установки расширения в БД**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/0adohe0hwh.jpg)

### 📊 Описание параметров

| Параметр | Описание |
|--------|--------|
| `shared_preload_libraries = 'securityprofile'` | Активирует модуль управления парольной политикой |
| `securityprofile.db_name = 'postgres'` | Указывается только БД `postgres` — политика применится ко всем БД |
| `securityprofile.special_chars` | Набор допустимых спецсимволов (можно изменить) |
| `securityprofile.special_count = 3` | Минимум 3 спецсимвола из набора |
| `securityprofile.minimum_length = 16` | Минимальная длина пароля — 16 символов |
| `securityprofile.upper_case_count = 1` | Минимум 1 заглавная буква |
| `securityprofile.numbers_count = 2` | Минимум 2 цифры |
| `securityprofile.failed_login_attempts_max_time_sec = 300` | Время окна для 5 неудачных попыток (5 мин) |
| `securityprofile.password_lock_time = 300` | Время блокировки учётной записи (в секундах) |
| `securityprofile.password_life_time = -1` | Срок действия пароля: `-1` = не ограничен |

---

## 🧪 Этап 4. Применение расширения в БД

Перезапустите сервис:

```bash
systemctl restart jatoba-4
systemctl status jatoba-4
```

Подключитесь к БД и установите расширение:

```bash
su -l postgres
psql -d postgres
create extension securityprofile;
\dx
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/23wu9uicuj.jpg)

> ❗ **Сразу после установки расширения — смените пароль пользователя `postgres`!**  
> Иначе учётная запись может быть заблокирована из-за несоответствия политики.

```sql
ALTER USER postgres WITH ENCRYPTED PASSWORD 'Gazprom*123';
```

Проверить политики можно командой:

```sql
SELECT * FROM securityprofile.show_profiles;
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/3d2ucb7rad.jpg)

---

## 🚫 Удаление парольной политики

Если нужно отключить:

```sql
DROP EXTENSION securityprofile;
```

Затем перезапустите Jatoba:

```bash
systemctl restart jatoba-4
systemctl status jatoba-4
```

> ⚠️ **Важно**: если политика применяется к уже работающей БД — **отключите все приложения**, использующие PostgreSQL, чтобы избежать блокировок.

Для разблокировки учётной записи (например, `efros_user`):

```sql
SELECT securityprofile.unlock_account('efros_user', 0);
```

---

## 📝 Примечания

- Параметры в `postgresql.conf` можно размещать в любом месте, но лучше группировать по смыслу.
- После изменений **обязательно перезапускайте** `jatoba-4`.
- Расширение `securityprofile` применяется ко **всем пользователям** всех БД, если активировано в `postgres`.

---

## 🎉 На этом всё!
