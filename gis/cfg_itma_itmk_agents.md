📝 Автор инструкции:**Грубич В.Е.**  
📅 Дата создания: **февраль 2025**

# 📡 Настройка связки ITM-K и ITM-A

> 📌 **Предполагается, что ITM-A/K уже установлен и настроен**

## 📋 Общее описание

В примере ниже рассмотрим настройки для сбора информации через **сервер агентов ITM-A** с последующей передачей данных в **ITM-K**.

---

## ➕ Добавление "Прокси" в веб-консоли ITM-K

1. В веб-интерфейсе ITM-K перейдите в раздел **«Прокси»** → **«Создать прокси»**.
2. Укажите:
   - **Имя прокси**: должно совпадать с `hostname` сервера, где развёрнут **ITM-A**.
   - **Режим**: по умолчанию — **Пассивный** (ITM-K опрашивает прокси каждые 6 секунд).
   - **IP-адрес**: сервера ITM-A.
   - **Порт**: `10051` (стандартный для Zabbix Proxy).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5114b1tup2.jpg)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/yv3a2hqfgm.jpg)

---

## 🖥️ Добавление узла сети (АРМ/сервер) для мониторинга

При создании нового узла в ITM-K:

- ✅ **Имя узла**: обязательно укажите реальный `hostname` наблюдаемой машины.  
- 🏷️ **Видимое имя**: можно задать понятное человеку (например, «АРМ бухгалтера»).  
- 🌐 **Интерфейс**: IP + порт `10050`.  
- 🔄 **Наблюдение через прокси**: выберите созданный прокси (ITM-A).  
- 📦 **Шаблоны**: выберите в зависимости от ОС (в примере — Linux).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/obs206511g.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/lo6afnnmuu.jpg)

---

## ⚙️ Настройка Zabbix Agent на наблюдаемом АРМе/сервере

Отредактируйте файл конфигурации:  
`/etc/zabbix/zabbix_agent2.conf`

Измените **только следующие параметры**:

```ini
Server=10.195.5.110          # ← IP сервера ITM-A
ServerActive=10.195.5.110    # ← IP сервера ITM-A
ListenPort=10050             # ← стандартный порт агента
Hostname=itm-a               # ← hostname сервера с ITM-A
```

> 💡 Остальные настройки оставьте по умолчанию.

После правки — примените изменения:

```bash
systemctl enable zabbix-agent2.service --now
systemctl restart zabbix-agent2
systemctl status zabbix-agent2
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/b3xa7qraq5.jpg)

---

## ✅ Результат

После выполнения всех шагов:
- Узел появится в веб-консоли **ITM-K**.
- Метрики будут собираться **на ITM-A**, а отображаться — в **ITM-K**.

---

## 🛠️ Возможные ошибки и диагностика

### 1. Проблемы с подключением к БД
Проверьте файлы:
- `/opt/datapkitm/env/.db.env` — корректность IP, логина, пароля БД.
- `/opt/datapkitm/env/.proxy.env` — режим прокси, IP ITM-K, порт.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/a22acsniac.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/s2fvj1edj2.jpg) 

> ⚠️ IP в `.db.env` — это **внутренний IP Docker-контейнера**, а не хоста!

### 2. Ошибки в `pg_hba.conf`

Убедитесь, что в файле разрешены подключения:
```conf
host all all 0.0.0.0/0 md5
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/jjv90o4ic5.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/syx0atqaqk.jpg)

### 3. Диагностика через логи
Выполняйте из директории `/opt/datapkitm`:

```bash
docker-compose logs
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/a1wwxbt97x.jpg)  
*Пример ошибки из-за неправильного `pg_hba.conf`*

### 4. Ошибки в `zabbix_agent2.conf`
Неверный `Hostname` или `Server` — частая причина отсутствия данных.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4ufnir3d20.jpg)


## 🎉 Завершение

> **На этом всё!**  
> Связка ITM-K ↔ ITM-A настроена и готова к работе.
