# 💾 Установка Cyber Backup на Astra Linux

📝 **Автор**: Грубич В.Е.  
📅 **Дата создания**: ноябрь 2024

---

## 📁 Источники

- **Локальный диск**:  
  `P:\Филиал г.Н.Уренгой\12.ОПНР\Обучение\Вендоры\Acronis`
- **Официальный сайт**:  
  🔗 [https://cyberprotect.ru/updates/updates-backup/](https://cyberprotect.ru/updates/updates-backup/)

> 💡 В примере используется:  
> `CyberBackup_16.5_64-bit_29856.x86_64_Astra`

---

## 🛠️ Шаг 1: Установка зависимостей

Если **bond-интерфейс не требуется**, установите пакеты **без `ifenslave`**:

```bash
sudo apt-get install make gcc rpm dialog flex bison
```

> ⚠️ Если нужен бондинг — добавьте `ifenslave`.

---

## ▶️ Шаг 2: Запуск установки

1. Сделайте установочный файл исполняемым:
   ```bash
   chmod +x CyberBackup_16.5_64-bit_29856.x86_64
   ```

2. Запустите установку:
   ```bash
   ./CyberBackup_16.5_64-bit_29856.x86_64
   ```

3. Обновите образы initramfs:
   ```bash
   sudo update-initramfs -u -k all
   ```

4. **Перезагрузите сервер**, затем снова запустите установщик.

5. При установке выберите компоненты:
   - ✅ Сервер
   - ✅ Агент
   - ✅ Диск восстановления
   - ✅ Хранилище

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/am4iov7oic.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ei71wctgjb.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/robjngdp8q.jpg)

---

## 🌐 Шаг 3: Порты и проверка

- **Веб-интерфейс**: `http://<IP>:9877`
- **Внутренний TCP-порт**: `7780`

Успешная установка подтверждается сообщением:

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/ef6hdtcddi.jpg)

---

## 🔐 Шаг 4: Настройка доступа

### 1. Установите пароль для `root`:
```bash
sudo passwd root
```

### 2. Добавьте пользователя (например, `gis`) в конфигурационные файлы:
```bash
echo "gis" | sudo tee -a /etc/security/acronisagent.conf /etc/security/acronis_storage.conf
```

### 3. Назначьте оболочку и группы:
```bash
sudo usermod -s /bin/sh gis
sudo usermod -aG asn_remote_users gis
```

### 4. Перезапустите службы:
```bash
sudo systemctl restart acronis_asm
sudo systemctl restart acronis_mms
sudo systemctl restart acronis_storage
```

---

## 🖥️ Шаг 5: Первый вход в веб-интерфейс

1. Откройте в браузере:  
   `http://<IP_сервера>:9877`
2. **Войдите под `root`** (пароль задан на шаге 4.1).
3. Перейдите: **Настройки → Учётные записи**
4. Добавьте пользователя `gis` с ролью **Администратор**.
5. Активируйте лицензию: **Настройка → Лицензия**

---

## 🗃️ Шаг 6: Настройка хранилища

1. Перейдите: **Хранилище → Узел хранения**
2. Выберите **IP-адрес** вместо имени хоста.
3. Укажите путь к папке (например, `/backup`).
4. При запросе пароля введите **данные root**.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/2025-10-20_16-24-25.png)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/x0xoltdvbu.jpg)

> 💡 **Совет**: заранее создайте подпапки в `/backup` для каждого сервера:  
> ```bash
> mkdir -p /backup/{ACS,CI,ITM,SIEM}
> ```
> Это упростит поиск бэкапов без веб-интерфейса.

---

## 📅 Шаг 7: Создание плана резервного копирования

> ⚠️ **Важно**: создавайте план **под учётной записью `gis`**, а не `root`!

1. Выйдите из веб-интерфейса.
2. Войдите заново под `gis`.
3. Перейдите: **Планы → Создать план**
4. Укажите:
   - Источник: нужные серверы/папки
   - Хранилище: выберите узел хранения
   - Расписание: по умолчанию (ежедневно + еженедельно)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/815tt7qrl8.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/629lr5dekj.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/e3416t6lub.jpg)

> 🔒 При указании пути хранения система запросит **пароль root** — введите его.

---

## ✅ Шаг 8: Применение плана

1. Перейдите: **Устройства → Все устройства**
2. Выберите нужное устройство.
3. Нажмите **Применить план защиты** и выберите созданный план.

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/5uj057e5f9.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/tq8328g9rj.jpg)
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/vckevtdvjo.jpg)

---

## 🎉 Завершение

> **На этом установка и настройка Cyber Backup завершены!** <br>
> 💾 **Совет**: Регулярно проверяйте целостность бэкапов и свободное место в хранилище.
