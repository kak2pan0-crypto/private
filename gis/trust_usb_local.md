📝 Автор инструкции: **Грубич В.Е.**  
📅 Дата создания: **июнь 2024**

# 🛡️ Создание доверительных (белого списка) USB-накопителей в KICS

> ✅ **Цель**: Разрешить подключение **только зарегистрированных USB-устройств** и **блокировать все остальные**.

---

## 🔍 Шаг 1: Получение идентификаторов устройств

Подключите доверяемую флешку и выполните команду:

```bash
kics-control --get-device-list
```

> 💡 Устройство может отображаться **несколько раз**, если:
> - На нём несколько разделов  
> - Это загрузочная флешка (один раздел скрытый)

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/jy8jjguq5d002.jpg)

---

## ⚙️ Шаг 2: Подготовка среды

Убедитесь, что режим перехватчика установлен в **`Block`** (иначе правила не сработают):

```bash
kics-control --set-app-settings InterceptorProtectionMode=Block
```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/dikb1o6xmx002.jpg)

---

## 📥 Шаг 3: Выгрузка текущих настроек

Создайте файл с настройками задачи **«Контроль устройств»** (ID = 15):

```bash
kics-control --get-settings 15 --file /srv/dev_control_1.ini
```

---

## ✏️ Шаг 4: Редактирование конфигурации

1. Откройте файл:
   ```bash
   nano /srv/dev_control_1.ini
   ```

2. В секции `[DeviceClass]` измените параметр:
   ```ini
   RemovableDrive=ByRule
   ```

3. **После секции `[DeviceClass]`** добавьте новую секцию для доверенного устройства:

   ```ini
   [TrustedDevices.item_0001]
   ID=USBSTOR\DISK&VEN_KINGSTON&PROD_DT_101_G2&REV_1.00\001CC0EC3239F9A18944098B&0
   Comment=USB_Sysadmins
   ```

   > 📌 Если у устройства **несколько разделов** — добавьте **каждый ID** как отдельную секцию:
   > ```ini
   > [TrustedDevices.item_0001]
   > ID=...&0
   > Comment=USB_Sysadmins_Part1
   >
   > [TrustedDevices.item_0002]
   > ID=...&1
   > Comment=USB_Sysadmins_Part2
   > ```

![screen](https://raw.githubusercontent.com/kak2pan0-crypto/private/main/gis/images/g8bi1dwhf7002.jpg)

---

## 📤 Шаг 5: Применение настроек

Загрузите обновлённый файл в KICS:

```bash
kics-control --set-settings 15 --file /srv/dev_control_1.ini
```

> ✅ Теперь **все USB-накопители будут заблокированы**, кроме добавленных в белый список.

---

## ♻️ Шаг 6: Перезапуск службы (если требуется)

Если изменения не применяются мгновенно:

```bash
systemctl restart kics
```

---

## 🎉 Завершение

> **На этом всё!**  
> Только доверенные USB-устройства могут подключаться к системе.
> 🐱 **Совет**: Всегда делайте бэкап `.ini`-файлов перед редактированием — это упростит откат при ошибках.
