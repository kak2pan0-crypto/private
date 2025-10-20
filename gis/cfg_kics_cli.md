
📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **июнь 2024**

# ⚙️ Настройка KICS через командную строку  
## (+ блокировка всех USB-накопителей)

> 💡 **Важно**: В графическом интерфейсе KICS можно **только включать/выключать** компоненты защиты.  
> Для **детальной настройки** (например, блокировки USB) требуется работа через **CLI**.

---

## 🧠 Логика настройки

1. 📥 **Выгрузить** текущие настройки задачи в редактируемый файл.  
2. ✏️ **Изменить** файл согласно требованиям.  
3. 📤 **Загрузить** обновлённые настройки обратно в KICS.  
4. ♻️ **Перезапустить** службу для применения изменений.

---

## 🔌 Пример: Блокировка всех USB-накопителей

### 1. Определяем задачу
Нас интересует задача **«Контроль устройств»** с **ID = 15**.

### 2. Выгружаем настройки
```bash
kics-control --get-settings 15 --file /srv/dev_control.ini
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/48pxx79qca002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/4htkqfg91c002.jpg)

> 📚 Документация по параметрам:  
> 🔗 [https://support.kaspersky.com/KICS4Linux/1.3/ru-RU/198078.htm](https://support.kaspersky.com/KICS4Linux/1.3/ru-RU/198078.htm)

---

## 🛡️ Подготовка среды

Перед применением правил **переведём файловый перехватчик в режим блокировки**:

```bash
kics-control --set-app-settings InterceptorProtectionMode=Block
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/fldnsy8xda002.jpg)

---

## ✏️ Редактирование конфигурации

Открываем файл:
```bash
nano /srv/dev_control.ini
```

Находим секцию `[DeviceClass]` и меняем параметр:
```ini
[DeviceClass]
RemovableDrive=Block
```

> ✅ Это **заблокирует все съёмные носители** (флешки, внешние диски и т.д.).

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/eqlnv3706b002.jpg)

---

## 📤 Применение настроек

Загружаем изменённый файл в KICS:
```bash
kics-control --set-settings 15 --file /srv/dev_control.ini
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/espekv3i0n002.jpg)

---

## ♻️ Перезапуск службы

Для применения изменений **обязательно** перезапустите службу:
```bash
systemctl stop kics
systemctl start kics
```

> ⚠️ **Если служба не запускается** — в файле `dev_control.ini` допущена ошибка (например, дублирование параметров).  
> Проверьте синтаксис!

---

## ✅ Проверка работы

- При подключении флешки появится **уведомление о блокировке**.
- Устройство **не отобразится** в системе:
  ```bash
  fdisk -l  # флешка отсутствует в списке
  ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/tmiy0rnyt8002.jpg)

---

## 💾 Советы по управлению

- **Всегда делайте бэкап** перед изменением:
  ```bash
  cp /srv/dev_control.ini /srv/dev_control.ini.bak
  ```
- Чтобы **разблокировать USB**, измените параметр обратно на `Allow` или `DependsOnBus`.

---

## 📋 Список задач для настройки

Вы можете настраивать любую из встроенных задач KICS. Полный список с ID и описанием:  
🔗 [https://support.kaspersky.com/KICS4Linux/1.3/ru-RU/8540.htm](https://support.kaspersky.com/KICS4Linux/1.3/ru-RU/8540.htm)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/avbps8qj88002.jpg)  
![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/tnrclw4d1b002.jpg)

---

## 🎉 Завершение

> **На этом всё!**  
> USB-накопители успешно заблокированы через CLI.

---

> 🦇 **Совет**: Храните все изменённые `.ini`-файлы в системе контроля версий — это упростит откат и аудит настроек.
