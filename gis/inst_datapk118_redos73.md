📝 Автор инструкции: **Смирнов В.В.**  
📅 Дата создания: **апрель 2025**

# 🚀 Установка DATAPK 1.18 в настроенной ОС RedOS 7.3

> 📁 **Источник материалов**:  
> `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\DATAPK\ПАК DATAPK`

---

## 📦 Подготовка файлов комплекса

1. 📤 Скопируйте основную директорию **`datapk`** в `/opt`:
   ```bash
   sudo cp -r datapk /opt/
   ```

2. ✅ Убедитесь, что в `/opt/datapk` присутствуют следующие компоненты:
   - 📁 `NGUI/environment/environment.json` — конфигурация UI (уникальна для ТК/филиала/предприятия)
   - 📄 `docker-compose.release.yml`, `docker-compose.stand.yml`, `docker-compose.backup.yml`
   - 🔐 `ca.crt` — корневой сертификат
   - 📜 `generate_env_file-[версия].sh` — скрипт генерации `.env`
   - 📁 `scripts_certs/datapk_script.crt` — сертификат для проверки скриптов
   - 📁 `logstash/` — правила нормализации событий
   - 📁 `export_data/` — пустая директория для экспорта
   - 🔑 `box_client.key` и `box_client.crt` — клиентские сертификаты

---

## 🔐 Получение клиентских сертификатов (если не поставляются в ПАК)

> ⚠️ На платформах базового уровня сертификаты обычно уже включены.

1. 🏷️ Установите понятное имя хоста:
   ```bash
   sudo hostnamectl set-hostname your-datapk-hostname
   ```

2. ▶️ Запустите скрипт `get_id.sh` в зависимости от уровня:
   ```bash
   # Верхний уровень (ДО)
   ./get_id.sh DO

   # Средний уровень (Филиал)
   ./get_id.sh F

   # Базовый уровень (ТК)
   ./get_id.sh TK
   ```
   → Будет создан архив `*.tar.gz`.

3. 📩 Отправьте архив на `support@cyberlympha.com` с указанием:
   - Архива от `get_id.sh`
   - Номера телефона для получения пароля к сертификатам

4. 📥 После получения — скопируйте `box_client.crt` и `box_client.key` в `/opt/datapk`.

---

## 🔍 Проверка цепочки сертификатов

Убедитесь, что клиентские сертификаты подписаны вашим `ca.crt`:

```bash
sudo openssl verify -CAfile /opt/datapk/ca.crt /opt/datapk/scripts_certs/datapk_script.crt
```

> ✅ Должно вернуть: `OK`

---

## 🗂️ Настройка директорий и прав

```bash
# Права для скриптов
sudo chmod -R 755 /opt/datapk/scripts_certs

# Директории для nginx и Elasticsearch
sudo mkdir -p /opt/datapk/nginx/certs
sudo mkdir -p /var/backups/elasticsearch/{indices,uploads}
sudo chmod -R 777 /var/backups/elasticsearch

# Права для logstash и экспорта
sudo chmod -R 777 /opt/datapk/logstash
sudo chmod -R 777 /opt/datapk/export_data
```

---

## 🔒 Генерация SSL-сертификата для nginx

### Вариант 1: По IP-адресу
```bash
IP="192.168.133.139"  # ← замените на IP вашего сервера

sudo openssl genrsa -out /opt/datapk/nginx/certs/${IP}.key 2048
sudo openssl req -new -x509 \
  -key /opt/datapk/nginx/certs/${IP}.key \
  -out /opt/datapk/nginx/certs/${IP}.cert \
  -days 3650 \
  -subj "/CN=${IP}"
```

### Вариант 2: По hostname (рекомендуется)

1. Задайте переменные:
   ```bash
   HOST_FQDN="datapk.local"
   DATAPK_IP="192.168.133.139"
   ```

2. Создайте конфиг для SAN:
   ```bash
   sudo printf "[dn]\nCN=${HOST_FQDN}\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:${HOST_FQDN}\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth" > /opt/datapk/nginx/certs/config.txt
   ```

3. Сгенерируйте сертификат:
   ```bash
   sudo openssl req -x509 \
     -out /opt/datapk/nginx/certs/${DATAPK_IP}.cert \
     -keyout /opt/datapk/nginx/certs/${DATAPK_IP}.key \
     -days 3650 \
     -newkey rsa:2048 -nodes -sha256 \
     -subj "/CN=${HOST_FQDN}" \
     -extensions EXT \
     -config /opt/datapk/nginx/certs/config.txt
   ```

---

## 📡 Установка SNMP MIB-базы

```bash
# Создание директории
sudo mkdir -p /var/backups/snmp_mibs
sudo chmod -R 755 /var/backups/snmp_mibs

# Распаковка (предварительно скопируйте snmp_mibs.tar.gz в эту папку)
cd /var/backups/snmp_mibs
sudo tar xvzf snmp_mibs.tar.gz
```

---

## ⚙️ Генерация `.env`-файла

1. Сделайте скрипт исполняемым:
   ```bash
   sudo chmod +x /opt/datapk/generate_env_file-1.18.0.1.sh
   ```

2. Запустите его:
   ```bash
   sudo bash /opt/datapk/generate_env_file-1.18.0.1.sh
   ```

3. Проверьте результат:
   ```bash
   ls -la /opt/datapk | grep .env
   sudo nano /opt/datapk/.env  # убедитесь, что все параметры корректны
   ```

---

## 🐳 Загрузка и запуск Docker-образов

1. Скопируйте архив образов в `/opt`:
   ```bash
   sudo cp datapk-v1.18.0.1.tar.gz /opt/
   ```

2. Загрузите образы:
   ```bash
   cd /opt
   sudo docker load -i datapk-v1.18.0.1.tar.gz
   ```

3. Запустите комплекс:
   ```bash
   cd /opt/datapk
   sudo docker-compose up -d
   ```

4. Проверьте статус контейнеров:
   ```bash
   sudo docker ps
   ```

> ✅ Все контейнеры должны быть в статусе **`Up`**.

---

## 🎉 Завершение

> **На этом всё!**  
> DATAPK 1.18 успешно установлен и запущен.

---

> 🌐 **Совет**: Для доступа к веб-интерфейсу откройте в браузере `https://<IP_или_hostname>`.  
> При первом входе может потребоваться принять самоподписанный сертификат.

--- 

Готово! Этот документ можно сразу использовать в GitHub, GitLab или как часть внутренней документации.
